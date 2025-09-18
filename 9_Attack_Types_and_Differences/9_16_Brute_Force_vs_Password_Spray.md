| **Inicio**         | **atrás 15**                  |
| ------------------ | ----------------------------- |
| [🏠](../README.md) | [⏪](./9_15_Typosquatting.md) |

---

## **Índice**

| Temario                                                                                 |
| --------------------------------------------------------------------------------------- |
| [295. Brute-force vs. Password Spray Attack](#295-brute-force-vs-password-spray-attack) |
| [296. Que es Password Spraying?](#296-que-es-password-spraying)                         |
| [297. Que es Brute-force Attack?](#297-que-es-brute-force-attack)                       |

# **Brute Force vs Password Spray**

## **295. Brute-force vs. Password Spray Attack**

![Brute Force vs Password Spray](/img/9_Attack_Types_and_Differences/Brute_Force_vs_Password_Spray.png "Brute Force vs Password Spray")

### 1️⃣ Ataque de fuerza bruta (Brute Force Attack)

Un **ataque de fuerza bruta** consiste en que el atacante intenta acceder a una cuenta **probando múltiples contraseñas posibles para un único usuario**, hasta encontrar la correcta.

🔹 Ejemplo:

- Usuario: `gustavo@empresa.com`
- Intentos: `gustavo123`, `perrito2025`, `password`, `Empresa2025!`... (cientos o miles de combinaciones).

📌 **Características detectables en Sentinel**:

- Muchos intentos de inicio de sesión fallidos contra **la misma cuenta**.
- Origen desde la misma IP o desde varias IP distribuidas (botnets).
- Alta frecuencia de intentos en un corto período.

### 2️⃣ Ataque de rociado de contraseñas (Password Spraying Attack)

En este ataque, en lugar de probar muchas contraseñas contra una cuenta, el atacante prueba **una contraseña común contra muchas cuentas diferentes**.
Se hace así para **evitar bloqueos de cuenta** y pasar desapercibido.

🔹 Ejemplo:

- Contraseña probada: `Empresa2024!`
- Cuentas atacadas: `gustavo@empresa.com`, `ana@empresa.com`, `jose@empresa.com`, `soporte@empresa.com`…

📌 **Características detectables en Sentinel**:

- Múltiples intentos de inicio de sesión fallidos **contra diferentes usuarios**.
- Generalmente desde la **misma IP o rango de IP**.
- Menor frecuencia que un ataque de fuerza bruta (para evitar detección).

### 3️⃣ Microsoft (Azure) Sentinel 🛰️

**Azure Sentinel** (ahora llamado **Microsoft Sentinel**) es una plataforma **SIEM/SOAR** que permite:

- Recibir registros de inicio de sesión desde Azure AD, Office 365, endpoints, firewalls, etc.
- Detectar comportamientos anómalos como fuerza bruta o rociado de contraseñas.
- Crear **alertas automáticas** con _detecciones analíticas_ (queries en KQL).
- Visualizar en **libros de trabajo (workbooks)** los intentos de autenticación sospechosos.

### 4️⃣ Registros que analiza Sentinel

Los ataques de este tipo suelen aparecer en:

- **SignInLogs**: registros de inicio de sesión en Azure AD.
- **AuditLogs**: registros de actividad administrativa.
- **SecurityEvent**: eventos de Windows (ID 4625 = inicio de sesión fallido).
- **OfficeActivity**: actividad en correos y aplicaciones de Microsoft 365.

👉 Ejemplo de campo clave:

- `ResultType = 50126` → indica que la contraseña no coincide (fallo de credenciales).
- `UserPrincipalName` → cuenta atacada.
- `IPLocation` → origen del intento.

### 5️⃣ Diferencias clave en detección (tabla comparativa)

| Característica        | Fuerza Bruta                                           | Rociado de Contraseñas                                             |
| --------------------- | ------------------------------------------------------ | ------------------------------------------------------------------ |
| Objetivo              | 1 usuario                                              | Muchos usuarios                                                    |
| Contraseñas probadas  | Miles                                                  | Pocas (una o varias comunes)                                       |
| Riesgo de bloqueo     | Alto (cuenta bloqueada)                                | Bajo (evita bloqueos)                                              |
| Patrón en registros   | Muchos intentos a un usuario                           | Pocos intentos a muchos usuarios                                   |
| Detección en Sentinel | SignInLogs muestra múltiples fallos de la misma cuenta | Fallos distribuidos en distintas cuentas con misma IP y contraseña |

### 6️⃣ Libros de trabajo en Sentinel 📊

Los **workbooks (libros de trabajo)** permiten visualizar gráficamente:

- Intentos de inicio de sesión por usuario, IP, ubicación.
- Mapas de calor de accesos fallidos.
- Tendencias en el tiempo de ataques por fuerza bruta vs. rociado.

👉 Ejemplo: un workbook puede mostrar que desde la IP `45.89.123.22` hubo 500 intentos fallidos contra 1 cuenta (fuerza bruta) o 1 intento fallido contra 500 cuentas (rociado).

### ✅ Conclusión

- Un **ataque de fuerza bruta** busca adivinar una contraseña probando muchas opciones sobre un mismo usuario.
- Un **ataque de rociado de contraseñas** busca comprometer al menos una cuenta probando pocas contraseñas comunes sobre muchos usuarios.
- **Azure Sentinel** es clave para identificar patrones, correlacionar registros y alertar.
- La mejor defensa combina:

  - 🔒 Autenticación multifactor (MFA).
  - 🔒 Bloqueo inteligente de cuentas.
  - 🔒 Monitoreo activo con Sentinel.
  - 🔒 Alertas basadas en KQL para detectar patrones de acceso sospechosos.

---

[🔼](#índice)

---

## **296. Que es Password Spraying?**

El **rociado de contraseñas** (_password spraying attack_) es una técnica de ciberataque en la que un atacante prueba **una o pocas contraseñas comunes contra un gran número de cuentas de usuario**, en lugar de probar muchas contraseñas contra una sola cuenta (como en un ataque de fuerza bruta clásico).

👉 Ejemplo:

- Contraseña probada: `Password123`
- Cuentas objetivo:

  - `ana@empresa.com`
  - `gustavo@empresa.com`
  - `ventas@empresa.com`
  - `soporte@empresa.com`

De esta forma, el atacante **minimiza el riesgo de bloqueo** y aumenta sus posibilidades de éxito, ya que en la mayoría de organizaciones **siempre hay alguien con una contraseña débil o común**.

### 📖 Seguir leyendo sobre la pulverización de contraseñas

La pulverización de contraseñas es peligrosa porque:

- Aprovecha **contraseñas débiles y reutilizadas**.
- Evade mecanismos de seguridad como bloqueos por intentos fallidos.
- Puede ser ejecutada de forma **distribuida** (botnets o IPs distintas).
- Suele ser el primer paso en **ataques de movimiento lateral** dentro de una red.

### 🛠️ Herramientas de descifrado utilizadas en password spraying

#### 1️⃣ **John the Ripper**

Es una herramienta clásica de auditoría de contraseñas.

- Se usa para **descifrar hashes** de contraseñas.
- Permite probar listas de contraseñas comunes (_wordlists_) contra múltiples cuentas.
- Ejemplo:

  ```
  john --wordlist=passwords.txt --format=NT hashes.txt
  ```

  Esto intentaría descifrar hashes de contraseñas de Windows con una lista común.

#### 2️⃣ **Hydra**

Es una herramienta potente para **ataques online** contra servicios como SSH, RDP, FTP, HTTP o bases de datos.

- Ejemplo de ataque de rociado contra SSH:

  ```
  hydra -L usuarios.txt -p "Password123" ssh://192.168.1.10
  ```

  Aquí, Hydra prueba la misma contraseña contra múltiples usuarios (password spraying).

### ⚖️ Regulaciones de la SEC y los ciberataques

En EE. UU., la **SEC (Securities and Exchange Commission)** ahora exige a las empresas que:

- Reporten incidentes de ciberseguridad relevantes.
- Documenten riesgos asociados a ataques como password spraying.
- Involucren al **CISO** en la toma de decisiones estratégicas sobre seguridad.

👉 Esto significa que **los ataques de pulverización de contraseñas no solo son un problema técnico**, también son un **riesgo legal y de cumplimiento**.

### 🔥 Principales tipos de amenazas de seguridad relacionadas

1. **Phishing + Password Spraying**:

   El atacante roba un usuario válido por phishing y luego usa rociado para descubrir la contraseña.

2. **Credential Stuffing**:

   Reutilización de contraseñas robadas en otras plataformas.

3. **Ataques híbridos**:

   Uso de spraying combinado con fuerza bruta lenta para evadir detección.

4. **Compromiso de cuentas privilegiadas**:

   Alguien en RRHH, finanzas o IT suele ser objetivo porque tiene acceso a datos sensibles.

### 👩‍💼 Rol de RRHH en la prevención de ciberataques

El área de **Recursos Humanos (RRHH)** juega un papel importante en la defensa contra ataques de contraseñas:

- **Capacitación de empleados** sobre buenas prácticas de seguridad.
- Políticas de **rotación de contraseñas** y uso de gestores de contraseñas.
- Supervisión de accesos cuando un empleado deja la empresa.
- Aplicación de **autenticación multifactor (MFA)** obligatoria.

### ✅ Conclusión

El **rociado de contraseñas** es un ataque **silencioso y efectivo** porque explota la **tendencia humana a usar contraseñas débiles**.
Para mitigarlo:

- 🚫 Evitar contraseñas comunes (`Password123`, `Empresa2024!`, etc.).
- 🔒 Implementar MFA en todas las cuentas.
- 👀 Monitorear accesos sospechosos con herramientas SIEM (como Azure Sentinel).
- 📚 Educar a empleados, especialmente de áreas críticas como RRHH, sobre higiene de contraseñas.

---

[🔼](#índice)

---

## **297. Que es Brute-force Attack?**

Un **ataque de fuerza bruta** es un método de ciberataque en el que un atacante intenta **descifrar contraseñas, claves de cifrado o credenciales** probando **todas las combinaciones posibles** hasta encontrar la correcta.

👉 Ejemplo:

- Usuario: `gustavo@empresa.com`
- Intentos: `123456`, `gustavo2024`, `password`, `Empresa2025!`, etc.

El ataque puede ser **rápido** (usando diccionarios de contraseñas comunes) o **lento y exhaustivo** (probando combinaciones de letras, números y símbolos).

### 📖 Definición de ataque de fuerza bruta

➡️ Se llama así porque no utiliza técnicas sofisticadas, sino **la fuerza computacional** para probar posibilidades.

➡️ El éxito depende de:

- Longitud y complejidad de la contraseña.
- Velocidad de procesamiento del atacante (CPU/GPU).
- Medidas de seguridad del sistema atacado.

### 🧩 Tipos de ataques de fuerza bruta

1. **Ataque simple**

   - El atacante prueba todas las combinaciones posibles.
   - Ejemplo: intentar descifrar `1234` probando desde `0000` hasta `9999`.

2. **Ataque de diccionario**

   - Se usan listas de contraseñas comunes.
   - Ejemplo: probar con `123456`, `qwerty`, `password`, `letmein`.

3. **Ataque híbrido**

   - Combina diccionarios con variaciones.
   - Ejemplo: `Password2024!`, `Qwerty123`.

4. **Ataque inverso (Reverse Brute Force)**

   - El atacante tiene una contraseña común y la prueba en múltiples cuentas.
   - Ejemplo: `Password123` probado en todos los usuarios de una empresa.

5. **Ataque de credenciales robadas (Credential Stuffing)**

   - Se reutilizan contraseñas filtradas de otras brechas.
   - Ejemplo: si tu correo y contraseña fueron robados en un hackeo de Facebook, intentarán usarlos en tu correo corporativo.

### 🎯 Motivos detrás de los ataques de fuerza bruta

- **Robo de credenciales** para acceder a cuentas de correo, banca o redes sociales.
- **Compromiso de sistemas empresariales** (servidores, bases de datos, VPNs).
- **Robo de información confidencial** (propiedad intelectual, datos de clientes).
- **Extorsión o espionaje** mediante ransomware.
- **Acceso inicial** para ataques más grandes (movimiento lateral en una red).

### 🛠️ Herramientas de ataque de fuerza bruta

Existen varias herramientas populares utilizadas tanto por **pentesters** (de forma legal) como por **ciberdelincuentes**:

- **Hydra** → ataques a SSH, FTP, RDP, HTTP.
- **John the Ripper** → descifrado de hashes de contraseñas.
- **Hashcat** → uno de los más rápidos para ataques con GPU.
- **Medusa** → similar a Hydra, muy flexible.
- **Aircrack-ng** → especializado en romper claves Wi-Fi WPA/WPA2.

### 🛡️ Cómo prevenir ataques de fuerza bruta

✅ **A nivel de usuario**

- Usar contraseñas largas y complejas (mínimo 12 caracteres).
- Activar **autenticación multifactor (MFA)**.
- Evitar reutilizar contraseñas en múltiples cuentas.
- Usar un **gestor de contraseñas**.

✅ **A nivel empresarial**

- Implementar políticas de bloqueo de cuenta tras intentos fallidos.
- Usar sistemas de detección de anomalías (SIEM, EDR, WAF).
- Limitar el número de intentos de autenticación por IP.
- Desplegar **CAPTCHAs** en formularios de login.
- Aplicar **hashing seguro** en almacenamiento de contraseñas (bcrypt, scrypt, Argon2).

### 🔐 ¿Qué es una clave de cifrado?

Una **clave de cifrado** es un valor secreto (cadena de bits) que se usa en algoritmos criptográficos para **encriptar y desencriptar información**.

- Mientras más larga y compleja sea, más difícil será romperla por fuerza bruta.

👉 Ejemplo:

- Clave de 56 bits (DES) → hoy se puede romper en horas.
- Clave de 256 bits (AES-256) → prácticamente imposible romperla con fuerza bruta actual.

### ❓ Preguntas frecuentes sobre ataques de fuerza bruta

#### 1. ¿Qué es un ataque de fuerza bruta?

Es un método de ataque que prueba todas las combinaciones posibles de contraseñas o claves hasta encontrar la correcta.

#### 2. ¿Es ilegal un ataque de fuerza bruta?

✅ Sí, si se hace sin autorización.

❌ No, si se hace en **pentesting autorizado** o pruebas de seguridad internas.

#### 3. ¿Qué tan comunes son los ataques de fuerza bruta?

Muy comunes. Están entre los ataques más usados porque son **simples y efectivos** contra contraseñas débiles.

#### 4. ¿Cuánto tiempo tomaría descifrar una contraseña de ocho caracteres?

Depende de la complejidad:

- `12345678` (solo números) → segundos o minutos.
- `Abc123!!` (mayúsculas, minúsculas, números, símbolos) → horas o días.
- Contraseña aleatoria de 8 caracteres (`#9qL%7rX`) → semanas o meses con hardware potente.
- Contraseña de 12+ caracteres aleatorios → miles de años con la tecnología actual.

✅ **Conclusión**:

Los ataques de **fuerza bruta** siguen siendo una de las mayores amenazas porque explotan la **debilidad de las contraseñas humanas**. La combinación de **MFA + contraseñas fuertes + monitoreo de accesos** es la mejor defensa.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 15**                  |
| ------------------ | ----------------------------- |
| [🏠](../README.md) | [⏪](./9_15_Typosquatting.md) |
