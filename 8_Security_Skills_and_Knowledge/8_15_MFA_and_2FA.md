| **Inicio**         | **atrás 14**                     | **Siguiente 16**          |
| ------------------ | -------------------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./8_14_Cyber_Kill_Chain.md) | [⏩](./8_16_Honeypots.md) |

---

## **Índice**

| Temario                              |
| ------------------------------------ |
| [223. ¿Qué es MFA?](#223-qué-es-mfa) |
| [224. ¿Qué es 2FA?](#224-qué-es-2fa) |

# **MFA y 2FA**

## **223. ¿Qué es MFA?**

![MFA y 2FA](/img/8_Security_Skills_and_Knowledge/MFA_y_2FA.jpg "MFA y 2FA")

### 🔐 ¿Qué es la autenticación multifactor (MFA)?

La **autenticación multifactor (MFA, por sus siglas en inglés)** es un método de seguridad que requiere que un usuario proporcione **dos o más pruebas de identidad** antes de acceder a un sistema, aplicación o cuenta.
Estas pruebas pertenecen a **categorías diferentes**, no solo una contraseña.

### ⭐ ¿Por qué es importante el MFA?

- Reduce el riesgo de **acceso no autorizado** en caso de robo o filtración de contraseñas.
- Protege contra ataques comunes como **phishing, fuerza bruta o keyloggers**.
- Es un **requisito de cumplimiento** en muchas normativas de seguridad (ej: **GDPR, PCI-DSS, HIPAA**).
- Aumenta la confianza en servicios de **banca online, e-commerce, correo corporativo y cloud**.

📊 Según Microsoft, **MFA bloquea el 99,9% de los ataques automatizados de robo de cuentas**.

### ⚙️ ¿Cómo funciona MFA?

El proceso general es:

1. El usuario ingresa su **nombre de usuario y contraseña** (factor 1: "lo que sabes").
2. El sistema solicita un **segundo factor de autenticación** (ej: código SMS, app, biometría).
3. Solo si ambos factores son correctos, se concede el acceso.

Ejemplo:

- Entras a tu correo con contraseña ✅.
- Te llega un código en tu móvil 📱 que también debes ingresar.
- Solo con ambas pruebas, accedes.

### 🔑 Tres tipos principales de métodos de autenticación MFA

Los factores de autenticación se agrupan en tres categorías:

1. **Algo que sabes** ➝ contraseñas, PINs, preguntas de seguridad.
2. **Algo que tienes** ➝ tarjeta inteligente, token físico, app autenticadora (Google Authenticator, Authy, Microsoft Authenticator).
3. **Algo que eres** ➝ biometría: huella digital, reconocimiento facial, voz, retina.

### 📌 Ejemplos de MFA

- Iniciar sesión en Gmail con contraseña + código de Google Authenticator.
- Acceder a tu cuenta bancaria online con contraseña + token físico.
- Desbloquear tu celular con PIN + huella digital.
- Entrar a un sistema empresarial con credenciales + tarjeta inteligente.

### 🛠 Otros tipos de autenticación multifactor

Además de los 3 básicos, existen variantes más modernas:

- **Ubicación geográfica** (ej: solo permite acceso desde un país específico).
- **Patrones de comportamiento** (ej: forma de escribir o mover el mouse).
- **Notificación push** (el usuario aprueba el acceso en su móvil).
- **Llaves de seguridad físicas** como **YubiKey** o **FIDO2**.

### 🔄 Diferencia entre MFA y autenticación de dos factores (2FA)

- **2FA**: se usan **exactamente dos factores** (ej: contraseña + SMS).
- **MFA**: implica **dos o más factores**. Puede ser 2FA o incluso 3 o más capas de autenticación.

👉 Toda **2FA es MFA**, pero no todo MFA es 2FA.

### ☁️ ¿Qué es MFA en Computación en la Nube?

En entornos cloud (ej: AWS, Azure, Google Cloud), MFA es clave porque:

- Los accesos son **remotos** y por internet, más expuestos a ataques.
- Asegura que solo los usuarios legítimos entren al **panel de control de la nube**.
- Se usa con apps como **Microsoft Authenticator, SMS o tokens físicos** para proteger datos y servicios en la nube.

Ejemplo:

- Un administrador de AWS inicia sesión → debe usar contraseña + app de autenticación en el móvil.

### 🏢 MFA para Office 365

En **Microsoft 365 / Office 365**, el MFA protege accesos a:

- Correo de Outlook.
- SharePoint y OneDrive.
- Teams y apps de Office.

Métodos comunes:

- **Microsoft Authenticator (notificación push o código temporal)**.
- **SMS o llamada telefónica**.
- **Llaves de seguridad FIDO2**.

La empresa puede configurarlo desde **Azure Active Directory** con **políticas condicionales** (ej: pedir MFA solo si el usuario accede desde un dispositivo desconocido).

---

[🔼](#índice)

---

## **224. ¿Qué es 2FA?**

### 🔐 Definición de 2FA

La **autenticación de dos factores (2FA, por sus siglas en inglés)** es un método de seguridad que requiere que el usuario verifique su identidad mediante **dos pruebas distintas** antes de acceder a un sistema, aplicación o servicio.

👉 Ejemplo simple: contraseña + código enviado por SMS.

### ⭐ Beneficios de la 2FA

- **Mayor seguridad**: protege contra robo de contraseñas y ataques de phishing.
- **Accesos más confiables**: reduce el riesgo de accesos no autorizados.
- **Cumplimiento normativo**: ayuda a cumplir con regulaciones de seguridad.
- **Protección de información sensible**: esencial para banca online, redes sociales y correo corporativo.
- **Prevención de ataques automatizados**: hace que un atacante necesite más que solo una contraseña.

### 🔑 Métodos de autenticación para 2FA

La 2FA combina **dos de estas categorías**:

1. **Algo que sabes** ➝ contraseña, PIN, respuesta de seguridad.
2. **Algo que tienes** ➝ token físico, SMS, app de autenticación (Google Authenticator, Authy, Microsoft Authenticator).
3. **Algo que eres** ➝ huella digital, reconocimiento facial, voz.

📌 Ejemplos concretos:

- Gmail: contraseña + código en app Google Authenticator.
- Banca online: contraseña + token físico.
- Smartphone: PIN + huella digital.

### ⚙️ Implementación de 2FA

El proceso típico para habilitar e implementar 2FA es:

1. **Registro del segundo factor**: el usuario asocia un número de teléfono, app autenticadora o dispositivo.
2. **Inicio de sesión normal**: se ingresa usuario y contraseña.
3. **Solicitud de segundo factor**: el sistema pide un código SMS, notificación push o biometría.
4. **Verificación exitosa**: el usuario accede solo si pasa ambos factores.

Ejemplo en Gmail:

- Entro con mi correo + contraseña.
- Recibo un código en Google Authenticator.
- Ingreso el código y accedo. ✅

### 🔄 2FA vs. MFA

- **2FA (Two-Factor Authentication):** requiere exactamente **dos factores** distintos (ejemplo: contraseña + SMS).
- **MFA (Multi-Factor Authentication):** requiere **dos o más factores**. Puede ser 2FA, pero también 3 o más (ejemplo: contraseña + app de autenticación + huella digital).

👉 En resumen:

- **Toda 2FA es MFA.**
- **No todo MFA es 2FA.**

---

[🔼](#índice)

---

| **Inicio**         | **atrás 14**                     | **Siguiente 16**          |
| ------------------ | -------------------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./8_14_Cyber_Kill_Chain.md) | [⏩](./8_16_Honeypots.md) |
