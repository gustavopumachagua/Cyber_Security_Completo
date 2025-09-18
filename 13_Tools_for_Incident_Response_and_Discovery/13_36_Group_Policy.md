| **Inicio**         | **atrás 35**                   | **Siguiente 37**           |
| ------------------ | ------------------------------ | -------------------------- |
| [🏠](../README.md) | [⏪](./13_35_Port_Blocking.md) | [⏩](./13_37_Sinkholes.md) |

---

## **Índice**

| Temario                                                                                                                              |
| ------------------------------------------------------------------------------------------------------------------------------------ |
| [481. Descripción general de la política de grupo](#481-descripción-general-de-la-política-de-grupo)                                 |
| [482. ¡Aprenda la directiva de grupo de Windows de forma sencilla!](#482-aprenda-la-directiva-de-grupo-de-windows-de-forma-sencilla) |

# **Group Policy**

## **481. Descripción general de la política de grupo**

![Group Policy](/img/13_Tools_for_Incident_Response_and_Discovery/Group_Policy.webp "Group Policy")

La **Directiva de Grupo (Group Policy, GPO)** es una característica de Microsoft Windows que permite a los administradores centralizar la **gestión de configuraciones, seguridad y políticas** en una red basada en **Active Directory (AD)**.

En pocas palabras:

👉 Es un **mecanismo de control centralizado** para aplicar configuraciones en usuarios y equipos, sin tener que configurarlos manualmente uno por uno.

**Ejemplo sencillo:**

Una empresa quiere que todos los usuarios:

- Tengan el mismo fondo de pantalla corporativo.
- No puedan instalar programas sin permisos.
- Tengan contraseñas que expiren cada 90 días.

En lugar de hacerlo PC por PC, el administrador lo configura en una **GPO** → Active Directory se encarga de aplicarlo automáticamente a todos los equipos/usuarios según reglas definidas.

### ⚙️ Descripción de la función

La Directiva de Grupo permite:

1. **Configurar ajustes de seguridad:** políticas de contraseña, bloqueo de cuentas, firewall, etc.
2. **Administrar software:** instalar, actualizar o restringir aplicaciones.
3. **Controlar la experiencia del usuario:** escritorio, menú inicio, accesos directos.
4. **Restringir funciones del sistema:** deshabilitar acceso al panel de control, impedir acceso a USB, etc.
5. **Definir scripts:** ejecutar scripts de inicio/apagado de equipos o inicio/cierre de sesión de usuarios.

👉 Todo esto se aplica **por unidad organizativa (OU)**, **grupos de usuarios** o **equipos** en un dominio AD.

### 🛠️ Aplicaciones prácticas (con ejemplos)

#### 🔹 1. Seguridad

- **Políticas de contraseñas:** longitud mínima, complejidad, caducidad.
- **Bloqueo automático de sesiones:** cerrar sesión tras X minutos de inactividad.
- **Restringir acceso a panel de control** para usuarios estándar.

#### 🔹 2. Control de software

- Instalar Office automáticamente en todos los equipos de la red.
- Bloquear el uso de navegadores no autorizados.
- Restringir programas como juegos o software no corporativo.

#### 🔹 3. Configuración del entorno de trabajo

- Establecer un fondo de pantalla institucional.
- Colocar accesos directos a carpetas compartidas en el escritorio.
- Configurar impresoras de red predeterminadas para cada departamento.

#### 🔹 4. Auditoría y monitoreo

- Habilitar registros de seguridad en todos los equipos.
- Forzar que todos los eventos críticos se envíen al servidor SIEM.

**Ejemplo práctico:**

Una universidad quiere que:

- Los alumnos tengan acceso solo a navegadores y Office.
- Los profesores puedan instalar apps adicionales.

  Se crean dos **GPOs diferentes** aplicadas a las OU correspondientes (Alumnos y Profesores).

### 🆕 Funcionalidad nueva y modificada (versiones modernas de Windows Server)

Con cada versión de **Windows Server** (2012, 2016, 2019, 2022) se añaden mejoras en Group Policy. Algunas son:

#### ✅ Nuevas funcionalidades

- **Soporte para configuraciones de Windows Defender y Exploit Guard.**
- **Administración de actualizaciones (Windows Update for Business).**
- **Control de dispositivos USB y periféricos más granular.**
- **Soporte para directivas de Microsoft Edge y apps modernas de Windows 10/11.**

#### ✅ Funcionalidad modificada

- **Inicio de sesión más rápido** al optimizar la aplicación de GPOs.
- **Mejor integración con la nube (Azure AD + Intune).**
- **Plantillas administrativas actualizadas** para nuevas versiones de Windows.
- **Deprecación de configuraciones obsoletas** (ej. Internet Explorer en favor de Edge).

### 🎯 Resumen

- **La Directiva de Grupo (GPO)** es la herramienta de Microsoft para **centralizar la gestión de configuraciones y seguridad** en entornos de Active Directory.
- Se usa para **seguridad, control de software, personalización y automatización**.
- Ha evolucionado con nuevas funciones, como soporte para Windows 10/11, Edge, Intune y políticas avanzadas de seguridad.
- Es esencial en **empresas, universidades y organizaciones grandes**, donde administrar cada PC manualmente sería imposible.

---

[🔼](#índice)

---

## **482. ¡Aprenda la directiva de grupo de Windows de forma sencilla!**

### 1) ¿Qué es Group Policy (Directiva de Grupo)?

Las **Directivas de Grupo (GPOs)** permiten a un administrador aplicar configuraciones (seguridad, escritorio, scripts, instalación de software, firewall, etc.) **centralmente** sobre equipos y usuarios en un dominio Active Directory.

En vez de configurar cada PC manualmente, creas una GPO, la enlazas a una OU (Unidad Organizativa) y AD la aplica automáticamente.

### 2) Conceptos clave que debes conocer

- **GPO**: objeto que contiene configuraciones.
- **GPMC**: Group Policy Management Console — consola para crear/editar/administar GPOs.
- **GPO Editor** (`gpedit.msc` cuando es local o el editor integrado en GPMC) — dónde se configuran las políticas.
- **OU (Organizational Unit)**: contenedor AD donde enlazas GPOs.
- **Scope (alcance)**: qué usuarios/ordenadores reciben la GPO (por enlace a OU, filtrado de seguridad, WMI filter).
- **Precedencia / Orden**: orden de aplicación LSDOU → Local, Site, Domain, OU (y GPOs aplicados más abajo pueden sobrescribir).
- **Block Inheritance** (bloquear herencia) y **Enforced/No override** (hacer que una GPO sea “no anulable”).
- **Security Filtering**: limitar quién aplica la GPO usando grupos.
- **WMI Filter**: aplicar solo si el equipo cumple condiciones (ej. Windows 10).
- **Loopback Processing**: hace que las GPO de máquina influyan sobre la configuración de usuario (útil en kioscos/terminal servers). Modo _Merge_ o _Replace_.

### 3) ¿Cómo crear y enlazar una GPO? — pasos básicos (GUI)

1. Abre **Group Policy Management** (GPMC) en un DC o máquina con RSAT.
2. En el árbol: clic derecho en **Group Policy Objects** → **New** → ponle un nombre claro (ej. `GPO_Wallpaper_Corporativa`).
3. Clic derecho sobre la GPO → **Edit** → se abrirá el Group Policy Editor (dividido en Computer Configuration y User Configuration).
4. Define la(s) configuración(es) que quieras (ver ejemplos más abajo).
5. En GPMC, selecciona la OU donde quieres aplicar la GPO → clic derecho → **Link an existing GPO** → elige tu GPO.
6. En GPMC puedes ajustar **Security Filtering** y **WMI Filters** si quieres refinar el alcance.
7. En un cliente, ejecuta `gpupdate /force` para forzar la aplicación de políticas inmediatamente.

### 4) Ejemplos prácticos paso a paso (configuraciones frecuentes)

#### Ejemplo A — Bloquear el Panel de Control (para usuarios)

**Ruta en el editor:**

`User Configuration → Policies → Administrative Templates → Control Panel → Prohibit access to Control Panel and PC settings → Enabled`
Efecto: usuarios no verán el Panel de Control ni los ajustes del sistema.

#### Ejemplo B — Forzar fondo de escritorio corporativo (wallpaper)

**Opción sencilla (Administrative Template):**
`User Configuration → Policies → Administrative Templates → Desktop → Desktop → Desktop Wallpaper` → **Enabled**, especifica ruta UNC del archivo (p. ej. `\\srvfiles\NETLOGON\wallpaper.jpg`).

> Consejo: coloca la imagen en un share con lectura para _Autheticated Users_: `\\servidor\netlogon\wallpaper.jpg`.

#### Ejemplo C — Deshabilitar almacenamiento USB (removable drives)

**Ruta recomendada (Administrative Template):**
`Computer Configuration → Policies → Administrative Templates → System → Removable Storage Access → All Removable Storage classes: Deny all access → Enabled`

Efecto: bloquea acceso a dispositivos extraíbles a nivel máquina.

#### Ejemplo D — Configurar Windows Update automático

`Computer Configuration → Policies → Administrative Templates → Windows Components → Windows Update → Configure Automatic Updates → Enabled`

Configuras el modo (p.ej. 4 — Auto download and schedule the install) y horario.

#### Ejemplo E — Instalar software mediante GPO (MSI)

1. Copia `app.msi` a un share UNC accesible (ej. `\\fileserver\packages\app.msi`).
2. En GPO: `Computer Configuration → Policies → Software Settings → Software installation → New → Package` y selecciona la ruta UNC.
3. Elige _Assigned_ para que se instale en inicio del equipo (recomendado para instalaciones a nivel equipo).

### 5) Filtrado y alcance: Security Filtering y WMI Filters

- **Security Filtering (filtrado por seguridad)**: por defecto GPO aplica a `Authenticated Users`. Para aplicarlo solo a un grupo: deja `Authenticated Users` (Read) y añade el grupo A al que quieres aplicarlo; asigna permiso **Apply Group Policy** al grupo.
- **WMI Filter**: ejemplo para aplicar solo en Windows 10:

  ```sql
  SELECT * FROM Win32_OperatingSystem WHERE Version LIKE "10.%" AND ProductType = "1"
  ```

  Enlaza el filtro a la GPO desde GPMC.

### 6) Loopback processing — cuándo usarlo y modos

- **¿Qué hace?**: cuando está habilitado en una GPO de equipo, modifica cómo se aplican las GPO de usuario.
- **Modos**:

  - **Merge**: aplican las GPO de usuario normales y luego se “mezclan” con las del equipo; en conflicto, la del equipo gana.
  - **Replace**: las GPO de usuario se _reemplazan_ por las definidas bajo la GPO del equipo.

- **Uso típico**: Terminal Servers, kioscos, aulas (donde el equipo determina las políticas del usuario).

### 7) Comandos y herramientas de diagnóstico (imprescindibles)

- **Forzar actualización:**

  ```powershell
  gpupdate /force
  ```

- **Ver qué GPOs aplicaron (resumen):**

  ```powershell
  gpresult /r
  ```

- **Generar reporte HTML (útil para auditoría):**

  ```powershell
  gpresult /h c:\temp\gp-report.html
  ```

- **RSoP (Resultant Set of Policy) – modo consola:**

  ```powershell
  rsop.msc
  ```

- **Ver eventos de Group Policy:** Event Viewer → `Applications and Services Logs → Microsoft → Windows → GroupPolicy`
- **Comprobar SYSVOL y replicación**: GPOs físicos están en `\\<DOMAIN>\SYSVOL\<DOMAIN>\Policies\{GUID}`. (Si los archivos no replican, los clientes no verán la GPO).

### 8) Troubleshooting común (si la GPO no se aplica)

1. Ejecuta `gpupdate /force` y luego `gpresult /r` en cliente.
2. Revisa permisos del GPO en GPMC (Read + Apply).
3. Asegura que el equipo/usuario está en la OU que tiene el enlace de la GPO.
4. Comprueba replicación AD/SYSVOL entre controladores de dominio (repadmin/dfs?).
5. Revisa event logs del cliente y `Group Policy Operational` log.
6. Verifica time sync (NTP) entre DC y cliente (Kerberos requiere timestamps sincronizados).
7. Si usas Security Filtering, asegúrate de que el grupo tenga permiso para Read y Apply.

### 9) Buenas prácticas (imprescindibles)

- **Nombres claros**: `GPO-Dept-Workstation-Win10` en vez de `GPO1`.
- **Documenta la GPO**: descripción que indique propósito, quién la creó y la fecha.
- **Testing**: primero enlaza la GPO a un OU de pruebas.
- **Poca cantidad, más lógica**: evita crear 1000 GPOs; agrupa configuraciones coherentes.
- **Default Domain Policy**: úsala solo para policies de dominio (contraseñas); evita mezclar cientos de settings ahí.
- **Central Store para ADMX**: mantener plantillas administrativas en `\\<domain>\SYSVOL\<domain>\Policies\PolicyDefinitions` para que todos los administradores vean las mismas ADMX.
- **Backup/Versionado**: exporta GPOs críticos (GPMC → Back Up).
- **Control de cambios**: usa tickets/registro cuando cambias una GPO (rollbacks sencillos).
- **Delegación mínima**: solo admins que necesiten editar GPO deben tener permisos.

### 10) Ejemplo de flujo real (deploy y rollback)

1. Crear GPO `GPO_DisableUSB_Test`.
2. Enlazar a OU_Test.
3. Configurar `Removable Storage Access -> Deny`.
4. Forzar `gpupdate /force` en clientes de OU_Test y verificar con `gpresult /r`.
5. Si OK, mover a OU_Prod (o enlazar a más OUs) y documentar.
6. Si causa problemas, deshacer (unlink) o editar la GPO; puedes restaurar backup antiguo desde GPMC.

### 11) Seguridad de GPOs — por qué importan

Una GPO puede ejecutar scripts con privilegios, instalar software o cambiar políticas de seguridad. **Controla quién puede editar/crear GPOs**. Revisa la ACL del GPO (GPMC → Delegation → Advanced) y evita dar permisos excesivos.

### 12) Glosario rápido (para tener a mano)

- **AD**: Active Directory.
- **OU**: Unidad Organizativa.
- **GPMC**: Consola de administración de Directivas de Grupo.
- **ADMX/ADML**: plantillas administrativas y archivos de idioma (formatos modernos).
- **SYSVOL**: carpeta compartida replicada donde GPOs almacena scripts/plantillas.
- **RSOP**: Resultado de la Política de Grupo aplicada.
- **Loopback**: modo para aplicar políticas de usuario desde GPOs de equipo.

### 13) Cheatsheet rápido — comandos y rutas

```text
gpupdate /force            # fuerza actualización de políticas
gpresult /r                # resumen de GPO aplicadas
gpresult /h report.html    # report en HTML
rsop.msc                   # RSoP MMC snap-in
GPMC                      # Abrir Group Policy Management (GUI)
SYSVOL path: \\domain\SYSVOL\<domain>\Policies\{GUID}\
Central Store: \\domain\SYSVOL\<domain>\Policies\PolicyDefinitions\
```

---

[🔼](#índice)

---

| **Inicio**         | **atrás 35**                   | **Siguiente 37**           |
| ------------------ | ------------------------------ | -------------------------- |
| [🏠](../README.md) | [⏪](./13_35_Port_Blocking.md) | [⏩](./13_37_Sinkholes.md) |
