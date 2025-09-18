| **Inicio**         | **atrás 13**                       | **Siguiente 15**       |
| ------------------ | ---------------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./7_13_Protocol_Analyzers.md) | [⏩](./7_15_RADIUS.md) |

---

## **Índice**

| Temario                                                                                        |
| ---------------------------------------------------------------------------------------------- |
| [186. ¿Qué es Kerberos?](#186-qué-es-kerberos)                                                 |
| [187. Explicación de la autenticación Kerberos](#187-explicación-de-la-autenticación-kerberos) |

# **Kerberos**

## **186. ¿Qué es Kerberos?**

![Kerberos](/img/7_Troubleshooting_Tools/Kerberos.jpg "Kerberos")

### 🔹 1. Definición de autenticación Kerberos

La **autenticación Kerberos** es un protocolo de seguridad basado en tickets que permite a los usuarios y servicios de una red **demostrarse mutuamente su identidad de manera segura** sin enviar contraseñas en texto claro.

👉 Fue desarrollado en el MIT en los años 80 como parte del **Proyecto Athena** y se convirtió en estándar (RFC 4120).

👉 Es ampliamente utilizado en **Windows (Active Directory)**, sistemas UNIX/Linux y muchos servicios corporativos.

### 🔹 2. ¿Qué es Kerberos?

Kerberos es un **protocolo de autenticación de red** que:

- Usa **criptografía de clave simétrica** (y a veces asimétrica en extensiones).
- Emplea un **tercero confiable** llamado **KDC (Key Distribution Center)**.
- Entrega **tickets** que prueban la identidad de un usuario o servicio.

📌 **Ejemplo real**: cuando inicias sesión en Windows en una red corporativa, **Kerberos valida tu identidad contra el Active Directory** y te da acceso a recursos (servidores, impresoras, correos, etc.) sin tener que escribir tu contraseña cada vez.

### 🔹 3. Ventajas de Kerberos

1. **Seguridad**: no transmite contraseñas en texto claro.
2. **Single Sign-On (SSO)**: el usuario inicia sesión una vez y obtiene acceso a múltiples servicios.
3. **Autenticación mutua**: no solo el servidor autentica al usuario, sino también al revés.
4. **Resistencia a ataques de repetición**: usa marcas de tiempo y tickets temporales.
5. **Integración**: ampliamente soportado en Windows, Linux, bases de datos (ej. PostgreSQL, SQL Server).

### 🔹 4. ¿Cómo funciona la autenticación Kerberos?

Kerberos se basa en **tickets y llaves**. El proceso general:

1. **Login inicial**:

   - El usuario ingresa usuario/contraseña en su PC.
   - Se genera una clave derivada de la contraseña.

2. **Ticket-Granting Ticket (TGT)**:

   - El cliente solicita al **KDC/AS (Authentication Server)** un TGT.
   - Si las credenciales son correctas, el KDC envía un TGT cifrado con la clave secreta del **Ticket Granting Server (TGS)**.

3. **Solicitar acceso a un servicio**:

   - El cliente usa su TGT para pedir al TGS un **Service Ticket** (ST).

4. **Acceso al servicio**:

   - El cliente presenta el ST al servidor de destino (ej. servidor de archivos).
   - El servidor valida el ticket con ayuda del KDC.
   - Si es válido, se establece comunicación segura.

👉 Todo esto ocurre de manera automática y transparente para el usuario.

### 🔹 5. Beneficios de la autenticación Kerberos

- Evita el **envío de contraseñas** en cada conexión.
- Reduce el riesgo de **ataques de hombre en el medio (MITM)**.
- Favorece la **eficiencia en redes grandes** gracias a los tickets temporales.
- Facilita el **control centralizado** (Active Directory).

### 🔹 6. ¿Cuáles son las debilidades de Kerberos?

1. **Punto único de fallo**: si el **KDC** cae, la red no puede autenticar usuarios.
2. **Sincronización de tiempo**: requiere que relojes estén sincronizados (generalmente con NTP).
3. **Complejidad**: configuración inicial complicada.
4. **Objetivo de ataques**: si un atacante roba el TGT o compromete el KDC, puede acceder a todo.
5. **No protege el contenido**: solo autentica; para cifrado de datos se necesitan protocolos adicionales (TLS, IPSec).

### 🔹 7. Descripción general del flujo del protocolo Kerberos

El flujo se divide en **tres fases principales**:

#### 🔸 1. Autenticación inicial (AS Exchange)

- Cliente → **AS (Authentication Server)**: solicita autenticación.
- AS responde con un **TGT** (Ticket Granting Ticket).

#### 🔸 2. Solicitud de servicio (TGS Exchange)

- Cliente → **TGS (Ticket Granting Server)**: usa el TGT para pedir acceso a un servicio.
- TGS emite un **Service Ticket (ST)**.

#### 🔸 3. Acceso al servicio (AP Exchange)

- Cliente → **Servidor de aplicación**: presenta el ST.
- Servidor valida el ticket y concede acceso.

📌 **Ejemplo**:

- Usuario inicia sesión → obtiene TGT.
- Usuario abre Outlook → usa TGT para pedir ST de Exchange.
- Outlook presenta el ST a Exchange → acceso concedido.

### 🔹 8. ¿Kerberos está obsoleto?

❌ **No está obsoleto**.

- Sigue siendo el **estándar de facto** en **Active Directory** y muchos entornos UNIX/Linux.
- Sin embargo, en algunos casos se complementa o reemplaza con:

  - **OAuth 2.0 / OpenID Connect** (para aplicaciones web modernas).
  - **SAML** (entornos federados).
  - **FIDO2/WebAuthn** (autenticación sin contraseñas).

👉 En redes empresariales tradicionales, **Kerberos sigue siendo esencial**.

### 🔹 9. Preguntas frecuentes sobre la autenticación Kerberos

#### ❓ ¿Kerberos transmite contraseñas?

No. Usa **claves derivadas** y **tickets cifrados**, nunca envía la contraseña real por la red.

#### ❓ ¿Kerberos es lo mismo que Active Directory?

No. **AD usa Kerberos** como protocolo de autenticación, pero AD incluye muchas más funciones (políticas, grupos, objetos).

#### ❓ ¿Qué pasa si expira un ticket?

El usuario debe renovar el ticket. Normalmente esto ocurre automáticamente en segundo plano.

#### ❓ ¿Puedo usar Kerberos en Linux?

Sí, es compatible (ej. con `krb5.conf`) y usado en servicios como NFS, PostgreSQL, Apache, etc.

#### ❓ ¿Kerberos cifra los datos transmitidos?

No. Solo autentica identidades. Para proteger el contenido se usan otros protocolos (ej. TLS).

✅ **Resumen final:**

Kerberos es un **protocolo de autenticación basado en tickets** usado en redes modernas, sobre todo en entornos empresariales. Ofrece **seguridad, autenticación mutua y SSO**, aunque depende fuertemente de la disponibilidad del KDC y sincronización horaria. Está muy vigente, especialmente en Active Directory, aunque convive con métodos modernos como OAuth2 y SAML.

---

[🔼](#índice)

---

## **187. Explicación de la autenticación Kerberos**

### 1) ¿Qué es Kerberos (resumen)?

**Kerberos** es un **protocolo de autenticación** basado en tickets diseñado para permitir que clientes y servicios se autentiquen mutuamente en una red sin enviar contraseñas en texto claro. Fue desarrollado en el MIT (Proyecto Athena) y está estandarizado (principalmente RFC 4120, Kerberos v5). Es el método de autenticación por defecto en entornos como **Active Directory**.

Beneficios clave: Single Sign-On (SSO), autenticación mutua, resistencia a replays (timestamps), evita enviar la contraseña repetidamente.

### 2) Componentes principales

- **Cliente (C)**: el usuario / máquina que quiere autenticarse (ej. `alice@EXAMPLE.COM`).
- **KDC (Key Distribution Center)**: servicio central que contiene dos roles internos:

  - **AS (Authentication Server)** — valida credenciales y emite TGT.
  - **TGS (Ticket Granting Server)** — emite tickets de servicio basándose en el TGT.
    En la práctica AS y TGS conviven en el mismo KDC.

- **Service (S)**: servidor que provee un recurso (ej. `HTTP/web01.example.com@EXAMPLE.COM`), posee una clave (almacenada en su keytab o en la cuenta de servicio).
- **Realm**: dominio administrativo Kerberos (p.ej. `EXAMPLE.COM`).
- **Tickets**:

  - **TGT (Ticket Granting Ticket)**: prueba que el cliente ya fue autenticado y permite pedir tickets de servicio sin reingresar contraseña.
  - **ST (Service Ticket)**: ticket que el cliente presenta al servicio objetivo.

### 3) Flujo general (alto nivel — tres fases)

```
1) AS Exchange (Obtener TGT)
C -> AS : AS_REQ (id cliente)
AS -> C : AS_REP (TGT encrypted with K_tgs, and session key encrypted with K_client)

2) TGS Exchange (Pedir ticket de servicio)
C -> TGS: TGS_REQ (TGT + authenticator, service principal)
TGS -> C: TGS_REP (Service Ticket encrypted with service key, + session key encrypted with client key)

3) AP Exchange (Presentar ticket al servicio)
C -> S: AP_REQ (Service Ticket + authenticator)
S -> C: (opcional) AP_REP (mutual auth)
```

Más abajo pongo el mismo flujo con más detalle técnico.

### 4) Detalle técnico del flujo (qué cifra qué y por qué)

> Notación:

- `K_c` = clave derivada de la contraseña del cliente (solo conocida por el cliente y el KDC).
- `K_tgs` = clave del servicio TGS (sólo conocida por el KDC/TGS).
- `K_s` = clave del servicio (conocida por el servicio S y el KDC).
- `K_c,tgs` = clave de sesión entre cliente y TGS (generada por KDC).
- `K_c,s` = clave de sesión entre cliente y servicio S (generada por KDC).

#### Paso A — Autenticación inicial (AS_REQ / AS_REP)

1. El cliente pide autenticación enviando su principal (ej. `alice@EXAMPLE.COM`) en **AS_REQ**.
2. Si se requiere **pre-authentication**, el cliente primero envía un timestamp cifrado con `K_c` (PA-ENC-TIMESTAMP) para demostrar que conoce la clave derivada de su contraseña.
3. **AS** valida y genera:

   - **TGT**: contiene `K_c,tgs`, la identidad del cliente y tiempos; **TGT** es cifrado con `K_tgs` (la clave secreta del TGS/krbtgt account) — de modo que sólo TGS puede leerlo.
   - Un **enc_part**: que contiene `K_c,tgs` cifrado con `K_c` (la clave del cliente), y otros datos.

4. **AS_REP** llega al cliente: el cliente puede descifrar el enc_part con su contraseña y obtener `K_c,tgs`. El TGT no es legible por el cliente (está cifrado con la clave del TGS).

> Resultado: cliente tiene `K_c,tgs` + TGT; no envió la contraseña en texto.

#### Paso B — Petición de ticket de servicio (TGS_REQ / TGS_REP)

1. Para acceder a `HTTP/web01.example.com`, el cliente envía **TGS_REQ** al TGS incluyendo:

   - El **TGT** (proporcionado por AS),
   - Un **Authenticator** (timestamp y datos cifrados con `K_c,tgs`) que prueba frescura, y
   - el nombre del servicio solicitado.

2. El **TGS** descifra el TGT con su clave `K_tgs`, obtiene `K_c,tgs`, valida el authenticator y genera:

   - **Service Ticket**: contiene `K_c,s` y datos del cliente, cifrado con `K_s` (clave del servicio).
   - Un **enc_part** para el cliente: contiene `K_c,s` cifrado con `K_c,tgs`.

3. **TGS_REP** vuelve al cliente; el cliente descifra el enc_part con `K_c,tgs` y obtiene `K_c,s`. El Service Ticket permanece cifrado con `K_s` (solo el servicio S podrá leerlo).

#### Paso C — Autenticación al servicio (AP_REQ / AP_REP)

1. Cliente envía **AP_REQ** al servicio S que contiene:

   - El **Service Ticket** (cifrado con `K_s`) y
   - Un **Authenticator** (timestamp etc.) cifrado con `K_c,s`.

2. El servicio descifra el Service Ticket con `K_s`, obtiene `K_c,s`, verifica el authenticator y (si aplica) responde con **AP_REP** cifrado con `K_c,s` — de este modo se consigue **autenticación mutua**.

### 5) Conceptos prácticos y ejemplos (comandos)

#### En Linux (MIT Kerberos / Heimdal)

- Obtener TGT (login Kerberos):

```bash
kinit alice@EXAMPLE.COM
# pedirá contraseña; tras éxito, klist mostrará el TGT
klist
# borrar ticket
kdestroy
```

- Ver tickets y servicios:

```bash
klist -f -e   # muestra flags y enctypes
```

- Usar una keytab (servicio que tiene su clave en /etc/krb5.keytab):

```bash
kinit -k -t /etc/krb5.keytab host/web01.example.com@EXAMPLE.COM
```

- Consumir un servicio HTTP con Kerberos (SPNEGO) en Linux:

```bash
curl --negotiate -u : -b ~/cookies.txt -c ~/cookies.txt http://web01.example.com/
# necesita que el navegador/cliente realice GSSAPI usando el TGT.
```

#### En Windows / Active Directory

- `klist` en PowerShell o cmd para listar tickets.
- AD usa una cuenta `krbtgt` (la cuenta del servicio TGS) para cifrar/descifrar TGTs.
- Para exponer un servicio para Kerberos, se asigna un SPN (Service Principal Name) a una cuenta de servicio:

```powershell
# ejemplo: establecer SPN para la cuenta svcWeb
setspn -S HTTP/web01.example.com DOMAIN\svcWeb
```

El servidor web tiene la clave asociada a esa cuenta (normalmente en AD), que es la que el KDC usa para cifrar el ticket del servicio.

### 6) Tiempos, validez y renovación

- **Timestamps** y **skew**: Kerberos depende de relojes sincronizados (NTP): si la diferencia de tiempo supera el _clock skew_ (por defecto \~5 minutos), los authenticators fallan.
- **Ticket lifetime**: configurable por el KDC; en AD por defecto TGT = 10 horas, ticket renovable por defecto 7 días (estos valores pueden variar en la política de Kerberos).
- **Renewable** tickets permiten renovar sin reingresar credenciales dentro de la ventana renovable.

### 7) Extensiones y características modernas

- **PKINIT**: autenticación inicial usando certificados (evita depender de contraseña para preauth).
- **Constrained delegation / protocol transition (S4U in AD)**: delegación con restricciones para escenarios web→backend.
- **FAST** (Flexible Authentication Secure Tunneling): protección adicional para preauth.
- **AES en lugar de DES/RC4**: usos modernos recomiendan AES (AES256/128), deshabilitar RC4/DES.

### 8) Debilidades y vectores de ataque (explicación — sin pasos de explotación)

Kerberos es robusto pero tiene puntos débiles si no se administra correctamente:

- **Punto único de fallo / objetivo**: el **KDC** (y la cuenta `krbtgt`) es crítico. Compromiso del KDC → control sobre emisión de tickets.
- **Golden Ticket**: si un atacante obtiene la clave `krbtgt`, puede forjar TGTs válidos (“Golden Ticket”) y acceder a recursos de forma persistente.
- **Kerberoasting**: los tickets de servicio (TGS) cifrados con la clave del servicio pueden extraerse y atacarse offline si la cuenta del servicio tiene contraseña débil; el objetivo es recuperar la contraseña de la cuenta de servicio.
- **Pass-the-ticket**: reuso de tickets robados en sistemas Windows.
- **Dependencia del tiempo**: relojes fuera de sincronía provocan fallos.
- **Delegación mal configurada**: delegación excesiva permite que un servicio actúe en nombre de usuarios con demasiados privilegios.

> Importante: no voy a dar pasos para explotar estas fallas. Abajo tienes **mitigaciones** defensivas.

### 9) Mitigaciones / buenas prácticas (recomendadas)

- **Protege el KDC** físicamente y lógicamente; segrega y endurece las máquinas KDC/Domain Controllers.
- **Rotación de la clave `krbtgt`** (cambio de contraseña de la cuenta krbtgt) en dos pasos para mitigar Golden Tickets.
- **Usa contraseñas fuertes para cuentas de servicio** o mejor: **Managed Service Accounts (gMSA)** en AD.
- **Deshabilita cifrados inseguros** (DES, RC4) y fuerza AES.
- **Monitorea eventos Kerberos** (Windows: eventos 4768, 4769, 4624 etc.) y alertas anómalas (picos de TGS_REQ, peticiones fuera de horario).
- **Limita y audita delegación**: usa **constrained delegation** en AD y evita delegación sin restricciones.
- **Aplicar MFA** para accesos de administración y relativamente a cuentas privilegiadas.
- **Clave de gestión de SPN**: usa keytabs seguros, restringe cuentas que tienen SPN y revisa su pertenencia a grupos privilegiados.
- **Instalar parches** y aplicar hardening (remove writable DACLs to service accounts).

### 10) Ejemplos de uso real (casos prácticos)

- **SSO en Windows**: usuario inicia sesión en su PC con dominio → obtiene TGT automáticamente → al abrir Outlook/SharePoint/SMB, el cliente solicita STs sin pedir contraseña otra vez.
- **Web con Kerberos (SPNEGO/GSSAPI)**: navegador usa el TGT para obtener ST para `HTTP/web01.example.com` y se autentica sin prompt. Apache con mod_auth_kerb o mod_auth_gssapi lo soporta.
- **NFS/Kerberos**: NFSv4 montado con Kerberos (krb5i/krb5p) para autenticación y opcional integridad/confidencialidad.

### 11) ¿Kerberos está obsoleto?

No. Kerberos **no está obsoleto**: sigue siendo la base de autenticación en Active Directory (entornos corporativos), en NFS, kerberos-aware applications, y sigue evolucionando con extensiones (PKINIT, AES, S4U). Para aplicaciones web modernas se suele usar OAuth2/OpenID Connect para SSO federado, pero en intranets Kerberos sigue siendo muy útil y eficiente.

### 12) Preguntas frecuentes (FAQ rápidas)

**Q:** ¿Qué diferencia hay entre TGT y Service Ticket?
**A:** TGT prueba que el KDC confía en el usuario y permite pedir ST. El Service Ticket es lo que presentas al servicio para autenticarte ante ese servicio concreto.

**Q:** ¿Por qué Kerberos necesita que las máquinas tengan el mismo tiempo?
**A:** Usa timestamps en authenticators para prevenir replays; relojes fuera de sincronía invalidan autenticators.

**Q:** ¿Qué es un SPN?
**A:** Service Principal Name — nombre único del servicio (ej. `HTTP/web01.example.com`) que el KDC usa para localizar la clave del servicio.

**Q:** ¿Qué pasa si alguien roba mi TGT?
**A:** Un TGT válido puede ser usado mientras esté vigente; por eso conviene políticas de renovación, detección de usos anómalos y, en casos de compromiso, revocar/rotar contraseñas y la clave `krbtgt`.

**Q:** ¿Cómo veo mis tickets en Windows?
**A:** Ejecuta `klist` en CMD/PowerShell.

**Q:** ¿Puedo usar Kerberos fuera de una red corporativa?
**A:** Sí, pero es típico en redes administradas con un KDC/AD. Para Internet federado se usan otras soluciones (SAML, OIDC).

### 13) Resumen práctico (comandos útiles)

```bash
# Obtener TGT (Linux)
kinit alice@EXAMPLE.COM

# Ver tickets
klist

# Usar keytab para servicio
kinit -k -t /etc/krb5.keytab host/web01.example.com@EXAMPLE.COM

# Borrar tickets
kdestroy
```

En Windows:

```powershell
# Ver tickets
klist

# Revisar eventos de Kerberos en el Event Viewer (Security logs)
# Buscar Event IDs: 4768 (TGT request), 4769 (service ticket)
```

---

[🔼](#índice)

---

| **Inicio**         | **atrás 13**                       | **Siguiente 15**       |
| ------------------ | ---------------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./7_13_Protocol_Analyzers.md) | [⏩](./7_15_RADIUS.md) |
