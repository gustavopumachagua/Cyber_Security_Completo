| **Inicio**         | **atrás 7**                         | **Siguiente 9**                                     |
| ------------------ | ----------------------------------- | --------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_7_Privilege_escalation.md) | [⏩](./8_9_Learn_how_Malware_Operates_and_Types.md) |

---

## **Índice**

| Temario                                  |
| ---------------------------------------- |
| [212. OWASP Top Ten](#212-owasp-top-ten) |

# **Ataques basados ​​en la web y OWASP 10**

## **212. OWASP Top Ten**

![OWASP Top Ten](/img/8_Security_Skills_and_Knowledge/OWASP_Top_Ten.jpg "OWASP Top Ten")

### 📌 ¿Qué es?

El **OWASP Top Ten** es una lista de las **10 principales vulnerabilidades de seguridad en aplicaciones web**, publicada por la **Open Web Application Security Project (OWASP)**.

- Es una **guía reconocida mundialmente** que ayuda a desarrolladores, testers y empresas a priorizar riesgos.
- Se actualiza aproximadamente cada 3–4 años para reflejar las amenazas más comunes.
- La versión más reciente (2021) reorganizó y renombró categorías para ajustarse mejor a la realidad actual.

👉 **Importancia:** el OWASP Top Ten no es solo una lista, sino también una **herramienta educativa** que promueve la seguridad desde el diseño (_security by design_).

### 🏆 OWASP Top Ten 2021 — Categorías explicadas

#### 🔴 A01:2021 – Broken Access Control (Control de acceso roto)

Cuando los usuarios pueden acceder a recursos o funciones que deberían estar restringidas.

- **Ejemplo:** un cliente modifica el ID en la URL (`/user/123 → /user/124`) y accede a la cuenta de otro usuario (**IDOR**).
- **Prevención:** implementar control de acceso en el servidor, nunca en el cliente.

#### 🔴 A02:2021 – Cryptographic Failures (Fallos criptográficos)

Problemas relacionados con cifrado débil, mal implementado o inexistente.

- **Ejemplo:** almacenar contraseñas en texto plano o usar HTTP en lugar de HTTPS.
- **Prevención:** usar TLS 1.3, contraseñas con hashing seguro (bcrypt, Argon2), y cifrado AES-GCM.

#### 🔴 A03:2021 – Injection (Inyección)

Cuando datos externos se insertan en un intérprete y alteran la consulta o comando original.

- **Ejemplo:** **SQL Injection**: `SELECT * FROM users WHERE id = '1 OR 1=1';` devuelve todos los usuarios.
- **Prevención:** usar consultas preparadas (prepared statements) y validación estricta de entradas.

#### 🔴 A04:2021 – Insecure Design (Diseño inseguro)

Errores en la arquitectura o lógica de la aplicación, no en el código.

- **Ejemplo:** un sistema bancario que permite transferencias sin límite ni validaciones adicionales.
- **Prevención:** aplicar _threat modeling_ y seguridad desde la fase de diseño.

#### 🔴 A05:2021 – Security Misconfiguration (Mala configuración de seguridad)

Configuraciones por defecto, permisos abiertos o servicios innecesarios.

- **Ejemplo:** un servidor con el panel de administración expuesto sin contraseña.
- **Prevención:** aplicar _hardening_, deshabilitar funciones no usadas, automatizar auditorías de configuración.

#### 🔴 A06:2021 – Vulnerable and Outdated Components (Componentes vulnerables y obsoletos)

Uso de librerías, frameworks o software sin parches.

- **Ejemplo:** una aplicación con **Struts 2** vulnerable (responsable del ataque a Equifax).
- **Prevención:** actualizar dependencias y usar herramientas como **OWASP Dependency-Check** o **Snyk**.

#### 🔴 A07:2021 – Identification and Authentication Failures (Fallos de autenticación)

Mecanismos de autenticación mal implementados que permiten robo de identidad.

- **Ejemplo:** no bloquear intentos tras múltiples contraseñas fallidas (ataque de fuerza bruta).
- **Prevención:** MFA (doble factor), sesiones seguras, políticas de contraseñas robustas.

#### 🔴 A08:2021 – Software and Data Integrity Failures (Fallos de integridad en software y datos)

Problemas al validar la integridad de datos o software, como cargas maliciosas.

- **Ejemplo:** actualizar una aplicación desde un repositorio no verificado.
- **Prevención:** firmar digitalmente actualizaciones y verificar integridad con hashes.

#### 🔴 A09:2021 – Security Logging and Monitoring Failures (Fallos en logging y monitoreo)

Falta de registro o monitoreo que permite que ataques pasen desapercibidos.

- **Ejemplo:** un ataque de fuerza bruta no detectado porque no hay alertas configuradas.
- **Prevención:** implementar SIEM, auditorías y alertas en tiempo real.

#### 🔴 A10:2021 – Server-Side Request Forgery (SSRF)

El servidor realiza solicitudes a recursos internos controlados por el atacante.

- **Ejemplo:** una app que permite al usuario ingresar una URL para previsualizarla; el atacante pone `http://localhost:8080/admin` y accede a un servicio interno.
- **Prevención:** validar entradas, limitar rangos de IPs y usar listas de denegación.

*

### 📊 Ejemplo integrador

Un **e-commerce** mal protegido podría:

- Permitir que un cliente acceda a pedidos de otros (**Broken Access Control**).
- Guardar contraseñas sin cifrar (**Cryptographic Failures**).
- Ser vulnerable a **SQL Injection** en el formulario de login.
- Usar librerías viejas como jQuery 1.x (**Vulnerable Components**).
- No tener alertas configuradas si alguien intenta ataques (**Logging Failures**).

👉 Resultado: pérdida de datos, reputación dañada y sanciones legales.

### 🛡️ Importancia del OWASP Top Ten

- **Para desarrolladores:** guía clara de qué evitar al programar.
- **Para empresas:** base para auditorías y pruebas de seguridad.
- **Para testers/pentesters:** punto de partida para pruebas de penetración.

En resumen: **seguir el OWASP Top Ten reduce drásticamente el riesgo de ciberataques comunes en aplicaciones web**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**                         | **Siguiente 9**                                     |
| ------------------ | ----------------------------------- | --------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_7_Privilege_escalation.md) | [⏩](./8_9_Learn_how_Malware_Operates_and_Types.md) |
