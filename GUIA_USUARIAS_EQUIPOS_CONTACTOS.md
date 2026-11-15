# Usuarias, Equipos (comisiones) y Contactos — cómo organizar Nextcloud

> Fecha: 15-11-2026 · Nube Lebeche (`https://nube.corrientelebeche.es`)

## 1. Modelo mental (3 conceptos)

| Concepto | Qué es | ¿Puede entrar? | Para qué |
|---|---|---|---|
| **Mondera** | Cuenta de usuaria de Nextcloud | ✅ Sí | Archivos, calendario, correo, tareas… |
| **Comisión** | **Equipo (Team)** + su **carpeta de equipo** | Solo sus miembros | Carpeta propia de la comisión |
| **Morrallera** | **Contacto** en una libreta compartida | ❌ No (no tiene cuenta) | Que su email esté disponible para escribir correos |

- **Grupos** (Monderas, admin): los gestiona la administración; sirven para lo común (calendarios, `MONDERAS`, libreta de contactos).
- **Equipos/Teams**: los gestiona quien los crea; ideales para comisiones.
- Un **grupo "Morralleras"** no aporta nada (los grupos son de usuarias, no de contactos): se puede borrar.

## 2. Estado actual (15-11-2026)

### Cuentas (grupo `Monderas`)
`danna`, `fran`, `ilaria`, `javi`, `paula`, `pelochi`, `ana`, `jose`, `enzo`, `noah`, `dario`, `rodolphe`, `sara`. Administración: `lebeche` y `pelochi`.

Emails configurados: todas menos **jose** (pendiente).

### Equipos (comisiones)

| Equipo | Miembros |
|---|---|
| C. RAÍZ (presidencia) | pelochi, fran, ilaria, javi, paula, danna |
| carpeta general MONDERAS | pelochi, ana, fran, jose, enzo, ilaria, paula |
| E.T. APERTURAS | pelochi, javi, rodolphe, ilaria, fran, danna, ana, paula |
| SOBERANÍA ALIMENTARIA | danna, javi, paula, ilaria |
| C. ACALENCÁ Y LOGÍSTICA | danna, javi, rodolphe |
| C. COMUNICACIÓN, DISEÑO Y ARTE | dario, enzo, ana, paula |
| C. CUIDADOS Y CARIÑITOS | javi, danna |
| FACILITACIÓN DE ASAMBLEAS | rota cada semana (esta semana: pelochi, danna) |
| HEMEROTECA LEBECHE | *(pendiente de miembros)* |
| SUBVERSIONES | *(pendiente de miembros)* |

- **Visibilidad:** los 10 equipos están en modo **visible para todas + solicitud de admisión**
  (`VISIBLE OPEN JOINREQUEST`): cualquier Mondera los ve en la app Equipos y puede pedir
  unirse; la solicitud la confirma un moderador. El contenido de las carpetas solo lo ven
  las miembros.

### Carpetas
- **MONDERAS** (100 GB) → grupo `Monderas` (permisos 31: leer, escribir, crear, borrar,
  compartir): contiene `Actas` (incluida el acta de la asamblea 25-10-2026) y `Fotos`.
- Una **carpeta de equipo** por comisión (50 GB). La del equipo MONDERAS se llama
  `carpeta general MONDERAS`.
- **Libreta de contactos `Morralleras`** (propietaria `lebeche`): 66 contactos importados
  de Roundcube, compartida en **solo lectura** con el grupo `Monderas`.
- Calendarios `lebeche`, `actividades`, `asambleas` compartidos con `Monderas`.

### Correo compartido
- El buzón `monderas@corrientelebeche.es` (Hostalia) está configurado en **todas las
  cuentas** de Monderas. El script `~/scripts/mail-provision.sh` (cron diario 05:15) añade
  el buzón automáticamente a las cuentas nuevas del grupo `Monderas`.

### Talk (reuniones pequeñas)
- Servidor **TURN (coturn)** en el NAS: puertos 3478 TCP/UDP y 50000–50100 UDP abiertos en
  el router hacia `192.168.50.94`. Verificado contra la IP pública el 15-11-2026.
- Sin HPB: adecuado para grupos pequeños (3–8). Para congresos grandes, montar el HPB en un
  servidor auxiliar (ver guía de despliegue).

### Office
- **Desactivado**: EuroOffice/OnlyOffice requiere un Document Server (4 GB de RAM mínimo),
  inviable en el DS223. Se podrá activar si se monta un servidor auxiliar (PC con 16 GB).

## 3. Crear una comisión nueva (paso a paso)

1. App **Equipos** → **Crear equipo** → nombre → añadir miembros → crear.
2. En la página del equipo → **Carpetas de equipo** → **Crear carpeta**.
3. Los miembros ven la carpeta automáticamente (lectura/escritura); el resto no.
4. Para que se pueda pedir admisión: Ajustes del equipo → «Cualquiera puede solicitar
   pertenecer» y «Visible para todos».
5. Para cambiar miembros: página del equipo → añadir/quitar.

> La carpeta de equipo es independiente de cada cuenta: quien sale de la comisión deja de verla; los archivos se quedan.

## 4. Morralleras (correo sin cuenta)

- Datos en **Contactos → libreta `Morralleras`** (solo lectura para las Monderas).
- En **Correo**, al escribir un nombre/email, autocompleta desde las libretas compartidas.
- **No pueden iniciar sesión ni ver archivos**: no existen como usuarias.
- Solo `lebeche` edita la libreta. Para un **envío masivo** (boletín), usar el newsletter
  (`newsletter/lista.csv`), no 60 direcciones en el Para:.

## 5. Pendientes

- [ ] Email de `jose`.
- [ ] Miembros de HEMEROTECA LEBECHE y SUBVERSIONES.
- [ ] Rotación semanal de FACILITACIÓN DE ASAMBLEAS (añadir/quitar en la app Equipos).
- [ ] (Opcional) borrar el grupo `Morralleras` vacío.
- [ ] Revisar los 66 contactos (duplicados / sin email).
- [ ] (Opcional) servidor auxiliar (PC i3 16 GB) para Office + HPB de Talk.

## 6. Preguntas frecuentes

| Pregunta | Respuesta |
|---|---|
| ¿Una Morrallera puede entrar? | No: no tiene cuenta; no aparece en Administración → Usuarios. |
| ¿Puede ver las carpetas? | No. |
| ¿Puedo entrar en una comisión? | Sí: app Equipos → ver todas → «Solicitar unirse»; un moderador la acepta. |
| ¿Y si una Morrallera pasa a Mondera? | Se le crea cuenta y se añade al grupo `Monderas` y, si toca, a su equipo. Su contacto sigue en la libreta. |
| ¿Quién puede crear Equipos? | Cualquier usuaria (abiertos o cerrados). |
| ¿Las carpetas de equipo cuentan en la cuota personal? | No: tienen su propia cuota (50 GB por comisión). |
| ¿Quién ve la carpeta MONDERAS? | Todas las cuentas del grupo `Monderas` (todas las monderas). |