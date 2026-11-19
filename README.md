# Documentación de la Asociación Lebeche

Repositorio central de documentación técnica, manuales operativos, guías de administración e inventario de infraestructura para la **Asociación Cultural y Vecinal Lebeche** (Salobreña, Granada).

[![Documentation](https://img.shields.io/badge/Docs-Markdown%20%7C%20PDF-informational?style=flat-square&logo=markdown&logoColor=white)]()
[![Association](https://img.shields.io/badge/Association-Lebeche-007ACC?style=flat-square)](https://www.corrientelebeche.es/lebeche/)
[![Security: Clean](https://img.shields.io/badge/Credentials-Zero%20Secrets%20Versioned-success?style=flat-square)]()

---

## 📚 Índice de documentos

Los manuales están disponibles en formato Markdown para lectura directa en GitHub y en versiones compaginadas en **PDF A4** para impresión y distribución interna:

| Documento | Destinatarias | Descripción y contenido | Versión PDF |
|---|---|---|---|
| [`INFRAESTRUCTURA_TECNICA_LEBECHE.md`](INFRAESTRUCTURA_TECNICA_LEBECHE.md) | Administración técnica | Inventario completo de la infraestructura digital: dominio, DNS (Cloudflare), servidor NAS Synology, proxy inverso, servicios Docker, copias de seguridad, mantenimiento periódico y tareas pendientes. | [Descargar PDF](INFRAESTRUCTURA_TECNICA_LEBECHE.pdf) |
| [`GUIA_NEXTCLOUD_MONDERAS.md`](GUIA_NEXTCLOUD_MONDERAS.md) | Monderas (organizadoras) | Manual de uso de la nube asociativa Nextcloud: acceso seguro, sincronización de archivos, uso de calendarios CalDAV, libreta de contactos compartida, normas de uso y resolución de incidencias. | [Descargar PDF](GUIA_NEXTCLOUD_MONDERAS.pdf) |
| [`GUIA_USUARIAS_EQUIPOS_CONTACTOS.md`](GUIA_USUARIAS_EQUIPOS_CONTACTOS.md) | Organización y comisiones | Modelo organizativo de cuentas y comisiones: carpetas compartidas por grupos de trabajo y libreta de contactos vecinales (morralleras y entidades colaboradoras). | [Descargar PDF](GUIA_USUARIAS_EQUIPOS_CONTACTOS.pdf) |
| [`GUIA_SERVIDOR_AUXILIAR_PC.md`](GUIA_SERVIDOR_AUXILIAR_PC.md) | Administración de sistemas | Plan de instalación y puesta en marcha del servidor auxiliar local para servicios de alto consumo (servidor de oficina y videoconferencia integrada). | [Descargar PDF](GUIA_SERVIDOR_AUXILIAR_PC.pdf) |

---

## 🏛️ Ecosistema digital de Lebeche

Este repositorio forma parte del conjunto de desarrollos autogestionados de la asociación:

- **Web oficial:** [lebeche-web](https://github.com/jesuscastilla/lebeche-web) (`https://www.corrientelebeche.es/lebeche/`)
- **Nube asociativa:** [lebeche-nextcloud](https://github.com/jesuscastilla/lebeche-nextcloud) (`https://nube.corrientelebeche.es/`)
- **Infraestructura de correo:** [lebeche-correo](https://github.com/jesuscastilla/lebeche-correo)
- **Barrioteca Acalencá:** [barrioteca-web](https://github.com/jesuscastilla/barrioteca-web) · [barrioteca-android](https://github.com/jesuscastilla/barrioteca-android) · [barrioteca-ios](https://github.com/jesuscastilla/barrioteca-ios) · [barrioteca-slims](https://github.com/jesuscastilla/barrioteca-slims)

---

## 🔒 Política de seguridad y confidencialidad

- Este repositorio es público y contiene documentación de arquitectura funcional y guías de uso.
- **Ninguna credencial**, contraseña en claro, token de API ni clave privada se versiona en Git. Todos los archivos de configuración con credenciales reales (`.env`, `config.php`, certificados TLS) se mantienen aislados en almacenamiento local cifrado o directamente en el servidor.

---

## ✍️ Mantenimiento y actualización

Toda modificación sustancial en la arquitectura de red, políticas de acceso o versiones de software debe quedar reflejada en el documento técnico correspondiente y compilada a su versión PDF equivalente.
