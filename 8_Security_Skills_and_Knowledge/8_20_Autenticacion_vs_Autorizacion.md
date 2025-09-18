| **Inicio**         | **atrás 19**                                 | **Siguiente 21**                                |
| ------------------ | -------------------------------------------- | ----------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_19_Conceptos_basicos_de_IDS_IPS.md) | [⏩](./8_21_Understand_Common_Hacking_Tools.md) |

---

## **Índice**

| Temario                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------- |
| [235. Autenticación de dos factores (2FA)](#235-autenticación-de-dos-factores-2fa)                                        |
| [236. Biometría (huella dactilar, reconocimiento facial, etc.)](#236-biometría-huella-dactilar-reconocimiento-facial-etc) |
| [237. Tokens o certificados de seguridad](#237-tokens-o-certificados-de-seguridad)                                        |
| [238. Control de acceso basado en roles (RBAC)](#238-control-de-acceso-basado-en-roles-rbac)                              |
| [239. Listas de control de acceso (ACL)](#239-listas-de-control-de-acceso-acl)                                            |
| [240. Control de acceso basado en atributos (ABAC)](#240-control-de-acceso-basado-en-atributos-abac)                      |

# **Autenticación vs. Autorización**

## **235. Autenticación de dos factores (2FA)**

![Autenticación vs. Autorización](/img/8_Security_Skills_and_Knowledge/Autenticacion_vs_Autorizacion.png "Autenticación vs. Autorización")

### 🔐 ¿Qué es la autenticación de dos factores (2FA)?

La **autenticación de dos factores (2FA)** es un método de seguridad que requiere **dos pruebas diferentes de identidad** antes de permitir el acceso a una cuenta, sistema o aplicación.

La idea es **añadir una capa extra de seguridad**: incluso si un atacante obtiene tu contraseña, necesitará el **segundo factor**.

Ejemplo simple:

- **Factor 1:** contraseña.
- **Factor 2:** código de un SMS, aplicación o token.

### ❌ ¿Por qué las contraseñas no son lo suficientemente buenas?

Las contraseñas son débiles porque:

1. **Se pueden adivinar o forzar** (ataques de diccionario/brute-force).
2. **Se filtran en brechas** (muchos usuarios repiten contraseñas).
3. **Son fáciles de robar** mediante phishing, malware o keyloggers.
4. **Los humanos eligen mal**: contraseñas cortas, simples o con patrones conocidos.

Ejemplo:

- Una contraseña como `123456` o `password` se crackea en segundos.
- Si tu correo y contraseña aparecen en una filtración (ej: LinkedIn 2012), un atacante puede probarlas en Facebook, Gmail o bancos (**credential stuffing**).

### 📈 El aumento de los delitos cibernéticos exige una mayor seguridad con 2FA

Con el crecimiento de ataques como:

- **Phishing** → engañan al usuario para que entregue su contraseña.
- **Ransomware** → acceden con credenciales robadas.
- **Ataques a bancos / fintech** → intentos de fraude financiero.

Las empresas y usuarios necesitan **2FA** para reducir el riesgo. Incluso si el atacante roba tu contraseña, se encontrará con una barrera adicional.

### 🕰️ Contraseñas: históricamente malas, pero aún en uso

- Fueron el **primer mecanismo de seguridad digital** en los 60s.
- Desde entonces, se han demostrado sus debilidades.
- Aún así, son **difíciles de reemplazar** porque son fáciles de implementar y todos están acostumbrados a usarlas.
- Por eso, la estrategia realista es: **no eliminarlas, sino complementarlas con 2FA**.

### 🛡️ 2FA al rescate

2FA combina **“algo que sabes” (contraseña)** con **“algo que tienes” (token, móvil) o “algo que eres” (biometría)**.
Esto hace que un atacante necesite **dos llaves diferentes**, lo que complica mucho su tarea.

Ejemplo:

- Gmail con 2FA → si alguien obtiene tu contraseña, no podrá acceder sin el código del segundo factor (en tu móvil).

### 🔑 Tipos comunes de 2FA

#### 1. 📟 Tokens de hardware

Dispositivos físicos que generan o almacenan un código.

Ejemplos:

- **Llaves YubiKey** o **Feitian** (con protocolo FIDO2/U2F).
- Tokens RSA que generan códigos cada 60 segundos.

Ventaja: **muy seguros** porque no dependen de red.

Desventaja: **pueden perderse** o ser caros para usuarios comunes.

#### 2. 📱 2FA basado en SMS y voz

El sistema envía un código único (OTP) por SMS o llamada.

Ejemplo:

- Inicias sesión en tu banco → recibes un SMS con un código → lo introduces.

Ventajas:

- Fácil de usar, no requiere apps.

  Desventajas:

- **Vulnerable a SIM swapping** (cuando un atacante clona tu SIM).
- Depende de la cobertura móvil.

#### 3. 🔑 Tokens de software

Apps que generan códigos temporales (**TOTP**) cada 30 segundos.

Ejemplos:

- **Google Authenticator**, **Authy**, **Microsoft Authenticator**.

Ventajas:

- No dependen de SMS.
- Gratis y fáciles de configurar.
  Desventajas:
- Si pierdes el móvil sin copia de seguridad, pierdes acceso.

#### 4. 📲 Notificación push

El sistema envía una notificación a tu app móvil y solo tienes que **aceptar o rechazar**.

Ejemplo:

- Facebook, Google o Microsoft envían una alerta: _“¿Eres tú intentando iniciar sesión?”_
- Con un toque apruebas o rechazas.

Ventajas:

- Más cómodo que teclear códigos.
- Permite mostrar **ubicación, hora y dispositivo**.

  Desventajas:

- Depende de Internet y de tener el móvil.

#### 5. 🧬 Otras formas de autenticación de dos factores

- **Biometría:** huellas digitales, reconocimiento facial, iris.
- **Correo electrónico (OTP enviado al mail).**
- **Preguntas de seguridad** (menos recomendadas porque son fáciles de adivinar).
- **Autenticación basada en ubicación o dispositivo de confianza.**

### 🌍 Todo el mundo debería usar 2FA

Casos clave:

- **Correo electrónico**: base de todo (recuperación de contraseñas).
- **Banca en línea**: dinero directamente en riesgo.
- **Redes sociales**: robo de identidad, fraudes y estafas.
- **Cuentas de trabajo**: riesgo para toda la empresa.

Incluso si parece molesto, 2FA es la **barrera más efectiva** contra ataques de credenciales.

### 📚 ¿Quieres saber más sobre 2FA?

- Revisa si tus cuentas permiten **activar 2FA en configuraciones de seguridad**.
- Prueba usar **apps de autenticación** en vez de SMS para más seguridad.
- Considera comprar una **llave de hardware** si manejas datos sensibles o trabajas en ciberseguridad.
- Explora estándares como **FIDO2/WebAuthn**, que son el futuro del login sin contraseñas.

---

[🔼](#índice)

---

## **236. Biometría (huella dactilar, reconocimiento facial, etc.)**

### 📌 ¿Qué es la biometría?

La **biometría** es la ciencia que utiliza **características físicas o de comportamiento únicas de una persona** para **verificar o autenticar su identidad**.

A diferencia de las contraseñas, la biometría se basa en **quién eres**, no en lo que sabes o lo que tienes.

👉 Ejemplos:

- Huella digital.
- Reconocimiento facial.
- Escaneo del iris o retina.
- Reconocimiento de voz.
- Dinámica de tecleo (patrón al escribir).

### 🔑 Tres tipos de seguridad biométrica

Podemos clasificar la biometría en tres grandes categorías:

1. **Biometría fisiológica** → basada en características físicas:

   - Huella digital.
   - Iris, retina.
   - Forma del rostro.
   - ADN.

2. **Biometría conductual** → basada en patrones de comportamiento:

   - Forma de caminar (gait recognition).
   - Firma manuscrita.
   - Patrón de escritura en teclado.

3. **Biometría multimodal** → combinación de dos o más métodos biométricos para mayor precisión (ejemplo: huella + rostro en un control de acceso).

### ⚙️ ¿Cómo funciona la seguridad biométrica?

El proceso tiene **cuatro fases principales**:

1. **Captura** → Se obtiene una muestra (ejemplo: foto del rostro, huella escaneada).
2. **Extracción de características** → El sistema convierte la muestra en un **código matemático único (plantilla biométrica)**.
3. **Almacenamiento** → La plantilla se guarda en una base segura (local o en la nube).
4. **Comparación** → Cuando intentas autenticarte, el sistema compara tu muestra actual con la plantilla registrada.

Ejemplo:

- Desbloqueo de un smartphone → el sensor de huella compara tu dedo con el patrón almacenado en el dispositivo.

### 🧩 Ejemplos de seguridad biométrica

- **Smartphones** → desbloqueo con huella (Touch ID), rostro (Face ID).
- **Bancos** → reconocimiento facial en apps de banca móvil.
- **Aeropuertos** → control migratorio con escaneo de iris y rostro (Ej: Aeropuerto de Changi, Singapur).
- **Edificios corporativos** → acceso con huella digital o tarjeta + biometría.
- **Computadoras** → Windows Hello permite iniciar sesión con huella o reconocimiento facial.

### 🔍 ¿Son seguros los escáneres biométricos? – Mejoras y preocupaciones

#### Mejoras:

- Cada vez más precisos gracias a **IA y machine learning**.
- Más difíciles de falsificar que contraseñas.
- Eliminan la necesidad de recordar claves.

#### Preocupaciones:

- Algunos sensores son vulnerables a engaños:

  - Huellas falsas hechas con silicona.
  - Fotos o máscaras para engañar reconocimiento facial básico.

- Riesgo de **falsos positivos** (autorizar a alguien incorrecto).
- Riesgo de **falsos negativos** (rechazar al usuario legítimo).

Ejemplo: en 2017, un grupo de investigadores alemanes desbloqueó un **iPhone X** con una máscara 3D del rostro del dueño.

### ⚠️ Biometría: preocupaciones sobre la identidad y la privacidad

- **Rastreo masivo:** gobiernos y empresas podrían usar reconocimiento facial sin consentimiento (ej: cámaras en espacios públicos).
- **Pérdida de anonimato:** tu rostro y huella te identifican siempre, no puedes cambiarlos.
- **Discriminación algorítmica:** sistemas de reconocimiento facial que fallan más con ciertos grupos étnicos o de género.

### 🔓 Preocupaciones sobre la seguridad de los datos biométricos

- Si un **password** se filtra → puedes cambiarlo.
- Si una **huella o iris** se filtra → **no puedes reemplazarlo**.
  Esto hace que los datos biométricos sean un **objetivo muy valioso** para hackers.

Ejemplo:

- En 2019, la base de datos **Biostar 2** (con huellas y rostros de millones de personas) fue expuesta en Internet por un fallo de seguridad.

### 🛡️ Formas de proteger la identidad biométrica

1. **Almacenamiento local** → guardar plantillas en el dispositivo (ej: iPhone guarda Face ID en el chip Secure Enclave).
2. **Cifrado avanzado** de las plantillas biométricas.
3. **Biometría multimodal** → combinar varios factores para mayor seguridad.
4. **Políticas de privacidad claras** → que los usuarios sepan cómo se usa y almacena su biometría.
5. **Complemento con otros factores** (ejemplo: biometría + PIN = 2FA).

### 📌 Conclusiones sobre la biometría

- La biometría es una herramienta poderosa de autenticación porque se basa en **identificadores únicos e intransferibles**.
- Ofrece **comodidad y seguridad superior** a las contraseñas tradicionales.
- Sin embargo, tiene **riesgos de privacidad y seguridad de datos** que deben gestionarse con cifrado, regulación y prácticas éticas.
- Lo ideal es usarla como parte de un esquema **MFA (autenticación multifactor)** y no como único factor.

---

[🔼](#índice)

---

## **237. Tokens o certificados de seguridad**

### 1 — ¿Token vs certificado? (comparación rápida)

- **Token**: normalmente es un _valor_ (cadena u objeto) emitido por un servicio de identidad. Puede ser _portador_ (bearer token) o _prueba de posesión_. Ej.: JWT, OAuth access token, TOTP.

  - Ventaja: práctico para APIs y SSO; fácil de emitir/invalidar.
  - Riesgo: si alguien roba un _bearer token_ puede usarlo hasta que expire/revoque.

- **Certificado**: es un documento digital (habitualmente **X.509**) que vincula una clave pública con una identidad, firmado por una Autoridad de Certificación (CA). Requiere posesión de la **clave privada** correspondiente.

  - Ventaja: autenticación basada en asimetría y no solo en portador; permite TLS, firma de código, mTLS.

  - Riesgo: compromiso de la clave privada o de la CA.

### 2 — Tipos y ejemplos de tokens

#### a) OTP: tokens de un solo uso

- **HOTP (event-based)** — RFC 4226.
- **TOTP (time-based)** — RFC 6238 (ej.: Google Authenticator, Authy).

  **Cómo funciona (TOTP)**: servidor y dispositivo comparten secreto; cada 30 s se genera un HMAC con la hora -> código de 6 dígitos.

  **Uso**: 2FA para cuentas.

#### b) Tokens de software (JWT)

- **JWT (JSON Web Token)**: formato compacto con `header.payload.signature`. Muy usado en OAuth/OIDC como access token / ID token.

- **Ejemplo de encabezado y payload (JSON)**:

  ```json
  // header
  {"alg":"RS256","typ":"JWT"}

  // payload
  {"iss":"https://id.example.com","sub":"user123","aud":"api://resource","exp":1710000000,"iat":1709996400}
  ```

- **Firma**: el emisor firma el `header.payload` con su clave privada; el receptor verifica con la clave pública (o JWKS).

#### c) Tokens opacos / de referencia

- Cadena sin estructura visible; el servidor introspecciona el token en la base de datos o endpoint de introspección (OAuth2).

  **Uso**: mayor control de revocación (backend mantiene estado).

#### d) OAuth2 tokens

- **Access token** (corto): usado para acceder a la API.

- **Refresh token** (más largo): usado para renovar access tokens.

- Flujos: Authorization Code (con PKCE), Client Credentials, Implicit (deprecated), Resource Owner Password (deprecated).

#### e) SAML assertions / Cookies de sesión / API keys

- SAML: XML firmado para SSO en entornos enterprise.
- Cookies: tokens de sesión típicos de webapps (precaución: proteger HttpOnly, SameSite).
- API keys: tokens largos que identifican a un cliente; son bearers si no hay prueba de posesión.

### 3 — Tipos y ejemplos de certificados

#### a) X.509 (PKI)

- **Root CA** → **Intermediate CA(s)** → **Leaf (server/client) cert**.
- Usado en **TLS/SSL**, firma de código, S/MIME, autenticación de clientes (mTLS).
- Contiene: Subject, SubjectAltName (SAN), clave pública, periodo de validez, firma de CA.

#### b) Certificados de cliente (mTLS)

- El servidor solicita certificado al cliente; ambos comprueban la identidad con PKI.
- Uso: acceso a APIs críticas, acceso remoto administrativo.

#### c) Smartcards / PIV / CAC

- Contienen claves privadas y certificados; se accede con PIN físico.
- Usado en entornos gubernamentales y corporativos para autenticación y firma.

#### d) Hardware tokens & FIDO/WebAuthn

- **FIDO2 / WebAuthn** usa claves públicas generadas en el dispositivo (Secure Enclave / TPM / YubiKey). Conceptualmente parecido a certificados: el servidor almacena la clave pública y verifica firmas del dispositivo.

### 4 — Cómo funcionan (principios técnicos)

#### Certificados (asimétrico)

1. Generas par de claves: `private.key` (privada) y `public.key`.
2. Creas un CSR (Certificate Signing Request) y lo firmas con tu `private.key`.
3. Envíalo a una CA; la CA firma y emite un certificado (`server.crt`).
4. En TLS el servidor presenta `server.crt`; el cliente valida cadena hasta una CA de confianza.
   **Comprobación** = verificar firma + fechas + revocación.

#### Tokens (puede ser simétrico o asimétrico)

- **JWT RS256**: emisor firma con clave privada RSA; receptor verifica con clave pública.
- **JWT HS256**: firma HMAC con una clave compartida (simétrica).
- **TOTP**: HMAC con secreto compartido y contador basado en tiempo.

### 5 — Ejemplos prácticos (comandos útiles)

#### Generar CA y firmar un certificado con OpenSSL (lab)

```bash
# 1) generar clave CA
openssl genpkey -algorithm RSA -out ca.key -pkeyopt rsa_keygen_bits:4096

# 2) crear certificado raíz auto-firmado
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -out ca.crt -subj "/CN=MyRootCA"

# 3) generar clave del servidor
openssl genpkey -algorithm RSA -out server.key -pkeyopt rsa_keygen_bits:2048

# 4) CSR para el servidor
openssl req -new -key server.key -out server.csr -subj "/CN=example.com"

# 5) firmar CSR con la CA (incluyendo SAN)
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
  -out server.crt -days 825 -sha256 \
  -extfile <(printf "subjectAltName=DNS:example.com,IP:127.0.0.1")
```

#### Crear y firmar un JWT (conceptual, en shell)

```bash
# header + payload (base64url) -> firma con clave RSA
# (esto ilustra el flujo; en producción usa librerías JWT)
HEADER='{"alg":"RS256","typ":"JWT"}'
PAYLOAD='{"sub":"user1","iss":"https://id.example.com","exp":1710000000}'
H64=$(printf '%s' "$HEADER" | openssl base64 -e -A | tr '+/' '-_' | tr -d '=')
P64=$(printf '%s' "$PAYLOAD" | openssl base64 -e -A | tr '+/' '-_' | tr -d '=')
SIGNING_INPUT="$H64.$P64"
SIG=$(printf "%s" "$SIGNING_INPUT" | openssl dgst -sha256 -sign private.pem | openssl base64 -e -A | tr '+/' '-_' | tr -d '=')
JWT="$SIGNING_INPUT.$SIG"
echo "$JWT"
```

### 6 — Ciclo de vida y gestión

1. **Generación / CSR**
2. **Emisión** por CA / Identity Provider (IdP)
3. **Distribución** (deploy de certificado en servidores o entrega del token)
4. **Almacenamiento seguro** de claves privadas (HSM, Key Vault, Secure Enclave)
5. **Rotación / renovación** periódica (certificados con fechas de expiración; tokens con expiración corta)
6. **Revocación**: CRL, OCSP (certificados) o listas/revocation endpoints / introspection (tokens)
7. **Auditoría** y registro de uso

Herramientas comunes: **ACME/Let’s Encrypt** (automatiza emisión TLS), **HashiCorp Vault**, **AWS KMS**, **Azure Key Vault**, proveedores de CA y gestión de certificados (Venafi).

### 7 — Revocación y verificación en tiempo real

- **CRL (Certificate Revocation List)**: lista publicada por la CA.
- **OCSP (Online Certificate Status Protocol)**: consulta puntual del estado de un certificado.
- **OCSP stapling**: el servidor incluye respuesta OCSP firmada para mejorar privacidad y latencia.
- **Tokens**: revocación por eliminar el token del store, o exigir tokens cortos y usar refresh tokens controlados.

### 8 — Riesgos, ataques comunes y contramedidas

#### Riesgos frecuentes

- **Robo de token** (XSS, almacenamiento inseguro, logs, repositorios).
- **Robo de clave privada** (mal protegido en disco o en copia).
- **Replay attacks** (uso repetido de tokens si no se usan jti/nonce o expiración).
- **Alg=none / JWT misconfiguration** (aceptar tokens sin verificar firma).
- **CA compromise / mis-issuance** (cualquier certificado emitido por CA confiable puede ser usado).
- **SIM swapping** (para SMS OTP).
- **Token leakage via Referer headers** (URLs con tokens).

#### Contramedidas

- **Almacenar claves privadas en HSM / KMS**; no en texto plano.
- **Usar short-lived tokens** (p. ej. access token 5–15 min) + refresh tokens con revocación y detección.
- **Validar `iss`, `aud`, `exp`, `nbf`, `jti`** en JWTs; validar firma con clave pública JWKS.
- **No guardar tokens sensibles en localStorage** (preferir HttpOnly Secure cookies con SameSite).
- **TLS obligatorio** siempre (HTTPS).
- **Implementar mTLS** para APIs sensibles.
- **Rotación periódica de claves y certificados**; automatizar renovación (ACME, Cert-Manager).
- **Harden CA**: reducir número de CAs confiables, usar Certificate Transparency, monitorizar logs CT.
- **Protección contra XSS/CSRF** en apps que usan tokens.
- **Detectar anomalías y revocar tokens comprometidos** (SIEM, detección UEBA).

### 9 — Buenas prácticas recomendadas (resumen accionable)

- **Preferir tokens opacos o short-lived JWTs** con refresh tokens y revocación.
- **Proteger claves privadas** en HSM / Cloud KMS.
- **Verificar firma + claims** (`iss`, `aud`, `exp`) en cada token.
- **Usar PKCE** para clientes públicos (mobile/SPAs) en flow Authorization Code.
- **No usar SMS para operaciones críticas** si hay alternativa más segura (TOTP/Push/FIDO).
- **Usar certificados modernos**: TLS 1.2/1.3, ECDSA/P-256 o RSA ≥ 2048, SHA-256 o mejor.
- **Habilitar OCSP stapling** y monitorizar revocaciones.
- **Rotación automática** de certs y claves cuando sea posible.
- **Registrar y auditar** emisión/uso/revocación de credenciales.

### 10 — Casos de uso prácticos (dónde aplicar cada cosa)

- **TLS / HTTPS** → certificados X.509 (server certs).
- **Mutual TLS (mTLS)** → autenticación fuerte entre cliente y servidor (APIs).
- **SSO (SAML / OIDC)** → tokens JWT / SAML assertions para sesión federada.
- **2FA / login** → TOTP / push notifications / hardware tokens.
- **APIs públicas** → Bearer access tokens (preferible: short-lived + introspection).
- **Firma de código / documentos** → certificados de firma con cadena CA.

### 11 — Ejemplo comparativo: JWT (bearer) vs Certificado (mTLS)

- **JWT (bearer)**

  - Fácil de usar en HTTP Authorization header.
  - Ideal para delegación (OAuth).
  - Riesgo: token robado = acceso (si no hay mitigaciones).

- **mTLS (certificado cliente)**

  - Requiere posesión de clave privada; más resistente a robo del token.
  - Más complejo de gestionar (PKI, distribución de certs, revocación).
  - Ideal para servicios máquina-a-máquina y APIs críticas.

---

[🔼](#índice)

---

## **238. Control de acceso basado en roles (RBAC)**

El **Control de Acceso Basado en Roles (RBAC)** es un **modelo de autorización** que otorga permisos a **roles** (grupos de privilegios) y luego asigna esos roles a **usuarios** o entidades (servicios, cuentas). En vez de dar permisos por usuario individual, asignas roles con un conjunto de permisos predefinidos: más orden, menos errores y mejor gobernanza.

### Conceptos clave (lo esencial)

- **Rol**: conjunto de permisos (ej.: `InventoryManager`, `Support`, `DBAdmin`).
- **Permiso**: acción sobre un recurso (ej.: `read:orders`, `write:products`, `DROP TABLE`).
- **Usuario / sujeto**: identidad que recibe roles (humano, cuenta de servicio).
- **Asignación (RoleAssignment)**: vínculo `usuario → rol`.
- **Sesión** (opcional): rol(s) activados por un usuario en un momento dado.
- **Jerarquía de roles**: un rol puede heredar permisos de otro (ej.: `Manager` > `Employee`).
- **Restricciones / SoD (Separation of Duties)**: reglas que impiden combinar roles conflictivos (ej.: el que crea pagos no puede aprobarlos).

### Modelos RBAC (breve)

- **RBAC0**: roles simples (bases).
- **RBAC1**: añade jerarquías de roles.
- **RBAC2**: añade restricciones/SoD.
- **RBAC3**: combina jerarquías y restricciones.
  (Estos nombres son del estándar académico; en la práctica verás combinaciones.)

### Cómo funciona (flujo nominal)

1. Diseñas **roles** y defines sus **permisos**.
2. Asignas roles a usuarios o grupos (manual, por sincronización con IdP o por reglas).
3. Al ejecutar una operación, el sistema **verifica** si alguno de los roles del usuario contiene el permiso requerido.
4. Si hay permiso → autorización; si no → denegación.

### Ventajas principales

- **Menor superficie de error**: menos permisos ad hoc.
- **Escalabilidad**: asignar grupos en vez de usuarios individuales.
- **Auditoría y cumplimiento**: fácil revisar “quién tiene qué”.
- **Principio de menor privilegio**: roles bien diseñados facilitan aplicar least privilege.

### Limitaciones / riesgos

- **Mala ingeniería de roles** (roles demasiado amplios) => exceso de privilegios.
- **Explosión de roles** (demasiados roles muy específicos) => gestión compleja.
- **Necesita gobernanza**: aprovisionamiento, revisiones periódicas, certificaciones.
- **RBAC no cubre atributos dinámicos** (para eso existe ABAC/Policy-based Access).

### Ejemplo de diseño: tienda e-commerce (matriz simple)

| Roles / Permisos   | `read_products` | `write_products` |   `read_orders` | `refund_orders` |
| ------------------ | --------------: | ---------------: | --------------: | --------------: |
| `Customer`         |               ✓ |                  | ✓ (solo propio) |                 |
| `InventoryManager` |               ✓ |                ✓ |                 |                 |
| `SupportAgent`     |               ✓ |                  |     ✓ (tickets) |                 |
| `Finance`          |                 |                  |       ✓ (todos) |               ✓ |

Nota: aquí `read_orders` puede tener _scope_ (propios vs todos); RBAC se complementa con scoping o checks adicionales.

### Ejemplos técnicos prácticos

#### 1) PostgreSQL — roles y permisos

```sql
-- Crear rol con permisos de solo lectura en la base 'mydb'
CREATE ROLE app_readonly NOINHERIT;
GRANT CONNECT ON DATABASE mydb TO app_readonly;
GRANT USAGE ON SCHEMA public TO app_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO app_readonly;

-- Asegurar que futuras tablas concedan SELECT por defecto
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO app_readonly;

-- Crear un usuario y asignar el rol
CREATE USER reporting WITH PASSWORD 'secret';
GRANT app_readonly TO reporting;

-- Ver roles y pertenencias en psql
\du
```

**Resultado:** `reporting` obtiene permisos por pertenecer a `app_readonly`. Para revocar: `REVOKE app_readonly FROM reporting;`.

#### 2) Kubernetes RBAC (Role + RoleBinding)

Archivo `role.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: store
  name: pod-reader
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "watch", "list"]
```

Archivo `rolebinding.yaml`:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods-binding
  namespace: store
subjects:
  - kind: User
    name: alice # identidad autenticada por el cluster
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

Comandos: `kubectl apply -f role.yaml && kubectl apply -f rolebinding.yaml`. Luego `alice` podrá listar pods sólo en el namespace `store`.

#### 3) Node.js / Express — middleware sencillo de RBAC

```js
// middleware roleCheck.js
module.exports = function requiredRole(role) {
  return (req, res, next) => {
    // suponiendo req.user.roles = ['user','admin']
    if (!req.user || !req.user.roles || !req.user.roles.includes(role)) {
      return res.status(403).json({ error: "Forbidden" });
    }
    next();
  };
};

// uso
const roleCheck = require("./roleCheck");

app.get("/admin/dashboard", authenticateJwt, roleCheck("admin"), (req, res) => {
  res.send("Admin dashboard");
});
```

**Idea:** las claims del JWT contienen roles (`roles: ['admin','support']`); el middleware verifica.

#### 4) AWS IAM (concepto)

En AWS no creas “usuarios → permisos” directamente; se crean **roles** y **políticas** (JSON) que se adjuntan a roles o usuarios. Un EC2 puede asumir un _role_ para obtener credenciales temporales: útil para acceso máquina-a-máquina sin credenciales embebidas.

### Gobernanza de roles: ciclo de vida y prácticas

1. **Role engineering (diseño)**: identificar funciones del negocio y mapear permisos mínimos.
2. **Role creation**: nombres claros (`svc-`, `app-`, `role-`) y documentación.
3. **Provisioning / assignment**: automatizar (SCIM, IdP sync, GPO, scripts).
4. **Revisión periódica**: certificaciones trimestrales/anuales (attestation).
5. **Revocación & offboarding**: desasignar roles al cambiar o salir un empleado.
6. **Role mining**: analizar logs para proponer roles basados en patrones de uso.
7. **Emergency / Break-glass**: accesos de emergencia con logging y aprobación posterior.
8. **Just-in-Time (JIT)**: acesso temporal (Azure AD Privileged Identity Management, AWS SSO) para admins.

### Separación de funciones (SoD) y restricciones

- **Ejemplo de SoD**: `InvoiceCreator` ≠ `InvoiceApprover`. Si una persona tiene ambos roles, existe riesgo de fraude.
- Implementar **constraints**: reglas que evitan asignar roles conflictivos, o exigir aprobación adicional.

### Auditoría y monitoreo

- Mantén logs de **assignments** y **usos de privilegios**.
- Revisa quién activó roles privilegiados, cuándo y por qué.
- Encadena RBAC con SIEM/EDR para detectar uso anómalo de roles privilegiados (UEBA).

Comandos útiles:

- Kubernetes: `kubectl get rolebindings --all-namespaces`
- Postgres: `\du`
- AD: revisar membresía de grupos (`Get-ADGroupMember`)
- Logs/SIEM: buscar `role_assignment` y `failed_authorization` events.

### Buenas prácticas (acción rápida)

- Diseña roles centrados en **funciones del negocio**, no en tecnicismos.
- **Principio de menor privilegio**: permisos mínimos requeridos.
- Usa **grupos** (AD / IdP) y asigna roles a grupos, no a usuarios individuales.
- **Automatiza** provisioning/deprovisioning (SCIM, IAM sync).
- Implementa **revisiones periódicas** y certificación de acceso.
- Nombra roles con convención clara (`env-team-role`: `prod-inventory-manager`).
- Documenta los **permisos** de cada rol y casos de uso.
- Evita roles “omnipotentes” (`superadmin`) salvo lo estrictamente necesario y controlado.
- Considera **JIT** y **time-bound roles** para tareas sensibles.
- Monitorea y alerta por uso inusual de roles privilegiados.

### RBAC vs ABAC (cuando elegir qué)

- **RBAC**: simple, fácil de auditar, excelente cuando los permisos se alinean a funciones estáticas.
- **ABAC (Attribute-Based Access Control)**: permite políticas basadas en atributos (usuario, recurso, entorno). Más flexible para reglas dinámicas (ej.: permitir `read` si `user.department == resource.ownerDept` y hora en rango).
- **Patrón híbrido**: muchos sistemas usan RBAC + atributos para el scoping (roles + condiciones).

### Checklist rápido para arrancar con RBAC en tu organización

- [ ] Mapear funciones de negocio y permisos necesarios.
- [ ] Crear catálogo de roles con documentación.
- [ ] Implementar en IdP / sistemas críticos (Azure AD, Kubernetes, DB).
- [ ] Automatizar asignaciones (onboarding/offboarding).
- [ ] Definir revisiones periódicas y SoD.
- [ ] Instrumentar logging y alertas para roles privilegiados.
- [ ] Probar procesos de emergencia y JIT.

---

[🔼](#índice)

---

## **239. Listas de control de acceso (ACL)**

### ¿Qué es una ACL?

Una **ACL** es una estructura de datos (una lista) asociada a un recurso que especifica **qué sujetos** (usuarios, grupos, hosts, redes) pueden realizar **qué acciones** (leer, escribir, ejecutar, conectar) sobre ese recurso.

Las ACLs aparecen en muchos niveles: sistemas de ficheros, dispositivos de red (routers, firewalls), almacenamiento en la nube, bases de datos y aplicaciones.

### Idea general de funcionamiento

- Una **entrada** de ACL (ACE — Access Control Entry) típicamente contiene: sujeto (user/group/addr), permiso (r/w/x, connect, tcp/udp), y una acción (allow/deny).

- Cuando alguien intenta acceder al recurso, el sistema **evalúa** la ACL y decide permitir o denegar.

- **Semánticas comunes**:

  - _First-match / first-applicable_ (ej. Cisco): se evalúan ACEs en orden, la primera que coincide determina el resultado.
  - _Implicit deny_: si ninguna ACE coincide, se suele denegar por defecto.
  - _Deny precedence_ (Windows): una entrada DENY suele prevalecer sobre una ALLOW aunque ambas existan.
  - _Máscaras/“mask”_ (POSIX ACLs): limitan permisos efectivos para la clase «group» y usuarios nombrados.

### Tipos de ACLs (dónde las verás)

#### 1) ACLs de sistema de ficheros (filesystem ACLs)

- **POSIX ACLs (Linux)**: extienden el modelo owner/group/other con entradas por usuario/grupo. Herramientas: `setfacl`, `getfacl`.
- **NTFS ACLs (Windows)**: más ricas (herencia, permisos finos, ACE deny/allow, audit). Herramientas: `icacls`, `Get-Acl`/`Set-Acl` en PowerShell.

**Ejemplo Linux (POSIX ACL):**

```bash
# Crear archivo
touch reporte.txt

# Dar permisos adicionales a usuario 'gustavo'
setfacl -m u:gustavo:rwx reporte.txt

# Dar permisos al grupo 'dev' lectura y ejecución
setfacl -m g:dev:rx reporte.txt

# Ver ACLs
getfacl reporte.txt
```

Salida `getfacl` (ejemplo):

```
# file: reporte.txt
# owner: root
# group: root
user::rw-
user:gustavo:rwx
group::r--
mask::rwx
other::r--
```

Nota: `mask::rwx` controla permisos máximos aplicables a las entradas de grupo y usuarios nombrados.

**Ejemplo Windows (icacls):**

```powershell
# Conceder lectura a 'Gustavo' en C:\datos\reporte.txt
icacls "C:\datos\reporte.txt" /grant "Gustavo:(R)"

# Denegar modificación a un usuario
icacls "C:\datos\reporte.txt" /deny "UsuarioMal:W"

# Ver ACLs
icacls "C:\datos\reporte.txt"
```

#### 2) ACLs de red (routers / switches / firewalls)

- Controlan qué hosts/redes pueden hablar con qué servicios en la red.
- Implementación típica en routers (Cisco IOS), firewalls, o iptables/nftables.

**Ejemplo Cisco (first-match):**

```text
ip access-list extended BLOQUEAR_HTTP
  deny tcp any host 192.0.2.10 eq 80
  permit ip any any

interface GigabitEthernet0/1
  ip access-group BLOQUEAR_HTTP in
```

Explicación: la primera ACE que coincida decide. Si un paquete es TCP a 192.0.2.10:80 será DENIED; otros paquetes serán permitidos.

**Ejemplo Linux (iptables):**

```bash
# Permitir SSH solo desde la subred de administración
sudo iptables -A INPUT -p tcp --dport 22 -s 10.0.0.0/24 -j ACCEPT

# Bloquear SSH de cualquier otro origen
sudo iptables -A INPUT -p tcp --dport 22 -j DROP
```

#### 3) ACLs en la nube (objetos / VPC / storage)

- **Cloud network ACLs** (ej. AWS NACL): reglas numeradas, **stateless**, evaluadas por orden ascendente; hay que permitir explícitamente inbound y outbound.

- **Storage ACLs** (ej. S3 ACL): definían acceso por grantee; hoy en día las políticas de bucket / IAM son el patrón recomendado.

(Nota: en la nube conviene distinguir Security Groups (stateful) vs NACLs (stateless) — cada proveedor tiene su terminología.)

#### 4) ACLs a nivel de aplicación y base de datos

- Aplicaciones pueden tener ACLs por recurso (documento, carpeta) que indican qué usuarios/grupos pueden ver/editar.
- Bases de datos: `GRANT` / `REVOKE` funcionan como ACLs para tablas/esquemas.

**Ejemplo SQL:**

```sql
GRANT SELECT ON schema.orders TO reporting_role;
GRANT UPDATE ON schema.orders TO finance_role;
REVOKE UPDATE ON schema.orders FROM public;
```

### Conceptos importantes y trampas comunes

#### Orden y evaluación

- **Orden importa** en muchas ACLs (Cisco, iptables). En Windows NTFS, el motor de autorización evalúa ACEs y DENY puede tener precedencia.
- **Implicit deny**: si no hay coincidencia, normalmente se deniega.

#### Efective permissions / máscara

- En POSIX ACLs la `mask` define permiso máximo efectivo sobre entradas de grupo y usuarios nombrados; esto suele confundir a admins (parece que un permiso no funciona cuando el mask es restrictivo).

#### Herencia

- En ficheros: ACLs por defecto en directorios (`default` entries en POSIX) definen ACLs de nuevos ficheros creados dentro del directorio.
- En Windows: ACEs pueden ser heredadas por subcarpetas y archivos; cuidado con el orden y con el “propagar a subcarpetas”.

#### Estado y logging

- ACLs a nivel de red pueden ser **stateful** o **stateless**; esto cambia cómo hay que permitir tráfico de retorno.
- Registrar hits/denegaciones ayuda a depurar reglas.

### Ventajas y desventajas

**Ventajas**

- Gran granularidad: control objeto-por-objeto.
- Flexible: puedes dar excepciones puntuales para casos concretos.
- Amplio soporte (ficheros, redes, cloud, apps).

**Desventajas**

- Escalabilidad: muchas ACLs por objeto generan complejidad y error humano.
- Mantenimiento: "rule sprawl" y conflictos entre reglas.
- Difícil auditoría si no hay centralización.
- Posible impacto en rendimiento si las ACLs son muy largas (en hardware de red muy antiguo).

### Buenas prácticas (acciónable)

1. **Principio de menor privilegio**: empezar por deny-all y permitir lo mínimo.
2. **Preferir grupos/roles** en vez de asignar ACLs por usuario individual.
3. **Whitelisting** en vez de blacklisting cuando sea posible.
4. **Documentar** cada ACL: qué protege, por qué existe y quién la aprobó.
5. **Revisión periódica** (access reviews) y eliminación de entradas obsoletas.
6. **Usar defaults/delegación**: default ACLs para directorios colaborativos.
7. **Centralizar** (cuando sea posible) la gestión de ACLs (IAM, herramientas de gestión de políticas).
8. **Auditar & monitorizar** hits y denegaciones; mantén logs y alertas para cambios de ACL.
9. **Testear** reglas en modo monitor (o en entorno staging) antes de activarlas en producción.
10. **Evitar reglas contradictorias**: un DENY y un ALLOW para el mismo sujeto crea confusión; documenta intención.

### Comparación rápida: ACL vs RBAC vs Capabilities

- **ACL**: lista de permisos asociada a cada **objeto** (objetos conocen quién puede hacer qué). Granular y orientada al recurso.
- **RBAC**: asigna permisos a **roles** y luego roles a **usuarios** (gestión por roles). Escala mejor para organizaciones.
- **Capabilities**: tokens/handles que el sujeto posee y que le dan permiso (modelo por posesión). Muy útil en sistemas distribuidos o microservicios.

En la práctica lo común es **combinar**: RBAC para aprovisionamiento + ACLs puntuales en recursos críticos.

### Ejemplos prácticos — mini-workflows

#### a) Configurar ACL de colaboración en Linux (directorio compartido)

```bash
# Crear directorio
mkdir /srv/proyecto
chown root:dev /srv/proyecto
chmod 2770 /srv/proyecto    # setgid para que nuevos ficheros hereden grupo 'dev'

# Default ACLs para que todos los archivos nuevos den r/w a grupo 'dev'
setfacl -d -m g:dev:rwx /srv/proyecto

# Dar acceso a usuario gustavo (si no está en grupo dev)
setfacl -m u:gustavo:rwx /srv/proyecto
```

#### b) Crear ACL en router para bloquear un host específico (Cisco)

```text
ip access-list extended BLOQUEAR_HOST
  deny ip host 198.51.100.23 any
  permit ip any any

interface GigabitEthernet0/1
  ip access-group BLOQUEAR_HOST in
```

#### c) Revisar permisos efectivos en Windows (PowerShell)

```powershell
# Ver ACL
Get-Acl "C:\datos\reporte.txt" | Format-List

# Comprobar Access Check para un usuario (Effective Access) - GUI: "Effective Access" tab en propiedades de Seguridad
# En PS, usar Test-Path combined with tokens or syscalls for deep checks (herramientas de terceros ayudan)
```

### Troubleshooting (errores comunes y cómo diagnosticarlos)

- **"No puedo acceder aunque la ACL diga que tengo permiso"** → revisar **mask** (POSIX), herencia, y permisos efectivos.
- **Reglas de red que no funcionan** → revisar orden de ACLs (first-match), políticas stateful/stateless y reglas de retorno.
- **Acceso inesperado** → comprobar membresías de grupo, ACE heredadas, y superusuarios/roles privilegiados.
- **Reglas que no se aplican en cloud** → comprobar si hay políticas de IAM/bucket policy que **sobrescriban** la ACL; revisa logs (flow logs, CloudTrail).

### Resumen ejecutivo (lo esencial para recordar)

- Las **ACLs** controlan acceso por objeto; son muy potentes pero pueden volverse difíciles de gestionar a escala.
- Entender **orden de evaluación**, **herencia** y **efective permissions** es clave para administrarlas correctamente.
- Combínalas con **roles/grupos (RBAC)**, automatización y auditoría para tener un control sostenible.
- Siempre **documenta, audita y revisa** tus ACLs.

---

[🔼](#índice)

---

## **240. Control de acceso basado en atributos (ABAC)**

El **Control de Acceso Basado en Atributos (ABAC, Attribute-Based Access Control)** es un modelo de autorización que decide si permitir o denegar una operación en función de **atributos** (propiedades) del _usuario_, _recurso_, _acción_ y _entorno_, evaluados contra **políticas**.
En lugar de “¿este usuario tiene este rol?”, ABAC responde: “¿las _propiedades_ actuales del usuario y del recurso cumplen la política para esta acción en este contexto?”.

### 1. ¿Qué son los atributos?

Los atributos son pares clave-valor que describen entidades del sistema. Tipos típicos:

- **Atributos del sujeto (usuario / servicio)**: `user.id`, `user.department`, `user.clearance`, `user.role`, `user.location`.
- **Atributos del recurso**: `resource.owner`, `resource.classification`, `resource.tags`, `resource.env`.
- **Atributos de la acción**: `action` (read, write, delete), `method`, `scope`.
- **Atributos ambientales (contexto)**: `time`, `ip`, `device.score`, `geo`, `mfa` (si el usuario usó MFA), `session.risk`.

Ejemplo: `user.department = "finance"`, `resource.classification = "confidential"`, `time.hour = 14`.

### 2. Arquitectura y componentes (XACML-like)

ABAC suele seguir el patrón PAP/PEP/PDP/PIP:

- **PAP (Policy Administration Point)**: donde se crean/gestionan las políticas.
- **PDP (Policy Decision Point)**: motor que evalúa la política frente a atributos y devuelve `Permit`/`Deny`. (Ej.: OPA, Axiomatics, motor XACML).
- **PEP (Policy Enforcement Point)**: punto que intercepta la petición (app, API gateway, sidecar) y solicita decisión al PDP; aplica la decisión.
- **PIP (Policy Information Point)**: fuentes de atributos (IdP, HR system, CMDB, DB, device telemetry).

Flujo resumido: cliente → PEP (recolecta atributos) → PDP (evalúa) → PEP aplica decisión → respuesta.

### 3. ¿Cómo funciona? (ejemplo en palabras)

Política (en lenguaje natural):

> “Permitir lectura de un documento si `user.department == document.ownerDepartment` **y** `user.clearance >= document.classification`. Además, permitir solo en horario laboral y si el acceso viene de la red corporativa.”

Al recibir la petición, el PEP envía al PDP los atributos necesarios (usuario, documento, hora, IP). El PDP evalúa la política y devuelve Permit o Deny.

### 4. Ejemplos concretos

#### 4.1 Ejemplo simple en Rego (OPA)

**Política (Rego):**

```rego
package authz

default allow = false

allow {
  input.action == "read"
  input.resource.type == "document"
  input.user.department == input.resource.owner_department
  input.user.clearance >= input.resource.classification
  input.env.time_hour >= 9
  input.env.time_hour < 18
  input.env.client_network == "corp-net"
}
```

**Solicitud (input JSON enviada al PDP):**

```json
{
  "action": "read",
  "resource": {
    "type": "document",
    "id": "doc-123",
    "owner_department": "finance",
    "classification": 2
  },
  "user": {
    "id": "u-42",
    "department": "finance",
    "clearance": 3
  },
  "env": {
    "time_hour": 14,
    "client_network": "corp-net",
    "ip": "10.0.5.17"
  }
}
```

**Resultado:** `allow = true` → PEP permite la lectura.

> Nota: en producción el PDP deberá validar la integridad y frescura de atributos (p. ej. `user.clearance` proviene del HR o IdP).

#### 4.2 Ejemplo AWS (ABAC mediante tags)

AWS soporta ABAC mediante _tags_ en recursos y atributos del principal (`aws:PrincipalTag`). Política que permite acciones en S3 sólo si el tag de la cuenta coincide:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::example-bucket/*",
      "Condition": {
        "StringEquals": {
          "aws:PrincipalTag/Department": "${aws:ResourceTag/Department}"
        }
      }
    }
  ]
}
```

Explicación: la petición se permite sólo si el tag `Department` del usuario (principal) coincide con el `Department` del recurso.

#### 4.3 Ejemplo en una app (middleware sencillo)

```js
// pseudo-middleware en Express
function abacMiddleware(policyEngine) {
  return async (req, res, next) => {
    const input = {
      user: req.user, // claims del JWT o IdP
      resource: req.resource, // metadatos del recurso
      action: req.method == "GET" ? "read" : "write",
      env: { ip: req.ip, time: new Date().getHours() },
    };
    const decision = await policyEngine.evaluate(input); // llama al PDP (ej. OPA)
    if (decision.allow) return next();
    return res.status(403).send("Forbidden");
  };
}
```

### 5. Ventajas de ABAC

- **Finura y flexibilidad**: políticas muy detalladas (propietario, clasificación, contexto).
- **Contextual / dinámico**: decisiones en función del momento, ubicación, riesgo o dispositivo.
- **Escalabilidad lógica**: menos necesidad de crear mil roles para cubrir combinaciones; una política ABAC puede cubrir muchas situaciones.
- **Adecuado para entornos multi-tenant / SaaS**: aplicar restricciones por etiquetas/atributos.
- **Encaja con Zero Trust**: decisiones basadas en contexto y riesgo.

### 6. Desventajas y retos

- **Complejidad**: políticas pueden volverse difíciles de entender y mantener.
- **Gestión de atributos (PIP)**: necesitas fuentes de atributos fiables, coherentes y con baja latencia.
- **Rendimiento**: evaluación de políticas complejas en línea puede añadir latencia; requiere caché y optimización.
- **Trazabilidad y auditoría**: explicar por qué se permitió/denegó puede ser más complejo; se requiere buen logging de la evaluación.
- **Errores de políticas**: mal redactadas pueden provocar brechas (falsos positivos) o denegaciones legítimas (falsos negativos).

### 7. Casos de uso típicos

- Acceso a documentos con clasificación y propietario (ej.: `ownerDepartment`).
- Control de acceso multitenant en SaaS (tenant tag en usuarios y recursos).
- Acceso condicional (p. ej. sólo desde red corporativa y con MFA).
- APIs internas críticas: mTLS + ABAC por atributos del servicio consumidor.
- Cumplimiento: permitir sólo personal con `clearance >= level` para datos sensibles (sanidad, finanzas).

### 8. Implementación práctica — hoja de ruta resumida

1. **Inventario y modelado de atributos**

   - Define qué atributos vas a usar (catálogo): nombres, tipos, fuentes.

2. **Identificar fuentes de verdad (PIP)**

   - IdP (Azure AD, Okta), HR, CMDB, DB, device posture (MDM/EDR). Asegurar su integridad y latencia.

3. **Elegir tecnología PDP/PEP**

   - Open Policy Agent (OPA/Rego), motores XACML, soluciones comerciales (Axiomatics, Ping), o condiciones nativas del cloud (IAM Conditions en GCP/Azure/AWS).

4. **Diseñar políticas simples y revisarlas**

   - Comienza con políticas pequeñas y auditadas; test en modo “report-only” antes de bloquear.

5. **Integrar PEP en puntos de entrada**

   - API gateway, sidecar en k8s, middleware en apps, WAF, proxy reverse.

6. **Pruebas y validación (unit & integration tests para políticas)**

   - Test con inputs variados; usar fuzzing; revisar logs.

7. **Monitoreo y auditoría**

   - Log de cada evaluación: input, política aplicada, decisión, timestamp.

8. **Gobernanza**

   - Versionado de políticas, aprobación, revisión periódica, registro de cambios.

### 9. Buenas prácticas y recomendaciones

- **Principio deny-by-default**: si falta atributo o falla la evaluación, denegar.
- **Atributos canónicos**: normaliza nombres (ej.: `department`, no `dept`/`Departamento`).
- **Fuente única de verdad** (single source of truth) para atributos críticos (ej.: HR para `employment_status`).
- **Caché con TTL**: cachea atributos frecuentes con expiración para rendimiento; pero asegúrate de coherencia.
- **Auditoría completa**: logea `who, what, why` (usuario, recurso, política que se aplicó, atributos usados).
- **Política "explainability"**: el PDP debe poder devolver por qué denegó/permitió (helpful for investigations).
- **Comenzar por casos de uso críticos** y expandir (no intentes cubrir todo de golpe).
- **Testing as code**: versiona políticas en repositorio, haz CI con tests de políticas.
- **Combinación con RBAC**: usa RBAC para permisos “estáticos” y ABAC para reglas dinámicas — híbrido es práctico.

### 10. Ejemplo comparativo rápido (RBAC vs ABAC)

- **RBAC**: fácil de entender; “Asigno roles → se aplican permisos”; bueno cuando funciones son estables.
- **ABAC**: más dinámico y preciso; “decisión basada en atributos y contexto”; mejor cuando las condiciones cambian con frecuencia (multitenancy, contexto, scoping fino).
- **Híbrido**: usar RBAC para BLs (coarse-grain) y ABAC para reglas finas (scoping y condiciones).

### 11. Checklist corto para empezar con ABAC

- [ ] Catalogar atributos y definir fuentes (PIP).
- [ ] Seleccionar PDP/PEP (OPA, XACML, cloud conditions).
- [ ] Escribir 3–5 políticas iniciales para casos críticos.
- [ ] Implementar PEP en gateway o middleware y ejecutar en modo log-only.
- [ ] Crear pipeline CI para políticas (tests automáticos).
- [ ] Habilitar logging de decisiones y métricas de latencia.
- [ ] Establecer gobernanza y revisiones periódicas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 19**                                 | **Siguiente 21**                                |
| ------------------ | -------------------------------------------- | ----------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_19_Conceptos_basicos_de_IDS_IPS.md) | [⏩](./8_21_Understand_Common_Hacking_Tools.md) |
