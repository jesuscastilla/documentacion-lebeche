# Guía: servidor auxiliar para Lebeche (PC i3-3240T · 16 GB · 100 Mbps)

> Fecha: 15-11-2026 · Actualizado 16-11-2026 (instalación con **ZimaOS**).
> Complemento del NAS Synology DS223.
> Objetivo: ejecutar los servicios pesados que el NAS (2 GB de RAM) no puede mover:
> **Office (OnlyOffice/Collabora)** y **Talk HPB (señalización + NATS)**. Redis ya corre
> en el NAS. Los datos siguen en el NAS; el PC solo ejecuta servicios.

## 1. Requisitos

| Componente | Mínimo | Recomendado |
|---|---|---|
| Disco | **SSD SATA 120 GB** | 240 GB (más margen) |
| Red | Ethernet 100 Mbps (la del equipo) | — |
| Instalación | Pendrive USB 8 GB | — |
| Sistema | **ZimaOS** (x86_64, basada en Debian) | — |
| Monitor + teclado | Solo para instalar | Después se gestiona por SSH/UI |

- **No hace falta disco grande**: el sistema y los contenedores ocupan 25–40 GB; los datos
  viven en el NAS. Evita arrancar desde USB para un servidor 24/7 (se desgastan).
- Consumo: ~25–35 W en reposo (i3-3240T, 35 W TDP).
- La caja grande se puede ocultar (armario ventilado) o cambiar por una mini-torre mATX (~20-40 €).

## 2. Instalar ZimaOS

1. Descarga la ISO x86_64 desde https://github.com/IceWhaleTech/ZimaOS-Releases/releases
   (el fichero `ZimaOS-x86_64-x.y.z.iso` del último release).
2. Graba la ISO en el pendrive con **Rufus** (Windows). Si pregunta el modo, elige
   "Write in DD Image mode".
3. Arranca el PC desde el USB (tecla F2/F11/F12/DEL según placa, elige el USB).
4. El instalador de ZimaOS instala automáticamente sobre el disco elegido (el SSD).
   Tarda unos minutos y reinicia solo. Quita el pendrive en el reinicio.
5. Configuración inicial (desde otro equipo de la LAN): abre `http://<IP-del-PC>` (puerto 80)
   y completa el asistente: crea la cuenta de administración con la contraseña estándar de la
   asociación (**apúntala en `CREDENCIALES.md`**) y zona horaria Europe/Madrid.
6. En **Settings (Ajustes) → General**: activa **SSH** y renombra el equipo a `lebeche-pc`.

> La IP del equipo se ve en el propio instalador, en el router o con `ip a`.
> Para el resto de la guía usaremos la IP fija **192.168.50.95**.

## 3. Primeros pasos por SSH (desde el PC de Pelotxo)

```bash
# averiguar la IP (instalador, router o "ip a")
ssh lebeche@192.168.50.95    # contraseña estándar de la asociación
```

> Nota: el usuario del asistente es `lebeche` (grupo `docker` incluido, no hace falta `sudo`
> para docker). **`root` no puede entrar por SSH** (ZimaOS lo bloquea). Los cambios de sistema
> se hacen con `sudo` (misma contraseña).

### Docker Compose (binario suelto)

ZimaOS trae el motor Docker pero **no** el plugin `compose` del CLI (y el sistema de archivos
raíz es de solo lectura). Se usa el binario suelto (persiste solo hasta el próximo reinicio):

```bash
curl -fsSL https://github.com/docker/compose/releases/latest/download/docker-compose-linux-x86_64 -o /tmp/dc \
  && chmod +x /tmp/dc && /tmp/dc version
# usar como: /tmp/dc -f <compose.yaml> up -d
```

## 3.1 Notas reales de la instalación (ZimaOS, 16-11-2026)

- **IP**: `192.168.50.95/24` fija. Ojo: la `.20` la ocupa el PC Windows de Pelotxo, por eso
  se eligió `.95` (junto al NAS .93/.94).
- **`/DATA` está protegido**: desde SSH no se puede `chown`/`chmod`/escribir según qué rutas
  (guard de ZimaOS). Para datos persistentes de contenedores usa **volúmenes nombrados de
  Docker** (los gestiona el daemon, no la shell). Los ficheros `compose.yaml` van en `/tmp`.
- **UI de ZimaOS**: `http://192.168.50.95` (puerto 80, solo LAN). SSH se activa en
  Settings → General.
- **Aviso cosmético** de Docker (`/DATA/.docker/config.json: permission denied`): inofensivo.
- Hardware visto en vivo: i3-3240T (4 núcleos), 16 GB RAM (13 libres), SSD 240 GB con 217 GB
  libres en `/DATA`, hostname `lebeche-pc`.

### IP fija (recomendado: reserva DHCP en el router)

En el router, reserva la IP **192.168.50.95** para la MAC del PC (lo más simple).
Alternativa en la propia UI de ZimaOS: **Settings → Network → Editar → Static IP**
(IP 192.168.50.95/24, gateway 192.168.50.1, DNS 1.1.1.1).

### Actualización del sistema

- UI: **Settings → Update** (se pueden activar actualizaciones automáticas; al actualizar,
  ZimaOS reinicia y los contenedores vuelven solos con `restart: unless-stopped`).
- Por SSH: `apt update && apt full-upgrade -y` (es Debian por debajo).

### Cortafuegos (opcional pero recomendable)

```bash
apt install -y ufw
ufw allow OpenSSH
ufw default deny incoming
ufw enable
```

> ⚠️ La web de ZimaOS (puerto 80) queda **solo en la LAN**: no la publiques ni abras puertos
> del router hacia el PC. El acceso desde internet lo da el proxy inverso del NAS, igual que
> con `nube`. Los servicios solo se publican en la IP interna (192.168.50.95).
## 4. Desplegar los servicios (Docker Compose)

Crea la estructura en el PC:

```bash
mkdir -p /tmp/lebeche/{onlyoffice,collabora,talk}
```

> Los `compose.yaml` se ejecutan con `/tmp/dc -f ...`. También puedes crear los contenedores
> desde la UI de ZimaOS (Apps → “+” → Custom / importar compose).
> Tras un reinicio del PC, vuelve a descargar `/tmp/dc` (ver §3) y recrea los compose en `/tmp`
> (los datos de OnlyOffice viven en un volumen nombrado, no se pierden).

### 4.1 Office — opción A: OnlyOffice Document Server (recomendada con 16 GB)

`~/lebeche/onlyoffice/compose.yaml`:

```yaml
services:
  onlyoffice:
    image: onlyoffice/documentserver:latest
    container_name: onlyoffice
    restart: unless-stopped
    ports:
      - "192.168.50.95:9980:80"      # solo accesible desde la LAN
    environment:
      - JWT_ENABLED=true
      - JWT_SECRET=CAMBIA_ESTE_SECRETO_LARGO
    volumes:
      - onlyoffice_data:/var/www/onlyoffice/Data
volumes:
  onlyoffice_data:
```

```bash
cd /tmp/lebeche/onlyoffice && /tmp/dc up -d
curl http://192.168.50.95:9980/healthcheck   # esperar "true" (tarda 1-2 min la primera vez)
```

### 4.2 Office — opción B: Collabora CODE (más ligera)

`~/lebeche/collabora/compose.yaml`:

```yaml
services:
  collabora:
    image: collabora/code:26.04.6.1.1
    container_name: collabora
    restart: unless-stopped
    cap_add: [MKNOD]
    ports:
      - "192.168.50.95:9980:80"
    environment:
      - aliasgroup1=https://nube.corrientelebeche.es
      - extra_params=--o:ssl.enable=false --o:ssl.termination=true --o:num_prespawn_children=2
```

```bash
cd /tmp/lebeche/collabora && /tmp/dc up -d
curl http://192.168.50.95:9980/hosting/discovery   # devuelve XML si está listo
```

> Elegir A o B (no las dos a la vez: comparten el puerto 9980). OnlyOffice soporta más
> formatos y va sobrada con 16 GB; Collabora consume menos.

### 4.3 Talk HPB (señalización para grupos) + NATS

`~/lebeche/talk/compose.yaml`:

```yaml
services:
  nats:
    image: nats:latest
    container_name: talk-nats
    restart: unless-stopped
  signaling:
    image: strukturag/nextcloud-spreed-signaling:latest
    container_name: talk-hpb
    restart: unless-stopped
    depends_on: [nats]
    ports:
      - "192.168.50.95:8081:8080"
    environment:
      - NATS_URL=nats://talk-nats:4222
      - SIGNALING_BACKEND=nats://talk-nats:4222
      - SIGNALING_LISTEN=0.0.0.0:8080
```

```bash
cd /tmp/lebeche/talk && /tmp/dc up -d
# generar el secreto de firma (se usa al conectar Talk):
docker exec -it talk-hpb spreed-signaling -signaling-secret
# (si el binario no está en ese path, ver "docker exec talk-hpb ls /usr/bin | grep spreed")
```

> El TURN (coturn) se queda en el NAS: ya está configurado y verificado. El HPB solo se
> ocupa de la señalización de los grupos.

## 5. Publicar y conectar con el NAS

### 5.1 Proxy inverso en DSM

Panel de control → Portal de inicio de sesión → Avanzado → Proxy inverso:

| Origen | Destino | Certificado |
|---|---|---|
| `office.corrientelebeche.es` : 443 | `http://192.168.50.95:9980` | CF Origin (comodín) |
| `signaling.corrientelebeche.es` : 443 | `http://192.168.50.95:8081` | CF Origin (comodín) |

(Recordatorio: en DSM 7.4 el certificado se asigna copiando los .pem en
`/usr/syno/etc/www/certificate/ReverseProxy_<uuid>/` y `nginx -s reload`; ver GUIA_DESPLIEGUE de lebeche-nextcloud.)

### 5.2 DNS en Cloudflare

| Tipo | Nombre | Contenido | Proxy |
|---|---|---|---|
| CNAME | `office` | `pelotxo.synology.me` | 🟠 Proxied |
| CNAME | `signaling` | `pelotxo.synology.me` | 🟠 Proxied |

- Cache Rule: **Bypass** para ambos subdominios.

### 5.3 Activar el Office en Nextcloud

**Con OnlyOffice (opción A):**
```bash
sudo docker exec -u www-data nextcloud php occ app:enable onlyoffice
# Luego en la web: Ajustes de administración → OnlyOffice →
#   Dirección del Document Server: https://office.corrientelebeche.es
#   Secreto JWT: el mismo que pusiste en el compose
```

**Con Collabora (opción B):**
```bash
sudo docker exec -u www-data nextcloud php occ app:enable richdocuments
sudo docker exec -u www-data nextcloud php occ config:app:set richdocuments wopi_url --value=https://office.corrientelebeche.es
```

### 5.4 Conectar el HPB a Talk

```bash
# en el NAS, con el secreto que generó el contenedor:
sudo docker exec -u www-data nextcloud php occ talk:signaling:add \
  https://signaling.corrientelebeche.es/ <SECRETO_HPB> --verify
sudo docker exec -u www-data nextcloud php occ talk:signaling:list
```

### 5.5 Verificación final

- Abrir un .docx/.xlsx en Nextcloud → se abre el editor (A o B).
- `curl -s https://office.corrientelebeche.es/healthcheck` (OnlyOffice) o `/hosting/discovery` (Collabora).
- Crear una conversación de Talk con 5+ personas y comprobar que la señalización usa el HPB
  (en el PC: `docker logs -f talk-hpb`).

## 6. Mantenimiento

- **Sistema (ZimaOS)**: Settings → Update, o actualizaciones automáticas.
- **Contenedores** (por SSH como `lebeche`):

```bash
cd /tmp/lebeche/<servicio>/ && /tmp/dc pull && /tmp/dc up -d
```

- Actualizar los contenedores **una vez al mes** (OnlyOffice y el HPB se actualizan a menudo).
- **Copias de seguridad:** no hay datos en el PC (todo vive en el NAS). Guarda los
  `compose.yaml` y secretos en `G:\GITHUB\documentacion-lebeche\` (fuera de Git los secretos;
  apúntalos en `CREDENCIALES.md`).
- Vigilar: `htop`, `docker stats`, `df -h` (el SSD de 120 GB debería quedar al 30-40 %).

## 7. Solución de problemas

| Síntoma | Causa probable | Acción |
|---|---|---|
| Office no abre | App deshabilitada / URL o JWT mal | revisar 5.3, `curl` al healthcheck, logs `docker logs` |
| Office lento al inicio | primera carga de OnlyOffice | normal; esperar el healthcheck |
| Talk sigue usando el NAS | signaling no verificado | revisar `talk:signaling:list` y `--verify` |
| 502 desde internet | proxy DSM o DNS | comprobar CNAME `office`/`signaling` y la regla de proxy |
| No llega al PC desde el NAS | IP o firewall | `ping 192.168.50.95`, `ufw status` |
| 100 Mbps saturados | transferencias internas grandes | normal para office/talk; los archivos grandes van por SMB del NAS |

## 8. Checklist rápido

- [ ] ZimaOS instalada en el SSD + SSH activado + IP fija 192.168.50.95.
- [ ] Docker OK sin sudo (usuario `lebeche`) + binario `/tmp/dc` (compose) descargado.
- [ ] OnlyOffice **o** Collabora desplegado y con healthcheck OK.
- [ ] Talk HPB + NATS desplegados; secreto generado y guardado en CREDENCIALES.md.
- [ ] Proxy inverso DSM: `office` (y `signaling`) → IP del PC.
- [ ] CNAME en Cloudflare: `office` (y `signaling`) → Proxied + Bypass.
- [ ] Office habilitado en Nextcloud y probado con un .docx.
- [ ] `talk:signaling:add` hecho y verificado.