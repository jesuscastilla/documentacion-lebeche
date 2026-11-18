# Guía urgente: exportar los datos de CryptPad

> Actualización **18-11-2026**: la cuenta `corrientelebeche` de cryptpad.fr **ha sido
> borrada** (verificado: el servidor devuelve 404 en el bloque de login; códigos internos
> `EDELETED` / `NO_SUCH_USER`). CryptPad no puede recuperarla.
> **La única vía de rescate es la sesión de invitado de los navegadores** de quien tuviera
> documentos abiertos o su CryptDrive de invitado guardado.

## ⚠️ Primero: revisar las sesiones de invitado (¡esto es lo importante ahora!)

En **el mismo navegador y equipo** donde la miembra vio "la cuenta" al entrar:

1. Abrir https://cryptpad.fr/drive/ **sin iniciar sesión** (o tras cerrar sesión).
2. Si aparece un **CryptDrive con documentos** → es la sesión de invitado del navegador:
   **exportar todo YA** siguiendo el Paso 2 de abajo.
3. Pedir a TODAS las Monderas que hagan lo mismo en sus ordenadores/móviles: los
   documentos pueden estar repartidos entre varios navegadores.

## Paso 2 — Exportar los documentos (uno a uno)

Abrir cada documento y usar el menú **File/Archivo → Export/Exportar**:

| Tipo de documento | Formatos a elegir |
|---|---|
| **Texto enriquecido** | Markdown (`.md`) o HTML (`.html`) — legibles en cualquier sitio |
| **Hoja de cálculo** | `.xlsx` (Export to file) o `.csv` |
| **Presentación** | Markdown (`.md`) |
| **Kanban** | `.json` (y copiar el contenido si es corto) |
| **Código/Markdown** | `.md` |
| **Pizarra/Whiteboard** | `.svg` o `.png` |
| **Formulario** | copiar las respuestas desde la pestaña *Responses* |

Prioridad: **actas, documentos de la asociación y textos**.
Guardad todo en una carpeta local tipo `cryptpad-export/` con nombres claros.

## Paso 3 — Compartir lo exportado

- Subir la carpeta a **nube.corrientelebeche.es → Archivos → MONDERAS → `Migración CryptPad`**.
- O enviársela a Pelotxo (administración).

## Confirmación con el equipo de CryptPad

Para dejar constancia (y por si existiera algún resto), escribir al equipo de CryptPad
(contacto en su web https://cryptpad.fr/contact.html y Mastodon) indicando el nombre de
usuario `corrientelebeche`: pueden confirmar la fecha de borrado por inactividad.

## Después (migración)

- Los `.md` se abren en la nube y se pueden guardar como `.docx` desde el editor.
- Las hojas `.csv` se abren con la hoja de cálculo de la nube.
