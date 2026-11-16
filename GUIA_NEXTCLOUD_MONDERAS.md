# Guía de Nextcloud para las Monderas

> **Nube de la asociación Lebeche** · Actualizado: 16-11-2026
> Dirección: **https://nube.corrientelebeche.es**
> Guía completa con todo lo que ya está en marcha: archivos, calendarios, contactos,
> correo, oficina colaborativa (OnlyOffice), videollamadas (Talk) y equipos/comisiones.

---

## 1. ¿Qué es Nextcloud?

Es **nuestra propia nube**: archivos, calendarios, contactos, correo, tareas, formularios,
videollamadas y oficina colaborativa **alojada en el NAS de la asociación** (Salobreña, con
apoyo de un PC auxiliar para el editor de documentos), no en servidores de Google, Microsoft
o Dropbox. Los datos son nuestros y se sirven por nuestro dominio (`corrientelebeche.es`)
con conexión cifrada (HTTPS).

Incluye de serie:

| App | Para qué | Estado |
|---|---|---|
| **Archivos** | Guardar, organizar y compartir documentos, actas, fotos… | ✅ en uso |
| **Calendario** | Calendarios compartidos (`lebeche`, `actividades`, `asambleas`) | ✅ en uso |
| **Correo** | Buzón `monderas@corrientelebeche.es` (Hostalia) integrado | ✅ en uso |
| **Contactos** | Libreta **Morralleras** (socias), compartida en solo lectura | ✅ en uso |
| **Fotos** | Galería de fotos de actividades | ✅ en uso |
| **Office** | Editor integrado (OnlyOffice): crear, abrir y **co-editar** Word/Excel/PowerPoint en el navegador | ✅ desde 16-11-2026 |
| **Talk** | **Videollamadas** y chats de la asociación (reuniones de grupo) | ✅ en uso |
| **Equipos (Teams)** | Las **comisiones** con su carpeta y sus miembros | ✅ 10 comisiones creadas |
| **Tareas** | Listas de tareas personales y compartidas | ✅ disponible |
| **Formularios / Encuestas** | Formularios para socias y votaciones | ✅ disponible |

## 2. Cómo entrar

- **Desde el navegador (ordenador o móvil):** abre `https://nube.corrientelebeche.es` y
  entra con tu usuario y contraseña.
- **App de móvil:** instala la app **Nextcloud** (Google Play / App Store) y en
  "Servidor" escribe `https://nube.corrientelebeche.es`.
- **App de escritorio (recomendada):** instala el cliente de Nextcloud en Windows/Mac/Linux
  (`nextcloud.com/install`). Crea una carpeta sincronizada: lo que guardes ahí se sube solo,
  y lo que haya en la nube se descarga solo. **Úsala para archivos grandes** (vídeos, fotos).

## 3. Cuentas: una por persona (¡ya está hecho!)

Cada Mondera tiene **su propia cuenta** (creadas en noviembre de 2026) y todas están en el
**grupo `Monderas`**:

`ana` · `jose` · `enzo` · `noah` · `dario` · `rodolphe` · `sara`

- Lo compartido (carpetas, calendarios, contactos) se comparte **con el grupo**: cuando
  entre una Mondera nueva, se añade al grupo y ya lo ve todo.
- La cuenta **`lebeche`** es la de **administración** (Pelotxo): crea usuarias, gestiona
  cuotas y apps. No se usa para el trabajo diario.
- La cuenta `monderas` queda como **cuenta de emergencia/pruebas** (recuperar accesos,
  configuraciones comunes).

**No compartáis contraseñas**: cada una usa la suya (ver normas de uso).

## 4. Guía básica de uso (todo esto ya está montado)

### Paso 1 — Cuentas

✅ Las 7 cuentas personales están creadas y en el grupo `Monderas` (ver sección 3).
Si entra una Mondera nueva, se lo pide a Pelotxo y en un momento la tiene.

### Paso 2 — Primer acceso de cada una

1. Entra en `https://nube.corrientelebeche.es` con tu usuario y tu contraseña.
2. Si es la primera vez, **cambia la contraseña** (avatar → Ajustes → Seguridad) por una
   propia y larga (un gestor de contraseñas ayuda).
3. Añade tu **nombre y foto** (avatar → Ajustes → Personal) para que las demás te
   reconozcan en documentos compartidos y videollamadas.

### Paso 3 — Calendarios compartidos

✅ Los calendarios **`lebeche`** (general), **`actividades`** y **`asambleas`** están
compartidos con el **grupo `Monderas`** en lectura/escritura: los ves directamente en la
app **Calendario** de la nube al entrar con tu cuenta.

- Para verlos en el móvil (app nativa CalDAV), la URL es
  `https://nube.corrientelebeche.es/remote.php/dav/` (con tu usuario y contraseña de la
  nube).

### Paso 4 — Correo (buzón de la asociación)

✅ El buzón **`monderas@corrientelebeche.es`** (Hostalia) está **configurado en la app
Correo de la nube** para las cuentas de las Monderas: al abrir Correo lo veis sin
configurar nada.

- Todas veis el **mismo buzón compartido** (correo entrante/saliente de la asociación).
- Cuando se cree una cuenta nueva, se le añade el buzón automáticamente (hay un
  procedimiento que lo hace cada noche).
- También podéis seguir usando las apps de correo del móvil/ordenador con los datos de
  Hostalia (`imap.dominioabsoluto.net` / `smtp.dominioabsoluto.net`).
- *(El antiguo webmail Roundcube está retirado: el correo se mira desde Nextcloud o desde
  las apps de correo, como hasta ahora.)*

### Paso 5 — Organizar los Archivos

- En **Archivos** tienes: tu **carpeta personal** (solo la ves tú) y las carpetas
  **compartidas**:
  - **MONDERAS** — carpeta de equipo general (100 GB): aquí viven `Actas` y `Fotos`
    (migradas del NAS, que guarda copia de seguridad).
  - **carpeta general MONDERAS** — carpeta del equipo del mismo nombre.
  - **Una carpeta por comisión** (ver Paso 8, Equipos).
- Para subir archivos: botón **«+» → Subir archivo** (o arrastra y suelta). Lo que subas
  en una carpeta compartida lo ven todas las Monderas.
- Cada cuenta tiene su cuota personal; si alguien se queda sin espacio, avisa a la admin
  y se amplía.

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

### Paso 8 — Equipos (comisiones)

Las **10 comisiones** están montadas como **Equipos** en la nube, cada una con su carpeta
privada y sus miembros:

C. RAÍZ · MONDERAS · E.T. APERTURAS · SOBERANÍA ALIMENTARIA · ACALENCÁ Y LOGÍSTICA ·
COMUNICACIÓN/DISEÑO/ARTE · CUIDADOS Y CARIÑITOS · FACILITACIÓN DE ASAMBLEAS ·
HEMEROTECA LEBECHE · SUBVERSIONES

- En la app **Equipos** ves a qué comisiones perteneces y quién está en cada una.
- En **Archivos**, cada comisión tiene su carpeta: **solo la ven y la tocan sus miembros**.
- Los documentos de cada comisión se guardan ahí (actas, materiales, presupuestos…).
- Si una Mondera cambia de comisión, se la añade o quita del equipo y ya está.

### Paso 9 — Talk (videollamadas y chats)

La app **Talk** sirve para **videollamadas de la asociación** (reuniones de comisión,
asambleas a distancia…):

- Entra en **Talk** → elige una conversación o crea una nueva → botón de **videollamada**
  (cámara). Se puede compartir pantalla, chatear a la vez y usarla desde el móvil o el
  ordenador.
- Las reuniones van fluida: la señalización usa un servidor propio (PC auxiliar) y el
  audio/vídeo pasa por el TURN del NAS.
- Para invitar a gente de fuera sin cuenta: se puede generar un **enlace público** de la
  conversación — consultad con la admin antes de usarlo.

### Paso 10 — Archivos grandes (vídeos, carpetas de fotos)

- Por la **web**, el límite es **100 MB por archivo** (límite de Cloudflare).
- Para archivos más grandes usa la **app de escritorio** de Nextcloud: trocea la subida
  automáticamente y no hay límite práctico.
- La app del móvil también trocea las subidas grandes.

### Paso 11 — Calendario y contactos en el móvil

- **Mondera's App** sigue usando el calendario de Synology (CalDAV de la asociación) como
  hasta ahora.
- Si además quieres los calendarios de la nube en el móvil, añade una cuenta **CalDAV**
  nativa con la URL `https://nube.corrientelebeche.es/remote.php/dav/`.
- La libreta de contactos **Morralleras** también puede sincronizarse con una cuenta
  **CardDAV** en esa misma URL.

## 5. Normas de uso (para no pisarnos)

1. **Cada una con su cuenta.** Nada de compartir contraseñas.
2. Lo común, **en las carpetas del grupo**; lo personal, en tu carpeta propia.
3. **No borrar** de las carpetas compartidas sin avisar: lo que se borra pasa a la
   **papelera de Nextcloud y se conserva 30 días**, así que se puede recuperar.
4. Si un archivo es el **único ejemplar** (fotos antiguas, actas), asegurarse de que está
   subido a la nube antes de borrar el original del móvil.
5. Las contraseñas iniciales se cambian en el **primer acceso**.
6. Ante cualquier duda técnica: Pelotxo (administración).
7. **Documentos**: para editar en grupo, todas dentro del navegador (editor OnlyOffice).
   Si alguien edita una copia local, avisad para no pisaros (ver Paso 7).
8. **Comisiones**: lo de cada comisión, en su carpeta de Equipo; lo común, en MONDERAS.
9. **Videollamadas**: probad cámara y micro unos minutos antes de una asamblea; para
   reuniones muy grandes, avisad a la admin con tiempo.

## 6. Preguntas frecuentes

| Problema | Solución |
|---|---|
| "No puedo entrar" | Comprueba usuario/contraseña; si la olvidaste, pide a la admin que la restablezca |
| "No veo el calendario de actividades" | Revisa la app Calendario: los 3 calendarios compartidos deben aparecer; si no, avisa a la admin |
| "No me abre un Word en el navegador" | Haz clic en el archivo dentro de Archivos (web). Si aun así no abre, avisa a la admin (el editor corre en el PC auxiliar) |
| "Quiero crear un documento nuevo" | Archivos → botón «+» (Nuevo) → Documento de texto / Hoja de cálculo / Presentación (ver Paso 7) |
| "No veo la carpeta de mi comisión" | Mira en Archivos → carpeta del Equipo; si no está, la admin te añade al equipo (Paso 8) |
| "La videollamada va mal" | Comprueba tu conexión; si persiste, avisa a la admin (los servidores de señalización y TURN se vigilan desde administración) |
| "No me deja subir un vídeo de 300 MB por la web" | Usa la app de escritorio/móvil (trocea la subida) |
| "¿Dónde están mis datos?" | En el NAS de la asociación (Salobreña). Hay copia semanal de la base de datos; la copia completa de archivos está en preparación |
| "¿Y si se rompe el NAS?" | Existen copias locales (web y BBDD); **no hay todavía copia externa**: no guardéis aquí el único ejemplar de algo irremplazable hasta avisaros de que el backup externo está listo |

## 7. Pendientes técnicos (para la administración)

1. Email de `jose` (única cuenta sin buzón configurado).
2. Rotación semanal del equipo **FACILITACIÓN DE ASAMBLEAS** (manual por ahora).
3. Asignar miembros a **HEMEROTECA LEBECHE** y **SUBVERSIONES**.
4. Añadir `/volume1/nextcloud` a HyperBackup (copias de archivos) + preparar copia
   externa (USB).
5. Borrar los DNS `correo`/`webmail` de Cloudflare (ya sin uso).
6. Decidir si Mondera's App pasa a usar el calendario de Nextcloud (hoy usa Synology).
7. 2FA: descartada de momento por decisión de la asociación (se puede reabrir).


