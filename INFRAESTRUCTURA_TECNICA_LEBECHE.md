# Infraestructura técnica de la asociación Lebeche

> **Documento técnico consolidado** · Fecha: 10-11-2026 · Mantenido por Pelotxo.
> Uso interno de la asociación. **Las contraseñas NO están en este documento**:
> viven en `G:\GITHUB\CREDENCIALES.md` (archivo local, fuera de Git).
> La descripción histórica completa está en `G:\GITHUB\CONTEXT.md`.

---

## 1. Resumen ejecutivo

La asociación Lebeche (Salobreña) gestiona su ecosistema digital de forma **autogestionada**:
web propia, biblioteca (Barrioteca), nube privada (Nextcloud), correo y apps móviles,
todo servido desde un **NAS Synology DS223** en casa, expuesto a internet a través de
**Cloudflare** bajo el dominio `corrientelebeche.es`.

| Servicio | URL pública | Dónde vive |
|---|---|---|
| Web oficial Lebeche | `https://www.corrientelebeche.es/lebeche/` | NAS `/volume1/web/lebeche/` (Web Station) |
| Barrioteca (app web / PWA) | `https://www.corrientelebeche.es/barrioteca/` | NAS `/volume1/web/barrioteca/` |
| SLiMS (backend biblioteca) | `https://www.corrientelebeche.es/slims/` (admin `/slims/admin/`) | NAS `/volume1/web/slims/` |
| **Nextcloud (nube)** | `https://nube.corrientelebeche.es` | Docker `nextcloud` (puerto 8087) |
| **Nextcloud Office** | `https://office.corrientelebeche.es` | Docker `collabora` (puerto 9980) |
| Correo de la asociación | `monderas@corrientelebeche.es` | Hostalia (MX) + Nextcloud Mail / apps |
| Newsletter de socias | `https://www.corrientelebeche.es/newsletter/` | NAS `/volume1/web/newsletter/` (envío por CLI) |
| Mondera's App | Google Play | NAS (CalDAV → Nextcloud, correo IMAP, File Station) |
| Barrioteca Android | Google Play | NAS (api-proxy PHP) |
| Barrioteca iOS | PWA | NAS |
| DSM (administración NAS) | `https://pelotxo.synology.me:5001` | NAS |

**Estado global (10-11-2026):** operativo. Nextcloud recién desplegado (nube + ofimática +
calendario + correo); Roundcube retirado; Snapshot Replication desactivado; limpieza de
almacenamiento ejecutada (ver sección 11).

## 2. Dominio y DNS (Cloudflare)

- **Dominio:** `corrientelebeche.es`, registrado en Hostalia, **delegado a Cloudflare**
  (NS de Cloudflare; TTL 240 s en los registros del NAS).
- **Registros actuales (10-11-2026):**

| Tipo | Nombre | Contenido | Proxy | Uso |
|---|---|---|---|---|
| CNAME | `@` | `pelotxo.synology.me` | 🟠 Proxied | Raíz (301 → /lebeche/) |
| CNAME | `www` | `pelotxo.synology.me` | 🟠 Proxied | Canónico |
| CNAME | `nube` | `pelotxo.synology.me` | 🟠 Proxied | Nextcloud |
| CNAME | `office` | `pelotxo.synology.me` | 🟠 Proxied | Collabora (Nextcloud Office) |
| CNAME | `correo` | `pelotxo.synology.me` | 🟠 Proxied | ⚠️ Sin servicio (Roundcube retirado; borrable) |
| MX | `@` (10) | `mx.corrientelebeche.es` | ⚪ | Correo (Hostalia) |
| TXT | `@` | `v=spf1 redirect=spf.dominioabsoluto.net` | ⚪ | SPF |
| A | `mx` / `imap` / `pop3` / `smtp` | `217.116.0.227` / `.237` / `.237` / `.228` | ⚪ | Correo |
| A/AAAA | `pelotxo.synology.me` | DDNS de Synology | — | IP pública del NAS |

- `webmail.corrientelebeche.es` **no existe** (NXDOMAIN); se usaba junto a `correo` para Roundcube.
- **TLS:** Universal SSL de Cloudflare en el edge (Google Trust Services, autorenovable) +
  **Origin Certificate `*.corrientelebeche.es`** instalado en el NAS (válido hasta **12-09-2041**).
  Modo SSL/TLS: **Full (strict)**. Sin DNSSEC.
- **Caché y rendimiento:** assets de la Barrioteca `immutable`; `index.html`, `version.json` y
  `api-proxy.php` `no-store`; miniaturas `max-age=86400`. Cache Rules: **Bypass** para
  `nube.corrientelebeche.es` y `office.corrientelebeche.es`. **NO** usar Rocket Loader,
  Cloudflare Fonts, Polish ni Email Obfuscation. La compresión es automática (zstd en Free).
- **Límite de subida de Cloudflare Free: 100 MB por petición.** Nextcloud lo sortea con
  *chunking* (app de escritorio/móvil); en la web, archivos >100 MB fallarán (413).
- **DDNS:** `pelotxo.synology.me` (cliente DDNS de DSM) apunta a la IP pública de casa.
- Acceso público verificado el 10-11-2026: `www`, `nube` y `office` responden 200 desde internet.

## 3. NAS Synology DS223

- **Modelo:** DS223 (ARM64 `rtd1619b`), **DSM 7.4.1-90080 Update 1**, kernel 5.10.55+.
- **RAM:** 2 GB (no ampliable). Tras desplegar Nextcloud+Collabora: ~1.056 MB usados,
  ~474 MB disponibles + 2 GB de swap. **Es el principal cuello de botella.**
- **Almacenamiento (Btrfs):**

| Volumen | Tamaño | Usado | Libre | Contenido principal |
|---|---|---|---|---|
| volume1 | 885 GB | 726 GB (83 %) | 160 GB | `web` (350 MB), `homes` (165 GB), `video` (546 GB), `music` (6,9 GB), `photo`, `photos`, `Descargas` |
| volume2 | 1,8 TB | 733 GB (42 %) | 1,1 TB | réplicas (`*-1`), `Backup_NAS`, `docker` |

- **Datos de la asociación en `homes/lebeche`:** ACTAS 1,4 GB · Fotos 4 GB (pendientes de
  migrar a Nextcloud).
- **`video` (546 GB):** videoteca personal (Jellyfin) en volume1; **pendiente de mover a
  volume2** sin romper Jellyfin (bind mount `/volume1/video` → `/volume2/video`).
- Tras vaciar las papeleras (160 GB + 161 GB de réplica) el espacio solo se libera al
  borrar las **últimas snapshots** (queda 1 por carpeta; pendiente del usuario).
- **Red:** `eth0` 1 GbE = `192.168.50.93` · `eth1` USB 2,5 GbE = `192.168.50.94` (ruta por
  defecto). Sin Link Aggregation (los USB no entran en Bond). **Failover:** si `.94` cae,
  reapuntar el reenvío de puertos del router de `.94` a `.93`.
- **Acceso:** SSH puerto 22 (clave `~/.ssh/cline_nas_ed25519`), SMB `\\192.168.50.94\`,
  File Station, DSM `https://pelotxo.synology.me:5001`.
- **Puertos escuchando (relevantes):** 22 (SSH), 80/443 (Nginx/Web Station), 445 (SMB),
  5000/5001 (DSM), 3307 (MariaDB), 5432 (PostgreSQL interno), 6379 (Redis interno),
  6690 (Synology Drive), 8085 (Web Lebeche), 8087 (Nextcloud), 8090 (Roundcube, parado),
  8096 (Jellyfin), 9980 (Collabora).

## 4. Servicios y aplicaciones en el NAS

### 4.1 Web Station (Nginx + PHP)

- Portales Web Station sirviendo las subrutas: `/lebeche/`, `/barrioteca/`, `/slims/`,
  `/newsletter/`. La raíz `/` redirige 301 a `/lebeche/` (`/volume1/web/index.php`).
- Perfiles PHP: **8.2** (SLiMS, con `exif`/`openssl`/`iconv` añadidos en `user_settings.ini`)
  y **8.3**. Apache 2.4 presente (prefork limitado por la optimización de RAM).
- `user.conf` de Nginx lo gobierna la **tarea programada `NoCacheBarrioteca`** (bloque de
  caché para `/barrioteca/assets/`) + una tarea de arranque que lo reaplica; **no editarlo a mano**.
- Reglas de denegación de `user.conf` (bloquean `.git`, `.env`, `.bak` y extensiones
  sensibles): **no eliminarlas**.
- Peculiaridades: Nginx no pasa `$_GET` con URL rewriting (se parsea `QUERY_STRING` a mano en
  PHP) y a veces no pasa el body POST (se envían parámetros también por query string).

### 4.2 Bases de datos

- **MariaDB 10.11.11** (paquete MariaDB10, puerto **3307**, `bind-address 0.0.0.0`):
  - `acalenca` — SLiMS (usuarios/patrimonio de la biblioteca).
  - `roundcube` — conservada pero sin servicio (Roundcube retirado).
  - `nextcloud` — Nextcloud (usuario `nextcloud`, utf8mb4).
- **Ajustes aplicados (10-11-2026):** `innodb_buffer_pool_size=128M`,
  `max_allowed_packet=64M`, `tx_isolation=READ-COMMITTED`. Ficheros:
  `/var/packages/MariaDB10/etc/my.cnf` (custom) y `synology.cnf` (el buffer pool se re-pisa
  ahí; backups `*.bak-20261110`). ⚠️ Recomprobar tras cada actualización del paquete.
- **PostgreSQL interno de DSM** (`/var/services/pgsql`): usado por DSM (synoindex, etc.);
  no tocar.
- **phpMyAdmin** disponible en DSM para gestionar las bases.

### 4.3 Docker (Container Manager)

| Proyecto | Servicio | Imagen | Puerto | Estado |
|---|---|---|---|---|
| `/volume2/docker/nextcloud/` | `nextcloud` | `nextcloud:35.0-apache` | 8087→80 | ✅ Up |
| ídem | `collabora` | `collabora/code:26.04.6.1.1` | 9980→9980 | ✅ Up (healthy) |
| `/volume2/docker/roundcube/` | `roundcube` | `roundcube/roundcubemail:latest` | 8090→80 | ⏸️ Exited (retirado) |

- Nextcloud: volúmenes `./html:/var/www/html`, `/volume1/nextcloud:/var/www/html/data`,
  `apache-mpm.conf` (prefork 2/1/3/8), `mem_limit 900m`, `extra_hosts host.docker.internal`.
- Collabora: `cap_add [MKNOD, SYS_ADMIN]`, `security_opt seccomp:unconfined`,
  `extra_params … --o:security.seccomp=false`, `PROBE_URL http://127.0.0.1:9980/livez`,
  `mem_limit 768m`. (Sin esos ajustes, los "kit" mueren con EINVAL en este kernel ARM64.)
- Ficheros fuente del stack: repo `G:\GITHUB\lebeche-nextcloud\docker\`.

### 4.4 Proxy inverso de DSM (Panel de control → Portal de inicio de sesión → Avanzado)

| Descripción | Origen | Destino | Certificado |
|---|---|---|---|
| jellyfin | `https://jellyfin.pelotxo.synology.me:443` | `localhost:8096` | Let's Encrypt |
| Web Lebeche | `https://lebeche.pelotxo.synology.me:443` | `localhost:8085` | Let's Encrypt |
| Nextcloud Lebeche | `https://nube.corrientelebeche.es:443` | `localhost:8087` | CF Origin (comodín) |
| Collabora Office | `https://office.corrientelebeche.es:443` | `localhost:9980` | CF Origin (comodín) |
| ~~Mail Hostalia~~ | ~~`correo.corrientelebeche.es`~~ | ~~8090~~ | eliminada 10-11-2026 |

- **Nota DSM 7.4:** la GUI no expone selector de certificado por regla; el cert se asigna
  copiando los `.pem` del certificado Cloudflare Origin en
  `/usr/syno/etc/www/certificate/ReverseProxy_<uuid>/` y editando su `cert.conf`, y luego
  `nginx -s reload`. El certificado CF Origin está respaldado en
  `/volume1/homes/pelotxo/certs/cf-origin-20261110/`.

### 4.5 Paquetes instalados (resumen)

WebStation, PHP8.2, PHP8.3, Apache2.4, MariaDB10, phpMyAdmin, ContainerManager, HyperBackup,
SnapshotReplication (programación **desactivada**), ReplicationService, SynoFinder
(Universal Search, desenmascarado: necesario si se reinstala Synology Drive), Jellyfin +
ffmpeg8 + synocli-videodriver + CodecPack, redis, r8152 (USB 2,5G), Node.js_v22
(dependencia de SynologyApplicationService), Perl, Python2, TextEditor, USBCopy, ScsiTarget,
LogCenter, OAuthService, QuickConnect, SecureSignIn, SMBService, StorageManager, FileStation,
SupportService, SynoAnalytics, SynoOnlinePack_v2.

- **NO instalados:** Synology Calendar, Contacts, Photos, Drive, Office/Spreadsheet
  (se eliminaron también sus restos en `@appdata`/`@appconf`/`@apphome`/`@appshare`).
- **Desinstalados en la limpieza del 10-11-2026:** Node.js_v20, python314, vim, UniversalViewer.
- **Jellyfin:** paquete (no Docker), sirve la videoteca personal `/volume1/video`
  (bibliotecas `PELÍCULAS`, `SERIES`, `PROGRAMAS TV`, `VIDEOCLIPS`); su BD de metadatos usa
  rutas `/volume1/video/…` (importante para el futuro movimiento de la videoteca).

### 4.6 Snapshots y replicación

- **Snapshot Replication: DESACTIVADO** (el 10-11-2026 se eliminaron las 8 tareas diarias de
  réplica). No se crean snapshots nuevas.
- Queda **1 snapshot por carpeta** (de las 3 diarias); las borra el usuario junto con las
  réplicas cuando quiera. Al borrarlas, volume1 liberará ~160 GB adicionales (papelera de
  Photos ya vaciada).
- Las réplicas en volume2 (`video-1`, `homes-replicated`, `music-1`, `web-1`,
  `web_packages-1`, `photo-1`, `photos-1`, `Descargas-1`) quedan como **datos estáticos**;
  `video-1` (546 GB) se eliminará cuando se mueva la videoteca.

### 4.7 Tareas programadas

| Tarea | Horario | Función |
|---|---|---|
| Renovar cert corrientelebeche | diario 04:00 | acme.sh/ZeroSSL (respaldo; el cert CF Origin no se toca) |
| NoCacheBarrioteca | diaria | reaplica `user.conf` (caché de assets) |
| ARP hardening + user.conf | arranque | sysctls + caché tras boot |
| Notificar vencimientos SLiMS | diario 08:00 | avisos de préstamos (`~/scripts/notificar_vencimientos.php`) |
| DSM Auto Update / security advisor | DSM | actualizaciones y avisos |
| HyperBackup "Backup diario" | diario | copia a `/volume2/Backup_NAS/` |
| **Nextcloud cron** | cada 5 min (`/etc/crontab`) | `~/scripts/nextcloud-cron.sh` |
| **Backup Nextcloud DB** | domingos 04:30 (`/etc/crontab`) | `~/scripts/backup-db.sh` (retención 7) |

⚠️ DSM puede regenerar `/etc/crontab`; si desaparecen las 2 líneas de Nextcloud, recrearlas
también en Panel de control → Programador de tareas.

## 5. Aplicaciones de la asociación (repositorios en `G:\GITHUB\`)

| Repo | Proyecto | Tecnología | Estado / notas |
|---|---|---|---|
| `lebeche-web/` | Web oficial | HTML+CSS+JS+PHP (panel staff edita `data/*.json`) | En producción; despliegue por SMB |
| `barrioteca-web/` | PWA Barrioteca | React 19 + Vite + Tailwind | En producción; `api-proxy.php` |
| `barrioteca-slims/` | SLiMS 9.8.0 | PHP 8.2 + MariaDB | En producción; API REST `/api/v1` |
| `barrioteca-android/` | App Android | Kotlin + Compose (`com.lebeche.barrioteca`, v5.0.0/100) | En Google Play (CI GitHub Actions) |
| `barrioteca-ios/` | App iOS | Swift + WKWebView | PWA en iOS |
| `monderas-app/` | App unificada | Kotlin + Compose (`com.lebeche.calendario`, v3.1.0/26) | Play: Calendario (CalDAV), Correo, Web STAFF, Drive |
| `lebeche-correo/` | Correo | Roundcube (retirado) + `newsletter/` activo | Newsletter CLI + baja one-click |
| `lebeche-nextcloud/` | Nube | Docker (Nextcloud + Collabora) | Desplegado 10-11-2026 |
| `documentacion-lebeche/` | Documentación | Markdown | Este documento + guías |
| `nas-scripts/` | Scripts NAS | shell | salud, certs, optimización, notas DSM |
| `LOGOS/` | Identidad de marca | PNG/EPS/tipografías | Color madre `#8DCDE7`, tinte `#A9D9ED`, sombra `#3B758B` |
| `keystore-backup/` | Claves de firma Android | — | No subir a Git |

Detalles relevantes:

- **SLiMS:** ficheros propios a conservar en upgrades (`config/`, `database.php`,
  `config/mail.php`, scripts de importación); rollback a 9.7.2 documentado; SMTP Hostalia;
  notificación de vencimientos diaria. Scripts de mantenimiento públicos
  (`importar-csv.php`, `anadir-libro.php`, `importar-isbns.php`, `actualizar-metadatos.php`)
  → borrarlos cuando se termine la importación.
- **Barrioteca web:** caché en 3 capas (proxy PHP → Nginx → Cloudflare); `env='production'`
  en despliegue; no desplegar `server.cjs` (artefacto Node, en producción solo PHP).
- **Mondera's App:** la pestaña Calendario usa CalDAV → **reapuntar a
  `https://nube.corrientelebeche.es/remote.php/dav/`** (antes apuntaba a Synology Calendar,
  que ya no está instalado). Correo: IMAP directo contra Hostalia. Drive: File Station.
- **Identidad:** tipografías autocontenidas (Open Sans, Garet, Courgette), sin Google Fonts.

## 6. Nextcloud (ficha técnica)

- **URLs:** `https://nube.corrientelebeche.es` · Office `https://office.corrientelebeche.es`
  (subdominio interno de Collabora; no tiene login propio).
- **Versión:** Nextcloud 35.0.2 (imagen `nextcloud:35.0-apache`), PHP embebido en la imagen.
- **Datos:** `/volume1/nextcloud` (bind mount; aún sin cuota de carpeta compartida DSM,
  cuota por usuario en Nextcloud = 20 GB). **BD:** `nextcloud` en MariaDB 10 (3307).
- **Usuarios:** `lebeche` (administrador) y `monderas` (grupo `Monderas`; recreada el
  10-11-2026 tras un borrado desde la UI). Contraseñas en `CREDENCIALES.md`.
- **Apps habilitadas:** Calendar, Contacts, Mail, Notes, Tasks, Deck, Talk (chat), Forms,
  Polls, Bookmarks, Files external, Federation, Photos, Activity, richdocuments (Office).
- **Calendarios:** `lebeche`, `actividades`, `asambleas` — propiedad de `monderas` y
  **compartidos con el grupo `Monderas` (lectura/escritura)** el 10-11-2026.
- **Archivos:** carpetas `Actas` (1,4 GB) y `Fotos` (4 GB) **migradas** desde
  `homes/lebeche` a la cuenta `monderas` y **compartidas con el grupo `Monderas`**
  (permisos 31). Los originales del NAS se conservan como respaldo temporal.
- **Correo:** cuenta `monderas@corrientelebeche.es` configurada en la app Mail para
  `monderas` (IMAP `imap.dominioabsoluto.net:993` SSL · SMTP `smtp.dominioabsoluto.net:587`).
- **Config clave (`config.php`):** trusted_proxies (127.0.0.1, 172.16/12, 192.168.50/24),
  forwarded_for_headers, overwriteprotocol=https, overwritehost, APCu, background=cron,
  previews limitados (2048 px / 50 MB), SMTP Hostalia para notificaciones,
  maintenance_window_start=1 (≈03:00 CET).
- **CalDAV/CardDAV:** `https://nube.corrientelebeche.es/remote.php/dav/`.
- **Límites conocidos:** RAM total del NAS 2 GB → la ofimática colaborativa aguanta
  **1–2 ediciones simultáneas**; subidas web ≤ 100 MB (Cloudflare) — la app de escritorio
  trocea; sin Full Text Search ni Recognize (pesados).
- **Actualizaciones:** pinnear `35.0`; al saltar de major: `docker compose pull` +
  `occ upgrade` + verificar apps. Collabora: `pull` mensual.

## 7. Correo electrónico

- **Proveedor:** Hostalia (MX `mx.corrientelebeche.es`). Buzón principal:
  `monderas@corrientelebeche.es`. Autenticación de correo: **SPF + DKIM (`domabs`) + DMARC** OK.
- **Clientes:** IMAP `imap.dominioabsoluto.net:993` (SSL) · SMTP `smtp.dominioabsoluto.net:587`
  (STARTTLS). ⚠️ Usar los hostnames del **proveedor** (`*.dominioabsoluto.net`), no
  `*.corrientelebeche.es` (el certificado TLS es del proveedor; puerto 465 cerrado).
- **Webmail:** Nextcloud Mail (Roundcube retirado el 10-11-2026; datos conservados en
  `/volume2/docker/roundcube/` y BD `roundcube` para rollback).
- **Newsletter:** envío por CLI desde el NAS (`/volume1/web/newsletter/enviar.php`, SMTP
  Hostalia) con baja en un clic (`baja.php`, `List-Unsubscribe` RFC 8058).
- Los CNAME `correo`/`webmail` de Cloudflare quedan sin servicio; se pueden borrar.

## 8. Backups y recuperación

| Qué | Dónde | Frecuencia | Restaurar |
|---|---|---|---|
| Config DSM | `/volume2/Backup_NAS/dsm-update-20261105/Pelotxo-NAS_20261105.dss` + HyperBackup | tras cambios | Panel de control → Actualizar y restaurar |
| BD SLiMS | `…/dsm-update-20261105/acalenca-20261105.sql.gz` + `Backup_NAS/slims/` | puntual | `gunzip \| mysql -u acalenca -h 127.0.0.1 -P 3307` |
| BD Nextcloud | `/volume2/Backup_NAS/nextcloud-db/nextcloud-YYYYMMDD.sql.gz` (retención 7) | domingos 04:30 | `gunzip \| mysql -u root -p nextcloud` |
| Web (`/volume1/web`) | HyperBackup "Backup diario" → `Backup_NAS/Pelotxo-NAS_1.hbk` | diario | HyperBackup → restaurar |
| Nextcloud datos | ⚠️ **PENDIENTE** añadir `/volume1/nextcloud` a HyperBackup | — | copiar carpeta + `occ files:scan --all` |
| Réplicas volume1→volume2 | DESACTIVADAS (Snapshot Replication) | — | — |
| Keystores Android | `G:\GITHUB\keystore-backup\` + secretos GitHub | — | ver `nas-scripts/RECUPERAR_KEYSTORE.md` |
| Certificados | `~/certs/cf-origin-20261110/` (CF Origin) + acme.sh (ZeroSSL) | — | reimportar en DSM |

- **⚠️ No hay backup offsite** (todo reside en el NAS). Recomendación prioritaria: disco USB
  externo con HyperBackup (o nube) para `web`, `homes/lebeche`, `nextcloud` y las BDs.
- La replicación local volume1→volume2 queda **desactivada** por decisión del usuario
  (10-11-2026); las réplicas existentes son estáticas.

## 9. Seguridad y credenciales

- **Credenciales centralizadas en `G:\GITHUB\CREDENCIALES.md`** (archivo local, **fuera de
  Git**). Este documento no las incluye.
- Normas ya establecidas: no subir `.env`, `sysconfig.inc.php`, `api-config.php`,
  keystores ni `CONTEXT.md`/reglas de IA a Git; no eliminar las reglas de denegación de
  `user.conf` de Nginx; no perder `signing.keystore` (firma de Play).
- Recomendaciones activas: 2FA en Nextcloud (app autenticadora), contraseñas individuales
  por cuenta, auto-bloqueo de DSM, Cloudflare (WAF/reglas gratuitas), revisar scripts de
  mantenimiento públicos de SLiMS cuando acabe la importación.
- Roundcube retirado = menos superficie expuesta. Collabora solo responde a WOPI desde
  Nextcloud (`aliasgroup1`); su subdominio no tiene login.

## 10. Mantenimiento y salud

- **Verificación:** `ssh pelotxo@192.168.50.94 "sh /var/services/homes/pelotxo/verificar-salud.sh"`
  (6 bloques: red, cabeceras de caché, micro-caché, despliegue, RAM, Cloudflare/API).
- **Regla de oro tras actualizar DSM:** comprobar primero el adaptador USB 2,5G (`eth1` = 2500).
- **Tras cada update de DSM o de MariaDB10:** revisar proxy inverso (certs de `ReverseProxy_*`),
  buffer pool/isolation de MariaDB, `user.conf` (tarea NoCacheBarrioteca), tareas crontab de
  Nextcloud y contenedores (`docker ps`).
- **Certificados:** Cloudflare Origin (2041, no caduca a corto plazo); ZeroSSL/acme.sh renovado
  a diario como respaldo (`renew-deploy-cert.sh`).
- **Nextcloud:** revisar el panel de administración (Ajustes → Vista general) tras
  actualizaciones; `occ status`; logs `docker logs nextcloud`.

## 11. Pendientes y próximos pasos

1. Borrar las **últimas snapshots** y las **réplicas** (lo hace el usuario) → libera ~160 GB.
2. **Mover la videoteca** (`video`, 546 GB) a volume2 con bind mount para Jellyfin.
3. Añadir **`/volume1/nextcloud` a HyperBackup** y, en general, preparar un **backup offsite**.
4. ~~Crear las cuentas de las Monderas y compartir calendarios/carpetas con el grupo~~ —
   **hecho el 10-11-2026**: calendarios y carpetas `Actas`/`Fotos` ya compartidos con el
   grupo `Monderas`; quedan por crear las cuentas personales (las añade el usuario).
5. **Reapuntar el CalDAV de Mondera's App** a Nextcloud.
6. ~~Migrar ACTAS (1,4 GB) y Fotos (4 GB) desde `homes/lebeche` a Nextcloud~~ — **hecho el
   10-11-2026** (copiadas a la cuenta `monderas`; originales conservados como respaldo).
7. Borrar los CNAME `correo`/`webmail` de Cloudflare si se desea.
8. Borrar scripts de mantenimiento públicos de SLiMS al terminar la importación.
9. (Opcional) 2FA y cambio de contraseñas por defecto en Nextcloud.

## 12. Glosario

| Término | Significado |
|---|---|
| Monderas | Organizadoras de la asociación (gestionan calendario, web y correo) |
| Morralleras | Socias: acuden a actividades, no organizan |
| Barrioteca Acalencá | Biblioteca vecinal autogestionada (préstamos por escaneo) |
| SLiMS | Software de biblioteca (PHP + MariaDB), backend de la Barrioteca |
| CalDAV/CardDAV | Protocolos de calendario y contactos (usa Mondera's App / Nextcloud) |
| WOPI | Protocolo de integración de Nextcloud Office con Collabora |
| CODE | Collabora Online Development Edition (suite ofimática libre) |
| HyperBackup | Copias de seguridad de Synology |
| Snapshot Replication | Instantáneas + réplicas volumen1→volumen2 (desactivado) |
| Cloudflare Origin Certificate | Certificado del origen (NAS) firmado por Cloudflare, modo Full (strict) |
| DSM | DiskStation Manager, sistema operativo del NAS Synology |





