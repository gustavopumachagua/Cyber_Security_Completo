| **Inicio**         | **atrás 12**           |
| ------------------ | ---------------------- |
| [🏠](../README.md) | [⏪](./1_12_iCloud.md) |

---

## **Índice**

| Temario                                      |
| -------------------------------------------- |
| [31. Google Workspace](#31-google-workspace) |

---

# **Google Workspace**

## **31. Google Workspace**

![Google Workspace](/img/1_Fundamental_IT_Skills/Google_Workspace.webp "Google Workspace")

### 1) ¿Qué es Google Workspace?

Google Workspace es la suite de productividad en la nube de Google: correo profesional (Gmail), almacenamiento (Drive), aplicaciones de productividad (Docs, Sheets, Slides), comunicación (Meet, Chat), calendario, herramientas de colaboración y todo el back-end de administración y seguridad que permite usar esos servicios dentro de una organización. Está pensada para trabajo colaborativo en tiempo real desde navegador, móvil o apps nativas.

**Ejemplo rápido:** una PyME usa Gmail con el dominio de la empresa, guarda documentos en Drive, edita un mismo documento de Google Docs en tiempo real con varias personas y organiza reuniones por Meet — todo con la misma cuenta corporativa.

### 2) Apps y servicios clave (qué hacen y ejemplo práctico)

- **Gmail** — correo empresarial con controles de seguridad y políticas. _Ej.: buzón corporativo con alias y reglas de retención._
- **Drive / Shared drives** — almacenamiento y colaboración en archivos; los equipos usan _Shared drives_ para que los archivos pertenezcan al equipo, no a la persona. _Ej.: carpeta de proyecto con acceso por rol._
- **Docs / Sheets / Slides** — edición colaborativa en tiempo real. _Ej.: reportes mensuales coeditados por finanzas y ventas._
- **Meet / Chat / Spaces** — videollamadas, chat y espacios temáticos para proyectos. _Ej.: daily standup por Meet y seguimiento de acciones en un Space._
- **Calendar** — calendarios compartidos, salas y controles de disponibilidad. _Ej.: reservar una sala y reservarla automáticamente en el calendario del equipo._
- **Forms, Sites, Keep** — encuestas, páginas internas y notas rápidas.
- **Admin console** — panel para crear usuarios, aplicar políticas y revisar auditorías.
- **Apps Script / AppSheet** — automatización y apps low-code para extender Workspace; **Apps Script** permite programar flujos (JavaScript) integrados con Gmail/Sheets/Drive.

### 3) Ediciones y licenciamiento (resumen)

Google Workspace ofrece planes pensados para pequeñas, medianas y grandes empresas (Business Starter / Business Standard / Business Plus y varias ediciones Enterprise). Cada plan tiene límites y características (almacenamiento por usuario o agrupado, funciones avanzadas de seguridad, administración de endpoints, retención/archivado). Las diferencias y precios se publican en la web oficial y varían por región y modalidad (anual/flexible).

**Ejemplo:** para una empresa de 15 personas que necesita 2 TB por usuario y grabaciones de Meet, _Business Standard_ suele ser suficiente; una multinacional con requisitos de cumplimiento elegirá alguna edición Enterprise.

### 4) Administración: tareas y herramientas que debes conocer

- **Admin Console:** crear/gestionar usuarios, grupos, Organizational Units (OUs), políticas de seguridad y aprovisionamiento.
- **Audit & Investigation tools / activity logs:** logs de actividad para investigar accesos, cambios y acciones administrativas. Estas herramientas permiten búsquedas y exportación de eventos para auditoría o integración con SIEM.
- **Device & endpoint management:** capacidades para aplicar políticas a móviles y PCs (wipe, passcode, inventario, bloqueo). Las funciones disponibles dependen del plan.
- **Migration services & data migration logs:** Google ofrece herramientas y servicios para migrar correo, calendarios y archivos desde Exchange, Office 365 u otros; los eventos de migración quedan registrados para seguimiento.

**Ejemplo de tarea de admin:** verificar dominio (añadir registro TXT en DNS) → crear 20 usuarios → forzar 2FA obligatorio → configurar Shared Drives y permisos por grupo.

### 5) Seguridad y cumplimiento (principales controles)

- **Identidad y acceso:** integración con Google Entra (Cloud Identity), SAML/SSO, y MFA.
- **Protección de datos:** DLP (Data Loss Prevention) para Drive y Gmail, retención y eDiscovery con Google Vault.
- **Protección de endpoints y gestión móvil** (wipe remoto, políticas de acceso).
- **Monitorización y auditoría:** exportación de logs, alertas y reglas de investigación.
- **Arquitectura y cifrado:** Google cifra datos en tránsito y en reposo y publica controles y whitepapers de seguridad empresarial.

**Ejemplo práctico:** habilitar DLP para bloquear que documentos que contengan números de tarjeta de crédito salgan fuera de la organización por Gmail o compartición externa en Drive.

### 6) Integraciones, automatización y extensibilidad

- **Apps Script:** automatiza tareas (ej.: generar reportes automáticos en Sheets, enviar resúmenes por correo).
  _Snippet de ejemplo (Apps Script) — enviar un correo con un resumen desde una hoja:_

  ```javascript
  function enviarResumen() {
    const hoja =
      SpreadsheetApp.openById("SPREADSHEET_ID").getSheetByName("Resumen");
    const texto = hoja.getRange("A1:A10").getValues().flat().join("\n");
    MailApp.sendEmail("equipo@miempresa.com", "Resumen diario", texto);
  }
  ```

- **AppSheet / Workflows / Marketplace:** crear apps sin código (AppSheet) o añadir apps del Marketplace para CRM, gestión documental, etc.
- **APIs y SDKs:** Admin SDK, Drive API, Gmail API y más para integraciones a medida.

**Ejemplo de uso:** automatizar aprobación de facturas: subir PDF a Drive → AppSheet crea ticket → responsable aprueba desde móvil → Apps Script mueve el archivo a carpeta “Aprobadas” y notifica por Chat.

### 7) Migraciones — enfoque y herramientas

- **Tipos comunes:** migración de correo (Exchange, IMAP), Drive (migración de archivos), y usuarios (sincronización de directorios).
- **Herramientas:** Data Migration Service (Google), Google Workspace Migrate para grandes entornos y soluciones de terceros (BitTitan, CloudM). Google registra eventos y logs para auditar el proceso.

**Ejemplo de plan de migración simple (pyme, 50 usuarios):**

1. Inventario: buzones, archivos y permisos.
2. Verificar dominio y preparar MX.
3. Configurar Data Migration Service y migrar buzones por grupos.
4. Migrar archivos Drive, validar permisos.
5. Formación a usuarios y desconexión del sistema antiguo.

### 8) Casos de uso reales (con ejemplos concretos)

- **Startup (10–50 personas):** Gmail con dominio, Drive compartido por equipos, Meet para vídeo, AppSheet para formularios internos — baja fricción y coste predecible.
- **Escuela / educación:** Classroom + Meet + Drive para tareas y material; controles parentales y políticas de datos para cumplimiento.
- **Corporación (miles de usuarios):** SSO, políticas de acceso condicional, DLP, Vault para retención legal y exportación de logs a SIEM.

### 9) Mejores prácticas y gobernanza

- **Identidad primero:** habilita 2FA/MFA obligatoria y SSO.
- **Principio de menor privilegio:** usa grupos y OUs para delegar permisos, evita dar privilegios globales.
- **Data governance:** políticas DLP, retención, clasificación y clasificación automática cuando sea posible.
- **Respaldo y recuperación:** aunque Workspace es resistente, implementa una estrategia de backup/retención (herramientas terceros o políticas de versión en Drive).
- **Formación y adopción:** plan de onboarding, playbooks para uso seguro y campañas de concienciación.

### 10) Desafíos comunes y cómo mitigarlos

- **Shadow IT / apps del Marketplace:** mitigar con políticas de apps permitidas, whitelisting y revisión periódica.
- **Gestión de almacenamiento y costes:** monitorizar uso de Drive y optimizar políticas de archivo; usar planes adecuados.
- **Migraciones complejas:** planificar y ejecutar por fases, validar permisos y dependencias.
- **Cumplimiento regional:** configurar retención y eDiscovery acorde a la normativa local (p. ej. GDPR).

### 11) Recursos y dónde mantenerse actualizado

- Página oficial y blog de Google Workspace (novedades de producto y anuncios).
- Documentación Admin SDK, Apps Script, y Admin Help (audit logs, endpoint management).

### 12) Mini-checklist rápida para arrancar como administrador (5–10 minutos)

1. Verifica dominio (DNS TXT).
2. Crea 1 usuario admin y 5 usuarios de prueba.
3. Habilita MFA obligatorio y configura políticas de contraseña.
4. Define OUs (por departamento) y aplica políticas por OU.
5. Crea Shared Drives por equipos y define propietarios.
6. Activa audit logs y configura exportación a Cloud Logging/SIEM si corresponde.
7. Programa una sesión de formación básica para usuarios (cómo compartir seguros, usar Meet, manejo de Drive).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 12**           |
| ------------------ | ---------------------- |
| [🏠](../README.md) | [⏪](./1_12_iCloud.md) |
