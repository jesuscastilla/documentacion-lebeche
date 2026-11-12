# Usuarias, Equipos (comisiones) y Contactos — cómo organizar Nextcloud

> Fecha: 12-11-2026 · Nube Lebeche (`https://nube.corrientelebeche.es`)

## 1. Modelo mental (3 conceptos)

| Concepto | Qué es | ¿Puede entrar? | Para qué |
|---|---|---|---|
| **Mondera** | Cuenta de usuaria de Nextcloud | ✅ Sí | Archivos, calendario, correo, tareas… |
| **Comisión** | **Equipo (Team)** + su **carpeta de equipo** | Solo sus miembros | Carpeta propia de la comisión |
| **Morrallera** | **Contacto** en una libreta compartida | ❌ No (no tiene cuenta) | Que su email esté disponible para escribir correos |

- **Grupos** (Monderas, admin): los gestiona la administración; sirven para lo común (calendarios, `Documentos`, libreta de contactos).
- **Equipos/Teams**: los gestiona quien los crea; ideales para comisiones.
- Un **grupo "Morralleras"** no aporta nada (los grupos son de usuarias, no de contactos): se puede borrar.

## 2. Estado actual (12-11-2026)

### Cuentas (grupo `Monderas`)
`danna`, `fran`, `ilaria`, `javi`, `paula`, `pelochi`, `ana`, `jose`, `enzo`, `noah`, `dario`, `rodolphe`, `sara`. Administración: `lebeche` y `pelochi`.

Emails configurados: pelochi, javi, fran, ilaria, ana, noah, dario, rodolphe, sara.
**Pendientes de email:** danna, paula, jose, enzo.

### Equipos (comisiones)

| Equipo | Miembros |
|---|---|
| C. RAÍZ (presidencia) | Fran, Ilaria, Javi, Paula, Danna, Pelochi |
| MONDERAS | Pelochi, Ana, Fran, Jose, Enzo, Ilaria, Paula |
| E.T. APERTURAS | Pelochi, Javi, Rodolphe, Ilaria, Fran, Danna, Ana, Paula |
| HEMEROTECA LEBECHE | *(pendiente de miembros)* |
| SOBERANÍA ALIMENTARIA | *(pendiente)* |
| SUBVERSIONES | *(pendiente)* |
| C. ACALENCÁ Y LOGÍSTICA | *(pendiente)* |
| C. CUIDADOS Y CARIÑITOS | *(pendiente)* |
| C. COMUNICACIÓN, DISEÑO Y ARTE | *(pendiente)* |
| FACILITACIÓN DE ASAMBLEAS | *(pendiente)* |

### Carpetas de equipo
Una por equipo (asignada al equipo automáticamente), con cuota de **50 GB** cada una
(excepto `C. RAÍZ`, sin cuota). Los miembros del equipo la ven en **Archivos**.

- **Documentos** (100 GB) → grupo `Monderas`: contiene `Actas` (1,4 GB) y `Fotos` (4 GB).
- **Libreta de contactos `Morralleras`** (propietaria `lebeche`): 66 contactos importados
  de Roundcube, compartida en **solo lectura** con el grupo `Monderas`.
- Calendarios `lebeche`, `actividades`, `asambleas` compartidos con `Monderas`.

## 3. Crear una comisión nueva (paso a paso)

1. App **Equipos** → **Crear equipo** → nombre → añadir miembros → crear.
2. En la página del equipo → **Carpetas de equipo** → **Crear carpeta**.
3. Los miembros ven la carpeta automáticamente (lectura/escritura); el resto no.
4. Para cambiar miembros: página del equipo → añadir/quitar.

> La carpeta de equipo es independiente de cada cuenta: quien sale de la comisión deja de verla; los archivos se quedan.

## 4. Morralleras (correo sin cuenta)

- Datos en **Contactos → libreta `Morralleras`** (solo lectura para las Monderas).
- En **Correo**, al escribir un nombre/email, autocompleta desde las libretas compartidas.
- **No pueden iniciar sesión ni ver archivos**: no existen como usuarias.
- Solo `lebeche` edita la libreta. Para un **envío masivo** (boletín), usar el newsletter
  (`newsletter/lista.csv`), no 60 direcciones en el Para:.

## 5. Pendientes

- [ ] Emails de `danna`, `paula`, `jose`, `enzo`.
- [ ] Miembros de las 7 comisiones sin asignar.
- [ ] (Cosmético) la carpeta del equipo MONDERAS se llama "MONDERAS (2)": renombrarla
      desde la app Equipos si molesta.
- [ ] Revisar los 66 contactos (duplicados / sin email).
- [ ] (Opcional) borrar el grupo `Morralleras` vacío y el equipo/carpeta de prueba si lo hubiera.

## 6. Preguntas frecuentes

| Pregunta | Respuesta |
|---|---|
| ¿Una Morrallera puede entrar? | No: no tiene cuenta; no aparece en Administración → Usuarios. |
| ¿Puede ver las carpetas? | No. |
| ¿Y si una Morrallera pasa a Mondera? | Se le crea cuenta y se añade al grupo `Monderas` y, si toca, a su equipo. Su contacto sigue en la libreta. |
| ¿Dónde se ven los Equipos? | Icono **Equipos** en la barra superior (app Teams). |
| ¿Quién puede crear Equipos? | Cualquier usuaria (abiertos o cerrados). |
| ¿Las carpetas de equipo cuentan en la cuota personal? | No: tienen su propia cuota (50 GB por comisión). |