# Guía urgente: exportar los datos de CryptPad

> Situación (18-11-2026): la cuenta `corrientelebeche` de cryptpad.fr da errores de login
> intermitentes (lleva ~1 mes). Hoy una miembra ha conseguido entrar. **Esta guía es para
> exportar todo lo posible AHORA, sin cerrar la sesión.**

## ⚠️ Antes de nada

- **NO cerrar la sesión** ni cerrar el navegador hasta terminar la exportación.
- **NO borrar caché/cookies** del navegador (ahí están las claves de los documentos).
- Hacerlo en un ordenador (mejor que en el móvil).

## Paso 1 — Copia de seguridad de la cuenta (lo primero)

1. Avatar (arriba a la derecha) → **Settings (Ajustes)** → sección **Account / Usuario**.
2. Botón **Backup / Copia de seguridad** → descarga un archivo con todo el CryptDrive.
   - Guardadlo con nombre claro: `cryptpad-backup-2026-11-18.json`.
   - ⚠️ Ese archivo está cifrado con la clave de la cuenta: guardad también la contraseña.

## Paso 2 — Exportar los documentos importantes (uno a uno)

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

Prioridad: **actas, documentos de la asociación y textos** (fotos y carteles probablemente
ya estén en la nube). Para cada uno, guardad en una carpeta local tipo
`cryptpad-export/` con nombres claros.

## Paso 3 — Compartir lo exportado

- Subir la carpeta a la nube de la asociación: **nube.corrientelebeche.es → Archivos →
  carpeta MONDERAS → (nueva carpeta) `Migración CryptPad`**.
- O enviársela a Pelotxo (administración) para que la organice.

## Si el login vuelve a fallar

- Reintentar más tarde (ha funcionado por rachas).
- Probar otro navegador o una ventana privada.
- Escribir al equipo de CryptPad (contacto en su web / Mastodon) si la cuenta no deja entrar
  durante días: pueden confirmar si sigue existiendo o está bloqueada por inactividad.

## Después (migración)

- Los `.md` se convierten a `.docx` con el editor de la nube si hace falta (se abren y se
  "guardan como" docx), o se dejan como texto.
- Las hojas `.csv` se abren con el editor de hojas de la nube.
