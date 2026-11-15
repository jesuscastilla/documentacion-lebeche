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
raíz es de solo lectura). El binario se instala una vez en una partición persistente:

```bash
sudo mkdir -p /mnt/overlay/lebeche-tools
sudo curl -fsSL https://github.com/docker/compose/releases/latest/download/docker-compose-linux-x86_64 -o /mnt/overlay/lebeche-tools/dc
sudo chmod +x /mnt/overlay/lebeche-tools/dc
/mnt/overlay/lebeche-tools/dc version
# usar como: /mnt/overlay/lebeche-tools/dc -f <compose.yaml> up -d
```

> `/mnt/overlay` (sda7) es la partición persistente de ZimaOS para cambios de sistema; ahí
> sí se puede ejecutar. `/tmp` es tmpfs (se borra al reiniciar) y `/DATA` está protegido.

## 3.1 Notas reales de la instalación (ZimaOS, 16-11-2026)

- **IP**: `192.168.50.95/24` fija. Ojo: la `.20` la ocupa el PC Windows de Pelotxo, por eso
  se eligió `.95` (junto al NAS .93/.94).
- **`/DATA` está protegido**: desde SSH no se puede `chown`/`chmod`/escribir según qué rutas
  (guard de ZimaOS). Para datos persistentes de contenedores usa **volúmenes nombrados de
  Docker** (los gestiona el daemon, no la shell).
- **Persistencia tras reinicios** (verificado 16-11-2026): `/var/lib/docker` está en el disco
  (sda8) → contenedores y volúmenes sobreviven; docker arranca en el boot y levanta los
  contenedores solos (`restart: unless-stopped`). El compose y los `compose.yaml` viven en
  `/mnt/overlay/lebeche-tools/` (persistente). Tras un reinicio **no hay que hacer nada**;
  para actualizar/recrear: `/mnt/overlay/lebeche-tools/lebeche-up.sh`.
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
mkdir -p /mnt/overlay/lebeche-tools/compose/{onlyoffice,talk}
```

> Los `compose.yaml` se ejecutan con `/mnt/overlay/lebeche-tools/dc -f ...` (persistente).
> Atajo para levantar todo: `/mnt/overlay/lebeche-tools/lebeche-up.sh`.
> También puedes crear los contenedores desde la UI de ZimaOS (Apps → “+” → Custom).

### 4.1 Office — opción A: OnlyOffice Document Server (recomendada con 16 GB)

`/mnt/overlay/lebeche-tools/compose/onlyoffice/compose.yaml`:

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
cd /mnt/overlay/lebeche-tools/compose/onlyoffice && /mnt/overlay/lebeche-tools/dc up -d
curl http://192.168.50.95:9980/healthcheck   # esperar "true" (tarda 1-2 min la primera vez)
```

### 4.2 Office — opción B: Collabora CODE (más ligera)

`/mnt/overlay/lebeche-tools/compose/collabora/compose.yaml`:

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
cd /mnt/overlay/lebeche-tools/compose/collabora && /mnt/overlay/lebeche-tools/dc up -d
curl http://192.168.50.95:9980/hosting/discovery   # devuelve XML si está listo
```

> Elegir A o B (no las dos a la vez: comparten el puerto 9980). OnlyOffice soporta más
> formatos y va sobrada con 16 GB; Collabora consume menos.

### 4.3 Talk HPB (señalización) — sin NATS (desplegado 16-11-2026)

Un solo servidor no necesita NATS (es solo para clustering). Backend HTTP directo contra Nextcloud.

`/mnt/overlay/lebeche-tools/compose/talk/compose.yaml`:

```yaml
services:
  signaling:
    image: strukturag/nextcloud-spreed-signaling:latest
    container_name: talk-hpb
    restart: unless-stopped
    ports:
      - "192.168.50.95:8081:8080"
    environment:
      - HTTP_LISTEN=0.0.0.0:8080
      - HASH_KEY=<64 hex>          # 32 o 64 bytes
      - BLOCK_KEY=<32 hex>         # 16, 24 o 32 bytes (¡no 64!)
      - BACKENDS=backend1
      - BACKEND_BACKEND1_URLS=https://nube.corrientelebeche.es
      - BACKEND_BACKEND1_SHARED_SECRET=<secreto para talk:signaling:add>
```

```bash
cd /mnt/overlay/lebeche-tools/compose/talk && /mnt/overlay/lebeche-tools/dc up -d
docker logs talk-hpb   # debe acabar con "Listening on 0.0.0.0:8080"
```

- Los secretos (HASH_KEY, BLOCK_KEY, SHARED_SECRET) se generan **una vez** y viven en las
  variables de entorno: persisten entre reinicios (no usar `-signaling-secret`, que cambia
  en cada arranque).
- El TURN (coturn) se queda en el NAS: ya está configurado y verificado. El HPB solo se
  ocupa de la señalización de los grupos.

## 5. Publicar y conectar con el NAS

### 5.1 Proxy inverso en DSM

Panel de control → Portal de inicio de sesión → Avanzado → Proxy inverso:

| Origen | Destino | Certificado |
|---|---|---|
| `office.corrientelebeche.es` : 443 | `http://192.168.50.95:9980` | origen `pelotxo.synology.me` |
| `signaling.corrientelebeche.es` : 443 | `http://192.168.50.95:8081` | origen `pelotxo.synology.me` |

**Notas reales (DSM 7.4, aplicado 16-11-2026):**

- Las reglas se renderizan en `/usr/local/etc/nginx/sites-available/<uuid>.w3conf`
  (enlazado como `sites-enabled/server.ReverseProxy.conf`). Editar
  `/usr/syno/etc/www/ReverseProxy.json` **no basta** con `nginx -s reload`; los bloques
  `server{}` se añadieron directamente al w3conf y se recargó. (Si se toca el proxy desde
  la UI de DSM, se regenera el w3conf y se perderían los cambios a mano.)
- El certificado de origen es el de `pelotxo.synology.me` (Cloudflare en modo **Full**, no
  valida el nombre del origen); los .pem se copian a
  `/usr/syno/etc/www/certificate/ReverseProxy_<uuid>/` con su `cert.conf`.
- **Ojo con WebSocket**: este nginx de DSM **elimina la cabecera `Upgrade` del cliente**.
  Para el HPB hay que fijarla solo en la ruta `/spreed`:

```nginx
location /spreed {
    proxy_set_header Upgrade "websocket";
    proxy_set_header Connection "Upgrade";
    # resto igual: proxy_pass http://192.168.50.95:8081; timeouts 3600
}
```

- HSTS añadido como `add_header Strict-Transport-Security "max-age=15552000" always;`
  en los bloques `office`, `signaling` y `nube` (aviso de administración resuelto).

### 5.2 DNS en Cloudflare

| Tipo | Nombre | Contenido | Proxy |
|---|---|---|---|
| CNAME | `office` | `pelotxo.synology.me` | 🟠 Proxied |
| CNAME | `signaling` | `pelotxo.synology.me` | 🟠 Proxied |

- Cache Rule: **Bypass** para ambos subdominios.

### 5.3 Activar el Office en Nextcloud (hecho: OnlyOffice)

```bash
sudo docker exec -u www-data nextcloud php occ app:install onlyoffice
sudo docker exec -u www-data nextcloud php occ app:enable onlyoffice
sudo docker exec -u www-data nextcloud php occ config:app:set onlyoffice DocumentServerUrl --value="https://office.corrientelebeche.es"
sudo docker exec -u www-data nextcloud php occ config:app:set onlyoffice jwt_secret --value=<JWT_SECRET del compose>
sudo docker exec -u www-data nextcloud php occ onlyoffice:documentserver --check
# → "Document server https://office.corrientelebeche.es/ version X is successfully connected"
```

Collabora (opción B) quedó descartado en el NAS por rendimiento; si algún día se prefiere
CODE en el PC: `app:enable richdocuments` + `config:app:set richdocuments wopi_url`.

### 5.4 Conectar el HPB a Talk

```bash
# en el NAS, con BACKEND_BACKEND1_SHARED_SECRET del compose del HPB:
sudo docker exec -u www-data nextcloud php occ talk:signaling:add \
  https://signaling.corrientelebeche.es/ <SHARED_SECRET> --verify
sudo docker exec -u www-data nextcloud php occ talk:signaling:list
```

> Requiere el CNAME `signaling` en Cloudflare (`--verify` sale a internet).

### 5.5 Verificación final

- `curl -s https://office.corrientelebeche.es/healthcheck` → `true`.
- `curl -s https://signaling.corrientelebeche.es/api/v1/welcome` → `{"nextcloud-spreed-signaling":"Welcome",...}`.
- WebSocket: `curl -o /dev/null -w "%{http_code}" -H "Connection: Upgrade" -H "Upgrade: websocket" ... https://signaling.corrientelebeche.es/spreed` → `101` (si diera 400/000, revisar las cabeceras fijas de `/spreed` en el w3conf).
- Abrir un .docx/.xlsx en Nextcloud → se abre el editor.
- Conversación de Talk con 5+ personas y comprobar señalización por el HPB
  (en el PC: `docker logs -f talk-hpb`).

## 6. Mantenimiento

- **Sistema (ZimaOS)**: Settings → Update, o actualizaciones automáticas.
- **Contenedores** (por SSH como `lebeche`):

```bash
cd /mnt/overlay/lebeche-tools/compose/<servicio>/ && /mnt/overlay/lebeche-tools/dc pull && /mnt/overlay/lebeche-tools/dc up -d
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
| WebSocket de Talk da 400/000 | nginx de DSM elimina la cabecera `Upgrade` del cliente | cabeceras fijas `Upgrade`/`Connection` solo en `location /spreed` del w3conf |
| No llega al PC desde el NAS | IP o firewall | `ping 192.168.50.95`, `ufw status` |
| 100 Mbps saturados | transferencias internas grandes | normal para office/talk; los archivos grandes van por SMB del NAS |

## 8. Checklist rápido

- [x] ZimaOS instalada en el SSD + SSH activado + IP fija 192.168.50.95.
- [x] Docker OK sin sudo (usuario `lebeche`) + binario `/mnt/overlay/lebeche-tools/dc` (compose) persistente.
- [x] Persistencia garantizada: contenedores autoarrancan tras reinicio (`unless-stopped` + docker en boot); compose + `lebeche-up.sh` en `/mnt/overlay/lebeche-tools/`.
- [x] OnlyOffice desplegado y con healthcheck OK (público: `https://office.corrientelebeche.es`).
- [x] Talk HPB desplegado (sin NATS); HASH_KEY/BLOCK_KEY/SHARED_SECRET en CREDENCIALES.md.
- [x] Proxy inverso DSM: `office` y `signaling` → 192.168.50.95 (w3conf + HSTS + `/spreed` con Upgrade fijo).
- [x] CNAME en Cloudflare: `office` y `signaling` → Proxied (verificado desde internet).
- [x] OnlyOffice habilitado en Nextcloud y verificado (`onlyoffice:documentserver --check`).
- [x] `talk:signaling:add` hecho y verificado (`--verify` OK; WebSocket público 101).