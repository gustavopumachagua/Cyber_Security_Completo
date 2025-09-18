| **Inicio**         | **atrás 5**                          | **Siguiente 7**                     |
| ------------------ | ------------------------------------ | ----------------------------------- |
| [🏠](../README.md) | [⏪](./8_5_Understand_Handshakes.md) | [⏩](./8_7_Privilege_escalation.md) |

---

## **Índice**

| Temario                                  |
| ---------------------------------------- |
| [210. The CIA Triad](#210-the-cia-triad) |

# **Understand CIA Triad**

## **210. The CIA Triad**

![The CIA Triad](/img/8_Security_Skills_and_Knowledge/The_CIA_Triad.jpeg "The CIA Triad")

La **Tríada CIA** (Confidentiality, Integrity, Availability) es el **modelo fundamental de la seguridad de la información**. Representa tres principios que guían cómo proteger datos, sistemas y redes.

- **C** → **Confidentiality (Confidencialidad)**
- **I** → **Integrity (Integridad)**
- **A** → **Availability (Disponibilidad)**

Si alguna de estas dimensiones falla, la seguridad de la información se ve comprometida.

### 🔒 1. Confidencialidad (Confidentiality)

#### 📌 Definición

La confidencialidad significa que la información **solo está disponible para las personas autorizadas**. Busca evitar accesos no autorizados o fugas de datos.

#### 📌 Ejemplos

- **Correo electrónico cifrado**: solo el receptor con la clave adecuada puede leerlo.
- **Contraseñas almacenadas con hashing**: incluso si alguien accede a la base de datos, no puede ver las contraseñas en texto plano.
- **MFA (autenticación multifactor)**: asegura que solo el usuario legítimo pueda entrar.
- **Políticas de permisos en un sistema de archivos**: empleados de finanzas no pueden acceder a los archivos de recursos humanos.

#### 📌 Amenaza típica

- **Exfiltración de datos** por un atacante.
- **Error humano**: un correo enviado con información sensible al destinatario equivocado.

### 🧾 2. Integridad (Integrity)

#### 📌 Definición

La integridad asegura que los datos **se mantienen correctos, completos y sin alteraciones no autorizadas**.

#### 📌 Ejemplos

- **Hashes de archivos (SHA-256, MD5)**: verificar que un archivo descargado no fue manipulado.
- **Firmas digitales** en correos electrónicos o documentos PDF.
- **Bases de datos con controles de integridad**: impiden que se registren datos inválidos.
- **Control de versiones (Git)**: detecta cambios en el código y quién los hizo.

#### 📌 Amenaza típica

- **Ransomware** que modifica archivos y los cifra.
- **Ataques “man-in-the-middle” (MITM)** que alteran mensajes durante la transmisión.
- **Empleado malicioso** que cambia registros financieros.

### ⚡ 3. Disponibilidad (Availability)

#### 📌 Definición

La disponibilidad garantiza que los sistemas y datos **están accesibles cuando los usuarios autorizados los necesitan**.

#### 📌 Ejemplos

- **Sistemas de respaldo (backups)**: recuperar servicios tras un fallo.
- **Balanceadores de carga**: mantienen disponible una aplicación aunque un servidor falle.
- **Planes de recuperación ante desastres (DRP)**: continuidad del negocio ante incidentes.
- **UPS (sistemas de energía ininterrumpida)**: mantienen servicios activos en cortes eléctricos.

#### 📌 Amenaza típica

- **Ataques DDoS** que dejan inaccesible un sitio web.
- **Fallas de hardware** sin redundancia.
- **Desastres naturales** que interrumpen servicios críticos.

### 📊 Ejemplo integrador (CIA en acción)

Imagina un **banco online**:

1. **Confidencialidad**: los datos de los clientes y transacciones están cifrados (TLS en HTTPS, cifrado en bases de datos).
2. **Integridad**: las transferencias usan firmas digitales y registros en blockchain para que no se alteren montos.
3. **Disponibilidad**: el banco tiene servidores redundantes en múltiples ubicaciones y planes de contingencia contra DDoS.

👉 Si falla uno de los tres:

- Sin **Confidencialidad**: se filtran los datos bancarios.
- Sin **Integridad**: alguien puede manipular montos de transferencias.
- Sin **Disponibilidad**: los clientes no pueden acceder a su dinero.

### 📌 Resumen visual (CIA Triad en pocas palabras)

- **C**: “¿Quién puede ver la información?”
- **I**: “¿La información sigue siendo exacta y no se manipuló?”
- **A**: “¿Puedo acceder a la información cuando la necesito?”

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**                          | **Siguiente 7**                     |
| ------------------ | ------------------------------------ | ----------------------------------- |
| [🏠](../README.md) | [⏪](./8_5_Understand_Handshakes.md) | [⏩](./8_7_Privilege_escalation.md) |
