| **Inicio**         | **atrás 9**                                         | **Siguiente 11**                                 |
| ------------------ | --------------------------------------------------- | ------------------------------------------------ |
| [🏠](../README.md) | [⏪](./8_9_Learn_how_Malware_Operates_and_Types.md) | [⏩](./8_11_Roles_of_Compliance_and_Auditors.md) |

---

## **Índice**

| Temario                                                                          |
| -------------------------------------------------------------------------------- |
| [214. ¿Qué es una red de confianza cero?](#214-qué-es-una-red-de-confianza-cero) |

# **Core Concepts of Zero Trust**

## **214. ¿Qué es una red de confianza cero?**

![red de confianza cero](/img/8_Security_Skills_and_Knowledge/red_de_confianza_cero.webp "red de confianza cero")

La **seguridad Zero Trust** (confianza cero) es un **modelo de ciberseguridad** basado en la idea de que **ningún usuario, dispositivo o aplicación debe ser confiado por defecto**, aunque esté dentro de la red corporativa.

👉 En vez de asumir que todo lo que está en la red interna es seguro, **Zero Trust exige verificación continua de identidad, contexto y cumplimiento de políticas antes de otorgar acceso**.

📌 En resumen: **"Nunca confíes, siempre verifica"**.

### 📜 Principios fundamentales de Zero Trust

1. **Verificación continua de identidad y dispositivo**

   - Autenticación multifactor (MFA).
   - Validación de estado de seguridad del dispositivo (ejemplo: ¿tiene antivirus activo?).

2. **Acceso mínimo necesario (Principio de menor privilegio)**

   - Los usuarios y servicios solo acceden a lo que necesitan para su función.
   - Se usan controles granulares (microsegmentación).

3. **Asume que la red ya está comprometida**

   - Se monitorea tráfico este-oeste (entre servidores internos).
   - La confianza no depende de estar “dentro” o “fuera” de la red.

4. **Microsegmentación y control de acceso dinámico**

   - Separar recursos críticos (servidores, bases de datos, apps).
   - Políticas adaptativas según contexto (ubicación, hora, comportamiento).

5. **Monitoreo y análisis continuo**

   - Detección en tiempo real de anomalías.
   - Registro de todas las actividades (para auditoría y respuesta a incidentes).

### ✅ Ventajas de Zero Trust

- 🔒 **Mayor seguridad**: reduce el movimiento lateral en caso de intrusión.
- 👤 **Protección de identidades**: evita uso indebido de credenciales robadas.
- 🌍 **Acceso seguro desde cualquier lugar**: ideal para entornos híbridos y trabajo remoto.
- 📉 **Reducción de riesgos de insiders**: empleados o contratistas malintencionados tienen menos alcance.
- 🛡️ **Cumplimiento normativo**: ayuda con regulaciones como GDPR, HIPAA, PCI DSS.
- 🚀 **Mejor visibilidad**: todo el tráfico se supervisa y registra.

### 🕰️ Historia de la seguridad Zero Trust

- **2004**: Jericho Forum plantea la necesidad de seguridad "de-perimetralizada".
- **2010**: John Kindervag (Forrester Research) acuña el término **Zero Trust**.
- **2014–2017**: Google implementa _BeyondCorp_, un modelo pionero de acceso seguro basado en Zero Trust.
- **2018**: NIST publica el marco de referencia **SP 800-207**, estandarizando Zero Trust Architecture (ZTA).
- **Hoy**: Adoptado ampliamente por gobiernos y empresas como estándar de seguridad moderna.

### 🌐 ZTNA – Zero Trust Network Access

**ZTNA (Zero Trust Network Access)** es una tecnología que aplica el modelo Zero Trust al **control de acceso a la red**.

📌 Diferencia con una VPN tradicional:

- **VPN**: conecta al usuario a toda la red interna (si el atacante entra, puede moverse lateralmente).
- **ZTNA**: conecta al usuario solo a la aplicación o recurso específico al que está autorizado.

👉 ZTNA ≈ una puerta segura que se abre **únicamente para cada aplicación**.

### 🎯 Casos de uso de Zero Trust

1. **Trabajo remoto y BYOD**

   - Verificación continua de empleados que acceden desde casa o dispositivos personales.

2. **Protección contra ransomware y malware**

   - Microsegmentación impide propagación dentro de la red.

3. **Protección de datos sensibles**

   - Acceso granular a datos de clientes, salud o finanzas.

4. **Entornos de nube híbrida y multicloud**

   - Unifica seguridad en AWS, Azure, GCP y on-premise.

5. **IoT y OT (tecnología operativa)**

   - Controla qué dispositivos conectados pueden comunicarse y con qué.

### 🏆 Buenas prácticas de Zero Trust

- **Adoptar MFA y autenticación adaptativa**.
- **Implementar gestión de identidades (IAM/IDaaS)**.
- **Clasificar y segmentar los activos críticos**.
- **Automatizar políticas de acceso** basadas en contexto.
- **Integrar EDR/XDR y SIEM** para monitoreo continuo.
- **Aplicar cifrado de extremo a extremo (TLS 1.3)**.
- **Educar a los empleados** en buenas prácticas de seguridad.

### ⚙️ Cómo implementar la seguridad Zero Trust (paso a paso)

1. **Descubrir y clasificar activos**

   - Identificar datos, aplicaciones, dispositivos y usuarios críticos.

2. **Definir la superficie de protección**

   - Determinar qué proteger (ejemplo: base de datos de clientes).

3. **Aplicar controles de identidad y acceso**

   - Implementar MFA, IAM y ZTNA.

4. **Microsegmentar la red**

   - Dividir recursos en “islas” de seguridad.

5. **Aplicar políticas dinámicas basadas en contexto**

   - Acceso depende de identidad, dispositivo, ubicación, comportamiento.

6. **Monitorear y registrar continuamente**

   - SIEM/XDR para analizar tráfico y detectar anomalías.

7. **Automatizar respuestas**

   - Integrar SOAR para aislar dispositivos comprometidos automáticamente.

### 📌 Resumen

- **Zero Trust = no confiar en nadie ni nada por defecto.**
- **Principios:** verificación continua, privilegios mínimos, microsegmentación.
- **ZTNA:** acceso seguro a aplicaciones, reemplazo moderno de VPN.
- **Ventajas:** protege identidades, datos, redes híbridas, usuarios remotos.
- **Historia:** desde 2010 (Forrester) hasta el marco oficial NIST SP 800-207.
- **Implementación:** empezar con MFA + IAM, luego microsegmentar, monitorear y automatizar.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 9**                                         | **Siguiente 11**                                 |
| ------------------ | --------------------------------------------------- | ------------------------------------------------ |
| [🏠](../README.md) | [⏪](./8_9_Learn_how_Malware_Operates_and_Types.md) | [⏩](./8_11_Roles_of_Compliance_and_Auditors.md) |
