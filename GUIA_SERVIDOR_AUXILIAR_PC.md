# Guía: servidor auxiliar para Lebeche (PC i3-3240T · 16 GB · 100 Mbps)

> Fecha: 15-11-2026 · Complemento del NAS Synology DS223.
> Objetivo: ejecutar los servicios pesados que el NAS (2 GB de RAM) no puede mover:
> **Office (OnlyOffice/Collabora)**, **Talk HPB (señalización)** y Redis. Los datos siguen
> en el NAS; el PC solo ejecuta servicios.

## 1. Requisitos

| Componente | Mínimo | Recomendado |
|---|---|---|
| Disco | **SSD SATA 120 GB** | 240 GB (más margen) |
| Red | Ethernet 100 Mbps (la del equipo) | — |
| Instalación | Pendrive USB 8 GB | — |
| Sistema | Ubuntu Server 24.04 LTS | Debian 12 también vale |
| Monitor + teclado | Solo para instalar | Después se gestiona por SSH |

- **No hace falta disco grande**: el sistema y los contenedores ocupan 25–40 GB; los datos
  viven en el NAS. Evita arrancar desde USB para un servidor 24/7 (se desgastan).
- Consumo: ~25–35 W en reposo (i3-3240T, 35 W TDP).
- La caja grande se puede ocultar (armario ventilado) o cambiar por una mini-torre mATX (~20-40 €).

## 2. Instalar Ubuntu Server 24.04 LTS

1. Descarga la ISO: https://ubuntu.com/download/server
2. Graba la ISO en el pendrive con **Rufus** (Windows) o balenaEtcher.
3. Arranca el PC desde el USB (tecla F2/F11/F12/DEL según placa, elige el USB).
4. Instalación guiada:
   - Idioma y teclado: Español.
   - Red: DHCP (ya pondremos IP fija).
   - Disco: **"Use an entire disk"** sobre el SSD (borra todo el disco, es lo correcto).
   - Perfil: nombre `lebeche`, servidor `lebeche-pc`, usuario **`lebeche`**,
     contraseña la estándar de la asociación (o una nueva, apúntala en `CREDENCIALES.md`).
   - **Marca "Install OpenSSH server"** ✅ (imprescindible).
   - Sin snaps adicionales (puedes dejar "Featured server snaps" vacío).
5. Reinicia y quita el pendrive. Ya no necesitas monitor: todo por SSH.

## 3. Primeros pasos por SSH (desde el PC de Pelotxo)

```bash
# averiguar la IP del equipo (en el router o con "ip a" en el propio PC)
ssh lebeche@192.168.50.XX

# actualizar
sudo apt update && sudo apt full-upgrade -y
```

### IP fija (recomendado: reserva DHCP en el router)

En el router, reserva la IP **192.168.50.20** para la MAC del PC (suele ser lo más simple).
Alternativa con netplan (`/etc/netplan/50-cloud-init.yaml`):

```yaml
network:
  version: 2
  ethernets:
    eno1:                      # comprueba el nombre con "ip a"
      dhcp4: no
      addresses: [192.168.50.20/24]
      routes:
        - to: default
          via: 192.168.50.1
      nameservers:
        addresses: [1.1.1.1, 8.8.8.8]
```

```bash
sudo netplan apply
ip a   # verificar 192.168.50.20
```

### Instalar Docker (oficial) + Compose

```bash
sudo apt install -y ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo usermod -aG docker lebeche
newgrp docker   # o cierra y abre la sesión SSH
docker --version && docker compose version
```

### Cortafuegos (opcional pero recomendable)

```bash
sudo apt install -y ufw
sudo ufw allow OpenSSH
sudo ufw default deny incoming
sudo ufw enable
```

> Los servicios se publican solo en la IP interna (192.168.50.20); el acceso desde internet
> lo da el proxy inverso del NAS. No abrir puertos del router hacia el PC.
## 4. Desplegar los servicios (Docker Compose)

Crea la estructura en el PC:

```bash
mkdir -p ~/lebeche/{onlyoffice,collabora,talk}
```

### 4.1 Office — opción A: OnlyOffice Document Server (recomendada con 16 GB)

`~/lebeche/onlyoffice/compose.yaml`:

```yaml
services:
  onlyoffice:
    image: onlyoffice/documentserver:latest
    container_name: onlyoffice
    restart: unless-stopped
    ports:
      - "192.168.50.20:9980:80"      # solo accesible desde la LAN
    environment:
      - JWT_ENABLED=true
      - JWT_SECRET=CAMBIA_ESTE_SECRETO_LARGO
    volumes:
      - ./data:/var/www/onlyoffice/Data
```

```bash
cd ~/lebeche/onlyoffice && docker compose up -d
curl http://192.168.50.20:9980/healthcheck   # esperar "true" (tarda 1-2 min la primera vez)
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
      - "192.168.50.20:9980:80"
    environment:
      - aliasgroup1=https://nube.corrientelebeche.es
      - extra_params=--o:ssl.enable=false --o:ssl.termination=true --o:num_prespawn_children=2
```

```bash
cd ~/lebeche/collabora && docker compose up -d
curl http://192.168.50.20:9980/hosting/discovery   # devuelve XML si está listo
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
      - "192.168.50.20:8081:8080"
    environment:
      - NATS_URL=nats://talk-nats:4222
      - SIGNALING_BACKEND=nats://talk-nats:4222
      - SIGNALING_LISTEN=0.0.0.0:8080
```

```bash
cd ~/lebeche/talk && docker compose up -d
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
| `office.corrientelebeche.es` : 443 | `http://192.168.50.20:9980` | CF Origin (comodín) |
| `signaling.corrientelebeche.es` : 443 | `http://192.168.50.20:8081` | CF Origin (comodín) |

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

```bash
# en el PC
sudo apt update && sudo apt full-upgrade -y
cd ~/lebeche/*/ && docker compose pull && docker compose up -d   # por servicio
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
| No llega al PC desde el NAS | IP o firewall | `ping 192.168.50.20`, `sudo ufw status` |
| 100 Mbps saturados | transferencias internas grandes | normal para office/talk; los archivos grandes van por SMB del NAS |

## 8. Checklist rápido

- [ ] Ubuntu Server instalado + OpenSSH + IP fija 192.168.50.20.
- [ ] Docker + Compose instalados; usuario `lebeche` en el grupo docker.
- [ ] OnlyOffice **o** Collabora desplegado y con healthcheck OK.
- [ ] Talk HPB + NATS desplegados; secreto generado y guardado en CREDENCIALES.md.
- [ ] Proxy inverso DSM: `office` (y `signaling`) → IP del PC.
- [ ] CNAME en Cloudflare: `office` (y `signaling`) → Proxied + Bypass.
- [ ] Office habilitado en Nextcloud y probado con un .docx.
- [ ] `talk:signaling:add` hecho y verificado.