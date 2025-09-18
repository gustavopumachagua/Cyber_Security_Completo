| **Inicio**         | **atrás 10**             | **Siguiente 12**       |
| ------------------ | ------------------------ | ---------------------- |
| [🏠](../README.md) | [⏪](./1_10_Infrared.md) | [⏩](./1_12_iCloud.md) |

---

## **Índice**

| Temario                                                                                              |
| ---------------------------------------------------------------------------------------------------- |
| [28. Directorio de la suite Microsoft Office](#28-directorio-de-la-suite-microsoft-office)           |
| [29. Explicación de cada aplicación de Office 365](#29-explicación-de-cada-aplicación-de-office-365) |

---

# **Microsoft Office Suite**

## **28. Directorio de la suite Microsoft Office**

![Microsoft Office Suite](/img/1_Fundamental_IT_Skills/Microsoft_Office_Suite.jpg "Microsoft Office Suite")

> **Nota:** “Directorio” puede significar varias cosas: la **carpeta de instalación** en el disco, el **servicio de directorio/identidades** (Active Directory / Microsoft Entra ID) que usa Office en empresas, o el **directorio de contactos/agenda** (Global Address List en Exchange/Outlook). Abajo lo tratamos todo.

### 1) Directorio = **carpeta de instalación** (dónde está Office en el disco)

**Qué es:** la ubicación en el sistema de archivos donde están los ejecutables (.exe), librerías y datos locales de Word, Excel, Outlook, etc.

**Rutas típicas (Windows Click-to-Run / Microsoft 365):**

- `C:\Program Files\Microsoft Office\root\Office16\` → rutas comunes para instalaciones Click-to-Run/Office 2016/2019/365.

**Variantes / notas:**

- Algunas instalaciones (p. ej. versiones instaladas desde Microsoft Store) pueden aparecer bajo `C:\Program Files\WindowsApps\...` en vez de la ruta anterior. Esto causa que herramientas administrativas y complementos se comporten distinto.
- En macOS las apps de Office aparecen en la carpeta `Applications` (p. ej. `Microsoft Word.app`). (Instalación típica de Mac).

**Ejemplo práctico (por qué lo necesitas):**

- Si Windows muestra el error “no encuentra `winword.exe`”, comprobar `C:\Program Files\Microsoft Office\root\Office16\WINWORD.EXE` es el primer paso.
- Para empaquetar/desplegar complementos o configurar GPOs, a menudo necesitas conocer la ruta exacta de los ejecutables o la estructura de carpetas.

### 2) Cómo **encontrar** la ruta de instalación (comandos / registro)

**Método rápido (PowerShell — Click-to-Run):**

```powershell
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration' |
  Format-List InstallPath, ClientVersionToReport, ProductReleaseIds
```

Ese registro y valores son la forma más fiable para automatizar reportes de dónde está instalado Office en PCs con Click-to-Run.

**Otras opciones:**

- Buscar `WINWORD.EXE`, `EXCEL.EXE` en `C:\Program Files\...` con `Get-ChildItem`.
- Revisar `Apps & features` (Configuración) o `Programas y características` (Panel de control) para ver la instalación y usar **Modify / Change** si necesitas reparar (ver punto 4).

### 3) Directorio = **servicio de directorio / identidades** (empresa)

**Qué es:** en organizaciones “directorio” suele referirse al sistema que gestiona usuarios, grupos y permisos: tradicionalmente **Active Directory (AD DS)** on-premises y, en nube/Office 365, **Microsoft Entra ID (antes Azure AD)**. Office/Microsoft 365 usa ese directorio para autenticar usuarios, aplicar políticas y controlar accesos.

**Integración típica (ejemplo):**

- Empresa con Active Directory on-premises instala **Microsoft Entra Connect** (DirSync). Entra Connect sincroniza usuarios y permite opciones como Password Hash Sync, Pass-through Auth o Single Sign-On. Así, los empleados inician sesión en Office/Outlook con sus cuentas corporativas.

**Por qué importa:**

- Configurar SSO/Conditional Access, MFA o políticas de acceso condicional requiere entender cómo Office confía en ese directorio.
- Migraciones (on-prem → Microsoft 365) pasan por planificar sincronización de identidades y mapeo de atributos (mail, teléfono, departamentos).

## 4) Directorio = **lista de contactos / agenda (Global Address List - GAL)**

**Qué es:** la **Global Address List (GAL)** es la libreta de direcciones centralizada que muestran Outlook y Exchange; contiene usuarios, buzones, grupos y recursos de la organización. En Exchange Online hay una GAL predeterminada y se pueden crear listas adicionales.

**Ejemplo práctico:**

- Si en Outlook escribes el nombre de un compañero y aparece el correo automáticamente, Outlook está consultando la GAL (o una libreta de direcciones local sincronizada).
- Administrador de Exchange puede crear GALs separadas por departamento (ej.: ventas vs. soporte) para grandes organizaciones.

**Cómo lo usan los usuarios:**

- En Outlook: _Address Book_ → elegir la GAL para buscar direcciones.
- En ambientes híbridos (Exchange on-prem + Exchange Online) hay consideraciones de sincronización al mantener la GAL consistente.

### 5) Administración y tareas comunes (ejemplos y pasos)

#### A) Local (PC individual)

- **Comprobar instalación / ruta:** usar el PowerShell indicado arriba (registro Click-to-Run).
- **Reparar Office si falla:** `Start > Settings > Apps > Microsoft 365 (o Office) > Modify` → Quick Repair / Online Repair. (Windows).

#### B) Administrador (empresa)

- **Inventario de instalaciones:** ejecutar script que consulte la clave `HKLM\SOFTWARE\Microsoft\Office\ClickToRun\Configuration` en todos los equipos y centralice `InstallPath` y `ClientVersionToReport`. (útil para patching/compatibilidad).
- **Identidades / sincronización:** desplegar **Microsoft Entra Connect** para sincronizar AD on-prem con Microsoft Entra ID y habilitar SSO/MFA/Conditional Access.
- **Gestionar GAL / listas de direcciones:** hacerlo desde Exchange Admin Center o por PowerShell de Exchange Online (crear, filtrar por propiedades, delegar).

### 6) Casos reales y ejemplos rápidos

- **Caso usuario doméstico:** Word no inicia → comprobar `C:\Program Files\Microsoft Office\root\Office16\WINWORD.EXE`; si falta, ejecutar _Online Repair_ desde Configuración → Apps.
- **Caso empresa pequeña (AD + Office 365):** sincronizar usuarios con Entra Connect para que empleados usen la misma cuenta para Windows, Outlook y Teams.
- **Caso TI (gestión de contactos):** crear GAL por ubicación (Oficina-Lima, Oficina-Lima-Finanzas) para que usuarios solo vean contactos relevantes; esto se hace en Exchange Online.

### 7) Errores y “gotchas” comunes

- **Office instalado desde Microsoft Store** puede residir bajo `WindowsApps` y complicar detección / permisos para complementos. (verificar tipo de instalación).
- **No puedes cambiar fácilmente la ruta de Click-to-Run:** Microsoft Click-to-Run instala por defecto en el disco del sistema; mover la instalación no es una opción soportada oficialmente (solo soluciones avanzadas).
- **Confusión GAL vs contactos locales:** usuarios pueden tener contactos localmente en Outlook mientras la GAL es administrada centralmente por Exchange; los cambios en la GAL no se editan desde el Outlook normal.

### 8) Checklist rápido para administradores

1. Inventario: localizar `InstallPath` en todos los equipos
2. Clasificar tipos de instalación: Click-to-Run vs Store vs MSI.
3. Mantener parcheo y usar un canal de actualización gestionado (Intune / GPO).
4. Para empresas: planificar Entra Connect / SSO antes de migrar buzones.
5. Gestionar GALs y listas desde Exchange Admin Center (Exchange Online).

---

[🔼](#índice)

---

## **29. Explicación de cada aplicación de Office 365**

### 1) Word — Procesador de textos

**Qué es:** editor de documentos rico en formato (cartas, informes, plantillas).

**Características clave:** revisión de cambios, estilos, tablas, fusión de correspondencia, plantillas, colaboración en tiempo real (web/desktop).

**Ejemplo:** redactar un contrato colaborando con 3 colegas: todos editan simultáneamente, se usan comentarios y la historial de versiones para volver atrás.

**Tip:** usa estilos predefinidos para crear un índice automático y mantener consistencia.

### 2) Excel — Hojas de cálculo

**Qué es:** cálculo, análisis de datos, tablas dinámicas y gráficos.

**Características clave:** fórmulas, tablas dinámicas, Power Query (importar/transformar), Power Pivot (modelado), macros/VBA, colaboración en la nube.

**Ejemplo:** consolidar ventas mensuales desde varios CSV con Power Query, luego crear un Panel con gráficos dinámicos.

**Tip:** para datos complejos automatiza la importación con Power Query en lugar de copiar/pegar.

### 3) PowerPoint — Presentaciones

**Qué es:** crear presentaciones visuales (diapositivas) para clases, pitch, informes.

**Características clave:** plantillas, transiciones, notas del presentador, exportar a vídeo, colaboración en línea.

**Ejemplo:** preparar una presentación comercial con diapositivas master, exportarla a PDF y compartir el enlace.

**Tip:** usa las diapositivas de resumen y el modo «Presentador» para controlar tiempos y notas.

### 4) Outlook — Correo y calendario

**Qué es:** cliente de correo, calendario, contactos y tareas; front-end para Exchange/Microsoft 365.

**Características clave:** reglas, bandeja enfocada, calendarios compartidos, tareas/flags, integración con Teams/OneNote.

**Ejemplo:** programar una reunión con sala y asistentes, ver disponibilidad y enviar convocatoria con un canal de Teams adjunto.

**Tip:** usa reglas y carpetas automáticas para mantener la bandeja ordenada; conecta To Do para gestionar tareas.

### 5) OneDrive (for Business) — Almacenamiento personal en la nube

**Qué es:** almacenamiento en la nube para archivos personales y sincronización entre dispositivos.

**Características clave:** sincronización selectiva, historial de versiones, compartir enlaces con permisos, integración con Office Online.

**Ejemplo:** guardar tu informe en OneDrive y compartir un enlace con edición restringida a tu equipo.

**Tip:** activa «Archivos a demanda» si tu disco está limitado.

### 6) SharePoint — Sitios, intranet y bibliotecas de documentos

**Qué es:** plataforma para crear sitios internos, bibliotecas documentales y soluciones de colaboración empresarial.

**Características clave:** sitios de equipo/público, bibliotecas con controles de versiones, páginas intranet, flujos de aprobación, listas personalizadas.

**Ejemplo:** construir un sitio de proyecto con lista de tareas, biblioteca de documentos y un panel con Power BI embebido.

**Tip:** usa SharePoint como backend para Teams: los archivos de un canal se guardan en una biblioteca de SharePoint.

### 7) Teams — Comunicación y colaboración unificada

**Qué es:** chat, llamadas, reuniones, y hub para colaborar con archivos y apps integradas.

**Características clave:** canales, reuniones, uso compartido de pantalla, apps integradas (Planner, OneNote, Power BI), grabación/transcripción.

**Ejemplo:** crear un canal por proyecto donde se discute por chat, se hacen reuniones semanales y se comparten entregables en la pestaña de archivos.

**Tip:** organiza canales por tema/función y usa etiquetas para notificar a grupos específicos.

### 8) Exchange (Exchange Online) — Correo/servicios de mensajería empresarial

**Qué es:** servidor de correo/agenda profesionales detrás de Outlook; gestiona buzones, reglas, políticas de retención.

**Características clave:** buzones, reglas de transporte, políticas DLP, retención legal.

**Ejemplo:** administrador configura una política de retención de correos para cumplimiento regulatorio.

**Tip:** usa políticas de retención y DLP para proteger información sensible en correos.

### 9) OneNote — Toma de notas

**Qué es:** cuadernos digitales con secciones/páginas para notas, recortes web y colaboración.

**Características clave:** sincronización, búsqueda OCR, inserción de imágenes/audio, integración con Outlook/Teams.

**Ejemplo:** tomar notas de una reunión, incrustar la agenda y marcar tareas que se sincronizan con Outlook.

**Tip:** crea una estructura de cuadernos por proyectos y usa etiquetas para localizar tareas rápidamente.

### 10) Planner — Gestión de tareas en equipo (kanban simple)

**Qué es:** tablero Kanban para asignar tareas dentro de equipos (ligero, integrado con Teams).

**Características clave:** buckets, asignación, fechas, checklist, vista gráfica.

**Ejemplo:** equipo de marketing planifica campaña: columnas para “Pendiente / En progreso / Listo”.

**Tip:** para tareas personales sincroniza Planner con To Do (si está habilitado).

### 11) To Do — Tareas personales

**Qué es:** lista de tareas personales con integración a Outlook (flags → tareas).

**Características clave:** My Day, listas, recordatorios, sincronización móvil.

**Ejemplo:** convertir emails importantes en tareas y planificarlas en My Day.

**Tip:** usa To Do para trabajo diario y Planner para tareas de equipo.

### 12) Forms — Formularios y encuestas

**Qué es:** crear encuestas, cuestionarios y formularios con respuestas automáticas.

**Características clave:** tipos de pregunta, analítica simple, exportación a Excel, integración en Teams/SharePoint.

**Ejemplo:** encuesta de satisfacción tras un curso, con análisis automático de respuestas.

**Tip:** protege formularios sensibles con autenticación (solo usuarios de tu organización).

### 13) Power Automate — Automatización de flujos (anteriormente Flow)

**Qué es:** construir flujos de trabajo automatizados sin código (o con poco código) entre apps y servicios.

**Características clave:** conectores (SharePoint, Outlook, Teams, OneDrive, apps externas), disparadores, condiciones.

**Ejemplo:** recibir un correo con adjunto -> guardar en SharePoint -> notificar canal de Teams.

**Tip:** usa plantillas para empezar y monitoriza ejecuciones para depurar fallos.

### 14) Power Apps — Construir aplicaciones low-code

**Qué es:** crear aplicaciones empresariales sin programar intensivamente; se conectan a datos en SharePoint, Dataverse, SQL, etc.

**Características clave:** canvas apps, model-driven apps, conectores, publicación para web/móvil.

**Ejemplo:** app para registro de incidencias que escribe en una lista de SharePoint y notifica por Teams.

**Tip:** empieza con Power Apps para formularios personalizados antes de invertir en desarrollo a medida.

### 15) Power BI — Visualización y análisis de datos

**Qué es:** plataforma de BI para crear dashboards y reportes interactivos.

**Características clave:** Power Query, modelos de datos, DAX, dashboards en la nube, compartir con permisos.

**Ejemplo:** dashboard de ventas con filtros por región y métricas calculadas.

**Tip:** usa modelos simplificados y medidas DAX reutilizables para rendimiento.

### 16) Visio — Diagramas y procesos

**Qué es:** crear diagramas de flujo, organigramas, planos y arquitectura.

**Características clave:** plantillas, formas inteligentes, colaboración en línea (Visio for web).

**Ejemplo:** diagrama de arquitectura de red para presentar al equipo de infra.

**Tip:** usar formas conectadas con datos para diagramas dinámicos.

### 17) Access — Base de datos de escritorio (Windows)

**Qué es:** gestor de bases de datos relacionales para aplicaciones de usuario con interfaz (solo Windows).

**Características clave:** tablas, consultas, formularios, informes, macros.

**Ejemplo:** pequeña app de inventario local para una oficina que no necesita servidor SQL.

**Tip:** migrar a SQL Server/Dataverse cuando la app crezca o requiera acceso concurrente robusto.

### 18) Yammer / Viva Engage — Red social empresarial

**Qué es:** red social interna para comunicación amplia y comunidades (Yammer → Viva Engage).

**Características clave:** comunidades, anuncios, Q\&A, integración con Viva.

**Ejemplo:** comunidad global de empleados comparte buenas prácticas y noticias corporativas.

**Tip:** fomentar su uso para cultura organizacional y difusión de noticias.

### 19) Stream (on SharePoint) — Vídeo empresarial

**Qué es:** plataforma para almacenar y compartir vídeos corporativos (ahora integrado con SharePoint/OneDrive).

**Características clave:** subida de vídeo, transcripciones, permisos, integración con Teams.

**Ejemplo:** grabación de capacitación almacenada y protegida para empleados.

**Tip:** gestiona permisos y usa transcripciones para accesibilidad y búsqueda.

### 20) Forms Pro / Dynamics 365 Customer Voice — Encuestas avanzadas

**Qué es:** versiones avanzadas de Forms para feedback con integraciones CRM/analytics.

**Ejemplo:** encuesta postventa que alimenta un tablero de satisfacción en Dynamics/Power BI.

**Tip:** segmenta encuestas y automatiza flujos de respuesta con Power Automate.

### 21) Bookings — Reservas y citas

**Qué es:** sistema para agendar citas (salones, consultorios, soporte) con calendario público.

**Ejemplo:** consultorio médico ofrece reservas online con sincronización a calendarios del personal.

**Tip:** personaliza horarios y políticas de cancelación para optimizar la logística.

### 22) Microsoft Defender for Office 365 — Seguridad de correo y colaboración

**Qué es:** protección contra phishing, malware y amenazas en correo y colaboración.

**Características clave:** protección contra enlaces maliciosos, sandboxing, Safe Attachments, Safe Links.

**Ejemplo:** bloquear un correo con adjunto sospechoso y ponerlo en cuarentena automáticamente.

**Tip:** combina Defender con políticas DLP y MFA para mayor defensa en profundidad.

### 23) Intune — Gestión de dispositivos y aplicaciones (MDM/MAM)

**Qué es:** administrar dispositivos móviles y PCs, políticas, deploy de apps y protección de datos.

**Ejemplo:** desplegar configuraciones de correo corporativo y exigir PIN en dispositivos móviles.

**Tip:** usar Intune con Azure AD Conditional Access para proteger recursos según estado del dispositivo.

### 24) Azure Active Directory (Microsoft Entra ID) — Identidad y acceso

**Qué es:** servicio de identidades para iniciar sesión en Microsoft 365 y apps SaaS.

**Características clave:** SSO, MFA, Conditional Access, gestión de identidades.

**Ejemplo:** exigir MFA para acceso desde redes públicas y permitir SSO a apps internas.

**Tip:** habilita MFA y revisa políticas de acceso condicional por riesgo.

#### Cómo se integran todas estas apps (resumen práctico)

- **Files:** OneDrive (personal) + SharePoint (equipo) → Teams usa SharePoint/OneDrive para archivos.
- **Comunicación:** Teams es el hub; Outlook/Exchange sigue siendo el correo corporativo.
- **Automatización/Apps:** Power Automate / Power Apps / Power BI conectan datos y procesos entre apps.
- **Seguridad & Gestión:** Defender, Intune y Azure AD protegen y controlan acceso/endpoint.

#### Consejos finales y mejores prácticas

1. **Organiza por propósito:** usa OneDrive para trabajo individual y SharePoint/Teams para trabajo en equipo.
2. **Protege identidad y datos:** MFA, Conditional Access, DLP y cifrado.
3. **Automatiza tareas repetitivas** con Power Automate.
4. **Capacita a equipos** en Teams + uso compartido seguro de documentos.
5. **Planifica licencias**: algunas apps (Power BI Pro, Visio, Access) pueden requerir licencias adicionales.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 10**             | **Siguiente 12**       |
| ------------------ | ------------------------ | ---------------------- |
| [🏠](../README.md) | [⏪](./1_10_Infrared.md) | [⏩](./1_12_iCloud.md) |
