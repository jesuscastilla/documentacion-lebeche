# Guía de Nextcloud para las Monderas

> **Nube de la asociación Lebeche** · Fecha: 10-11-2026
> Dirección: **https://nube.corrientelebeche.es**
> Este documento explica qué es Nextcloud, cómo usarlo y **qué pasos seguimos ahora**.

---

## 1. ¿Qué es Nextcloud?

Es **nuestra propia nube**: una plataforma de archivos, calendarios, contactos, correo,
notas, tareas y ofimática **alojada en el NAS de la asociación** (en Salobreña), no en
servidores de Google, Microsoft o Dropbox. Los datos son nuestros y se sirven a través de
nuestro dominio (`corrientelebeche.es`) con conexión cifrada (HTTPS).

Incluye de serie:

| App | Para qué |
|---|---|
| **Archivos** | Guardar, organizar y compartir documentos, actas, fotos… |
| **Calendario** | Calendarios compartidos de la asociación (actividades, asambleas…) |
| **Correo** | Leer y enviar desde `monderas@corrientelebeche.es` (o cuentas propias) |
| **Contactos** | Agenda de la asociación |
| **Fotos** | Galería de fotos de actividades |
| **Office** | ✅ Editor integrado (OnlyOffice): crear, abrir y co-editar Word/Excel/PowerPoint en el navegador — ver Paso 7 |
| **Tareas** | Listas de tareas |
| **Formularios / Encuestas** | Formularios para socias y votaciones |

## 2. Cómo entrar

- **Desde el navegador (ordenador o móvil):** abre `https://nube.corrientelebeche.es` y
  entra con tu usuario y contraseña.
- **App de móvil:** instala la app **Nextcloud** (Google Play / App Store) y en
  "Servidor" escribe `https://nube.corrientelebeche.es`.
- **App de escritorio (recomendada):** instala el cliente de Nextcloud en Windows/Mac/Linux
  (`nextcloud.com/install`). Crea una carpeta sincronizada: lo que guardes ahí se sube solo,
  y lo que haya en la nube se descarga solo. **Úsala para archivos grandes** (vídeos, fotos).

## 3. Cuentas: ¿una para todas o una por persona?

**Recomendación: UNA CUENTA POR PERSONA.** Es la práctica correcta y lo que vamos a hacer:

| Opción | ¿La usamos? | Por qué |
|---|---|---|
| Una sola cuenta compartida para todas | ❌ | No se sabe quién hizo/borró qué; si una pierde la contraseña, se cambia para todas; imposible poner permisos distintos; sin cuota por persona. |
| **Una cuenta por Mondera** | ✅ | Cada una tiene su usuario y contraseña; se puede saber quién ha tocado cada archivo; se pueden dar permisos distintos; recuperación individual. |
| Cuenta de la asociación (genérica) | ⚠️ Solo para emergencias | La cuenta `monderas` actual sirve de **pruebas/emergencia**. En cuanto haya cuentas personales, se usa solo si alguien olvida la suya o para configuraciones comunes. |

**Lo compartido no vive en una cuenta común, sino en el GRUPO:**

- Existe un grupo llamado **`Monderas`**.
- Las carpetas, calendarios y documentos compartidos se comparten **con el grupo**, no con
  cuentas sueltas: así, cuando entre una Mondera nueva, se añade al grupo y ya lo ve todo.
- La cuenta **`lebeche`** es la de **administración** (crear usuarias, quotas, apps). No se
  usa para el trabajo diario.

## 4. Pasos que seguimos AHORA (checklist)

### Paso 1 — Crear la cuenta de cada Mondera

La persona administradora (Pelotxo) crea una cuenta por persona (nombre corto, p. ej.
`ana`, `maria`…) con una contraseña inicial y la apunta. Cada cuenta se añade al grupo
`Monderas`. Pedir a cada Mondera su nombre de usuaria preferido.

### Paso 2 — Primer acceso de cada una

1. Entra en `https://nube.corrientelebeche.es` con tu usuario y la contraseña inicial.
2. **Cambia la contraseña** (menú de tu avatar → Ajustes → Seguridad) por una propia
   (larga, que no uses en otros sitios; puedes usar un gestor de contraseñas).
3. **Activa la verificación en dos pasos (2FA)** en el mismo sitio: descárgate una app
   autenticadora (p. ej. Aegis, FreeOTP o Google Authenticator), escanea el código QR y
   guarda los **códigos de respaldo** en un sitio seguro.

### Paso 3 — Calendarios compartidos

✅ **HECHO (10-11-2026):** los calendarios **`lebeche`**, **`actividades`** y **`asambleas`**
están compartidos con el **grupo `Monderas`** (lectura/escritura). Cuando crees las cuentas
de las Monderas y las añadas al grupo, verán estos calendarios automáticamente.

Además, cada una puede añadir el calendario a su móvil (Mondera's App o cualquier app
CalDAV) con la URL `https://nube.corrientelebeche.es/remote.php/dav/`.

### Paso 4 — Correo

La cuenta `monderas@corrientelebeche.es` (Hostalia) **ya está configurada** en la app
**Correo** de la cuenta `monderas`. Decisiones a tomar:

- Si el buzón es **común**: añadirlo en cada cuenta personal (Ajustes → Correo → Añadir
  cuenta → datos de Hostalia). Todas verán el mismo buzón.
- Si preferís **buzones personales** (cada Mondera con el suyo en Hostalia), se añade cada
  uno igualmente.

*(El antiguo webmail Roundcube queda retirado; el correo se mira ahora desde Nextcloud o
desde las apps de correo del móvil/ordenador, como hasta ahora.)*

### Paso 5 — Organizar los Archivos

1. En **Archivos**, crear las carpetas comunes de la asociación (p. ej. `Actas`,
   `Documentos`, `Cartelería`, `Fotos`, `Formularios`).
2. Compartir cada carpeta con el **grupo Monderas** (botón compartir → Monderas → edición).
3. ✅ **HECHO (10-11-2026):** las carpetas **`Actas` (1,4 GB)** y **`Fotos` (4 GB)** del NAS
   (`homes/lebeche`) se han copiado a la cuenta `monderas` y se han **compartido con el
   grupo Monderas** (lectura/escritura). Los originales siguen en el NAS como copia de
   seguridad hasta que confirmes que está todo bien.
4. Subir lo que cada una tenga en su ordenador/móvil (carteles, presupuestos, fotos…).
5. La **cuota actual es de 20 GB por cuenta** (se puede ampliar si hace falta).

### Paso 6 — Compartir con el mundo (enlaces)

- Cualquier archivo o carpeta se puede compartir por **enlace público** (botón compartir →
  crear enlace): útil para pasar fotos de actividades a las socias sin darles cuenta.
- Para cosas sensibles: marca **contraseña** y **fecha de caducidad** en el enlace.
- Para **recibir** archivos de socias: crear una carpeta con enlace de **subida** (permiso
  "Subir archivos") — se usa, p. ej., para recoger fotos de una actividad.
- Norma: fotos donde salgan menores → enlace con contraseña y caducidad, no enlace abierto.

### Paso 7 — Documentos de ofimática: editor OnlyOffice (Word/Excel/PowerPoint)

Desde el **16-11-2026** tenemos editor de documentos integrado (OnlyOffice, funciona en un
PC auxiliar de la asociación). Se usa **dentro del navegador**, sin instalar nada.

**Crear un documento desde cero**

1. Entra en **Archivos** y sitúate en la carpeta donde quieras crearlo (tu carpeta personal
   o la carpeta de equipo **MONDERAS**).
2. Pulsa el botón **«+» (Nuevo)** de la barra superior.
3. Elige una de las tres opciones de OnlyOffice:
   - 📄 **Documento de texto** (`.docx`, como Word)
   - 📊 **Hoja de cálculo** (`.xlsx`, como Excel)
   - 📽️ **Presentación** (`.pptx`, como PowerPoint)
4. Se crea el archivo en blanco y **se abre el editor directamente**. Al cerrarlo, queda
   guardado en la nube.

**Abrir y editar**

- Haz **clic en cualquier `.docx`, `.xlsx` o `.pptx`** de Archivos: se abre en el editor.
- También abre `.doc`, `.xls`, `.ppt`, `.csv`, `.txt` y `.odt`. Con `.pdf` solo se **ve**
  (no se edita).
- El guardado es **automático** mientras escribes (no hace falta darle a guardar).
- Las versiones anteriores quedan en el botón **Versiones** (historial de cambios).

**Editar varias personas a la vez (co-edición en tiempo real)**

- Dos o más Monderas pueden abrir el mismo documento a la vez y escribir **simultáneamente**.
- Se ve el nombre de quién está dentro y su cursor, cada una de un color.
- Se pueden dejar **comentarios** (botón de comentarios del editor) y responderlos.
- Ojo: para co-editar, todas deben estar **dentro del navegador**. Si una descarga el archivo
  y lo edita en local (Word/LibreOffice), creará una versión aparte (ver normas de uso).

**Consejos**

- Los documentos de la asociación, mejor en la carpeta **MONDERAS** (los ven todas).
- En el **móvil**: el editor funciona en el navegador, pero para trabajar en serio usad un
  ordenador (la app móvil de Nextcloud no abre el editor de Office).
- Quien prefiera seguir con LibreOffice/Word en su ordenador puede seguir
  descargando/subiendo como antes; las dos formas conviven.

### Paso 8 — Archivos grandes (vídeos, carpetas de fotos)

- Por la **web**, el límite es **100 MB por archivo** (límite de Cloudflare).
- Para archivos más grandes usa la **app de escritorio** de Nextcloud: trocea la subida
  automáticamente y no hay límite práctico.
- La app del móvil también trocea las subidas grandes.

### Paso 9 — Calendario en el móvil

- **Mondera's App** se actualizará para usar el calendario de Nextcloud (CalDAV):
  `https://nube.corrientelebeche.es/remote.php/dav/`.
- En iPhone/Android también se puede añadir la cuenta CalDAV nativa con esa misma URL.

## 5. Normas de uso (para no pisarnos)

1. **Cada una con su cuenta.** Nada de compartir contraseñas.
2. Lo común, **en las carpetas del grupo**; lo personal, en tu carpeta propia.
3. **No borrar** de las carpetas compartidas sin avisar: lo que se borra pasa a la
   **papelera de Nextcloud y se conserva 30 días**, así que se puede recuperar.
4. Si un archivo es el **único ejemplar** (fotos antiguas, actas), asegurarse de que está
   subido a la nube antes de borrar el original del móvil.
5. Las contraseñas iniciales se cambian en el **primer acceso**.
6. Ante cualquier duda técnica: Pelotxo (administración).

## 6. Preguntas frecuentes

| Problema | Solución |
|---|---|
| "No puedo entrar" | Comprueba usuario/contraseña; si la olvidaste, pide a la admin que la restablezca |
| "No veo el calendario de actividades" | Acepta la compartición del grupo (aviso en Calendario) o pide que te añadan al grupo |
| "No me abre un Word en el navegador" | Haz clic en el archivo dentro de Archivos (web). Si aun así no abre, avisa a la admin (el editor corre en el PC auxiliar) |
| "No me deja subir un vídeo de 300 MB por la web" | Usa la app de escritorio/móvil (trocea la subida) |
| "¿Dónde están mis datos?" | En el NAS de la asociación (Salobreña). Hay copia de seguridad de la base de datos semanal; la copia completa de archivos se añadirá en breve |
| "¿Y si se rompe el NAS?" | Existen copias locales (web y BBDD); **no hay todavía copia externa**: no guardéis aquí el único ejemplar de algo irremplazable hasta avisaros de que el backup externo está listo |

## 7. Próximos pasos técnicos (para la administración)

1. Crear las cuentas de las Monderas (grupo `Monderas`).
2. Compartir los 3 calendarios con el grupo.
3. Migrar `ACTAS` y `Fotos` desde el NAS a Nextcloud.
4. Añadir `/volume1/nextcloud` a HyperBackup (copias) + preparar copia externa (USB).
5. Reapuntar Mondera's App (CalDAV) a Nextcloud.
6. Borrar los DNS `correo`/`webmail` de Cloudflare (ya sin uso).
7. Mover la videoteca del NAS a volume2 (no afecta a Nextcloud).
8. 2FA y contraseñas definitivas en todas las cuentas.


