| **Inicio**         | **atrás 6**                         | **Siguiente 8**                               |
| ------------------ | ----------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_6_Understand_CIA_Triad.md) | [⏩](./8_8_Web_Based_Attacks_and_OWASP_10.md) |

---

## **Índice**

| Temario                                                                            |
| ---------------------------------------------------------------------------------- |
| [211. ¿Qué es la escalada de privilegios?](#211-qué-es-la-escalada-de-privilegios) |

# **Privilege escalation**

## **211. ¿Qué es la escalada de privilegios?**

![Privilege escalation](/img/8_Security_Skills_and_Knowledge/Privilege_escalation.webp "Privilege escalation")

### 📌 ¿Qué es la escalada de privilegios?

La **escalada de privilegios** es una técnica utilizada por atacantes (externos o internos) para **ganar más permisos o derechos de los que deberían tener** dentro de un sistema, aplicación o red.

- Normalmente empieza con un acceso limitado (usuario básico o cuenta comprometida).
- El atacante aprovecha vulnerabilidades o configuraciones erróneas para obtener **mayores privilegios**, como permisos de administrador o root.

👉 **Ejemplo sencillo:**

Un empleado con acceso solo a su buzón de correo explota un fallo en el sistema y consigue acceso completo al servidor de correos de toda la empresa.

### ⚙️ ¿Cómo funciona la escalada de privilegios?

1. **Acceso inicial:** el atacante entra al sistema (ejemplo: phishing, credenciales débiles, explotación web).
2. **Enumeración:** analiza qué permisos tiene y qué debilidades existen.
3. **Explotación:** aprovecha vulnerabilidades, errores de configuración o ingeniería social.
4. **Elevación de privilegios:** consigue permisos más altos.
5. **Acciones maliciosas:** roba datos, instala malware, crea usuarios ocultos, mantiene persistencia.

### 🛠️ Técnicas de escalada de privilegios

Algunas técnicas comunes:

- **Explotación de vulnerabilidades:** bugs en el sistema operativo o aplicaciones que permiten ejecutar código con más privilegios.
- **Errores de configuración:** contraseñas por defecto, permisos mal asignados, servicios inseguros.
- **Inyección de código:** SQLi, RFI, o explotación en aplicaciones mal programadas.
- **Pass-the-Hash / Pass-the-Ticket:** robo y reutilización de credenciales en entornos Windows.
- **Bypass de UAC (Windows):** ejecutar aplicaciones con permisos elevados sin autorización.

### 🔑 Tipos principales de escalada de privilegios

#### 1. Escalada de privilegios **vertical**

- Consiste en pasar de un rol **bajo a uno alto**.
- Ejemplo: un usuario normal explota un fallo y se convierte en administrador/root.

#### 2. Escalada de privilegios **horizontal**

- No incrementa el nivel de permisos, pero permite acceder a **recursos de otro usuario del mismo nivel**.
- Ejemplo: un cliente de un banco accede a la cuenta de otro cliente modificando parámetros en la URL.

### ➕ Más tipos de técnicas

- **Explotación local vs. remota:**

  - Local: se requiere acceso previo al sistema.
  - Remota: el atacante logra escalar sin acceso físico, por internet o red.

- **Persistencia mediante servicios o tareas programadas:** crear backdoors con privilegios altos.
- **Uso de credenciales expuestas en repositorios (GitHub, scripts, logs).**

### 🛡️ Estrategias para prevenir la escalada de privilegios

1. **Principio de mínimo privilegio (PoLP):** cada usuario debe tener solo los permisos necesarios.
2. **Parcheo y actualizaciones:** mantener sistemas y aplicaciones al día.
3. **Seguridad en contraseñas y autenticación multifactor (MFA).**
4. **Separación de roles:** no dar a un solo usuario permisos administrativos y de auditoría.
5. **Hardening del sistema:** desactivar servicios innecesarios, configurar permisos correctos.

### 🔍 Cómo detectar un ataque de escalada de privilegios

- **Logs sospechosos:** intentos de acceder a archivos restringidos.
- **Uso anómalo de cuentas de servicio.**
- **Creación de usuarios desconocidos con permisos altos.**
- **Alertas del SIEM sobre ejecución de comandos administrativos inusuales.**

### 📚 Ejemplos de ataques de escalada de privilegios

- **Dirty COW (CVE-2016-5195, Linux):** vulnerabilidad que permitía a un usuario local escalar a root.
- **PrintNightmare (Windows, 2021):** explotación del servicio de impresión para obtener privilegios de SYSTEM.
- **OWASP IDOR (Insecure Direct Object Reference):** un atacante modifica un ID en una URL y accede a información de otro usuario (escalada horizontal).

### 🚨 Importancia de prevenir la escalada de privilegios

- La escalada de privilegios suele ser un **paso clave en el ciclo de ataque** (MITRE ATT\&CK, Kill Chain).
- Permite al atacante **moverse lateralmente** y comprometer sistemas críticos.
- Si no se controla, **un acceso menor se convierte en una brecha total**.

### 🏗️ Cómo afecta a la seguridad de las aplicaciones

- Una app vulnerable puede permitir que un usuario normal se convierta en administrador.
- Puede romper el **aislamiento de datos** entre clientes.
- Riesgo de manipulación de información sensible (ej. datos financieros o médicos).

### 🔐 Cómo proteger los sistemas de la escalada de privilegios

- **Segregar entornos:** producción, pruebas y desarrollo separados.
- **Monitorización continua:** SIEM + EDR para alertar accesos sospechosos.
- **Pruebas de penetración regulares:** identificar fallos antes que los atacantes.
- **Controles de acceso granulares:** RBAC (Role-Based Access Control).
- **Uso de PAM (Privileged Access Management):** gestión segura de cuentas privilegiadas.

### 🛠️ Controles clave a implementar

- Auditorías de permisos y cuentas privilegiadas.
- MFA obligatorio en accesos críticos.
- Rotación de credenciales y detección de secretos en código.
- Configuración segura de sistemas (hardening de Windows/Linux).
- Segmentación de red para limitar el movimiento lateral.

### 🚀 Conclusión

La **escalada de privilegios** es uno de los ataques más peligrosos porque convierte accesos limitados en **control total del sistema**.

- **Prevenirla** requiere aplicar mínimo privilegio, parches, monitoreo y controles de acceso.
- **Detectarla a tiempo** evita que un atacante use cuentas comprometidas para moverse por la red.
- **Protegerse contra ella** es fundamental para la seguridad de aplicaciones, usuarios y negocios.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**                         | **Siguiente 8**                               |
| ------------------ | ----------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_6_Understand_CIA_Triad.md) | [⏩](./8_8_Web_Based_Attacks_and_OWASP_10.md) |
