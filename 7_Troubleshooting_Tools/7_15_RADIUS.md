| **Inicio**         | **atrás 14**             | **Siguiente 16**     |
| ------------------ | ------------------------ | -------------------- |
| [🏠](../README.md) | [⏪](./7_14_Kerberos.md) | [⏩](./7_16_LDAP.md) |

---

## **Índice**

| Temario                                                                                                                                                        |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [188. RADIUS (Servicio de autenticación remota de acceso telefónico de usuario)](#188-radius-servicio-de-autenticación-remota-de-acceso-telefónico-de-usuario) |
| [189. Cómo funciona la autenticación RADIUS](#189-cómo-funciona-la-autenticación-radius)                                                                       |

# **RADIUS**

## **188. RADIUS (Servicio de autenticación remota de acceso telefónico de usuario)**

![RADIUS](/img/7_Troubleshooting_Tools/RADIUS.jpeg "RADIUS")

### 🔹 ¿Qué es RADIUS?

**RADIUS** (Remote Authentication Dial-In User Service, traducido como _Servicio de acceso telefónico de usuario con autenticación remota_) es un **protocolo cliente-servidor** que se utiliza para **autenticación, autorización y contabilización (AAA: Authentication, Authorization, Accounting)** en redes.

👉 Fue desarrollado en 1991 por Livingston Enterprises y hoy es un estándar (RFC 2865, RFC 2866).

👉 Se usa en redes corporativas, ISP, WiFi empresariales, VPNs y servicios que necesitan validar credenciales de usuarios centralizadamente.

**Ejemplo cotidiano:**

Cuando un empleado se conecta al WiFi corporativo usando sus credenciales de dominio, el punto de acceso WiFi (AP) no valida la contraseña directamente, sino que reenvía la solicitud al servidor RADIUS, el cual confirma si el usuario es legítimo.

### 🔹 ¿Cómo funciona la autenticación RADIUS?

El flujo básico tiene 3 participantes:

1. **Cliente RADIUS (NAS — Network Access Server):**

   Puede ser un router, firewall, switch, access point o VPN concentrator. Su función es recoger las credenciales del usuario (usuario/contraseña, token, certificado, etc.) y enviarlas al servidor RADIUS.

2. **Servidor RADIUS:**

   Procesa la petición de autenticación, valida credenciales contra una base de datos interna o externa (AD, LDAP, SQL, etc.) y devuelve respuesta (permitir/denegar).

3. **Usuario final (suplicante):**

   El dispositivo del usuario (PC, móvil, etc.) que envía las credenciales al NAS.

#### 🔁 Flujo paso a paso de autenticación RADIUS

1. El **usuario** intenta conectarse a la red (por ejemplo, al WiFi corporativo).
2. El **NAS** (ejemplo: AP WiFi) recibe las credenciales y crea un **Access-Request** dirigido al servidor RADIUS.
3. El **Servidor RADIUS** procesa la petición:

   - Valida usuario y contraseña contra su base de datos o un directorio externo (ejemplo: Active Directory).
   - Aplica políticas (qué VLAN asignar, ancho de banda, si se requiere MFA, etc.).

4. El servidor RADIUS responde con:

   - **Access-Accept** → el usuario puede conectarse.
   - **Access-Reject** → acceso denegado.
   - **Access-Challenge** → pide más información (ej. segundo factor).

5. El NAS aplica la decisión: permite, deniega o pide más datos al usuario.

### 🔹 ¿Cómo se utilizan los servidores RADIUS?

Los servidores RADIUS centralizan la autenticación y son muy usados en entornos donde hay **muchos dispositivos de red** o **muchos usuarios**.

### 📌 Casos de uso comunes:

- **Empresas y universidades** → control de acceso al WiFi con credenciales de dominio.
- **ISP (proveedores de Internet)** → validación de usuarios de banda ancha, fibra o DSL.
- **VPN corporativas** → validar usuarios antes de conceder acceso remoto.
- **Acceso administrativo a switches/routers** → en vez de claves locales en cada dispositivo, se usa RADIUS centralizado.

### 📌 Ejemplo práctico:

- Una universidad configura un servidor **FreeRADIUS**.
- Los puntos de acceso WiFi (clientes RADIUS) envían las credenciales de los alumnos al servidor.
- El servidor valida contra una base LDAP (o AD de Microsoft).
- Dependiendo del rol (alumno/profesor/administrador), el RADIUS asigna:

  - VLAN de estudiantes,
  - VLAN de docentes,
  - o acceso administrativo.

### 🔹 Beneficios de RADIUS

✔ Centralización de usuarios y políticas.

✔ Escalabilidad (puede dar servicio a miles de dispositivos y usuarios).

✔ Compatible con diferentes bases de datos (AD, LDAP, SQL).

✔ Soporta diferentes métodos de autenticación (contraseña, certificados, tokens, EAP).

✔ Permite contabilidad: registrar quién se conectó, cuándo y cuánto ancho de banda usó.

### 🔹 Ejemplo real de configuración (muy simplificado)

En **FreeRADIUS** (Linux), un usuario se podría definir así en `/etc/raddb/users`:

```txt
juan   Cleartext-Password := "12345"
       Service-Type = Framed-User,
       Framed-Protocol = PPP,
       Reply-Message = "Bienvenido Juan"
```

Cuando "juan" se conecte al WiFi y escriba su contraseña, el servidor RADIUS lo valida y devuelve **Access-Accept**.

✅ **Resumen:**

- RADIUS es un **protocolo de AAA (Autenticación, Autorización y Contabilización)**.
- Se basa en un modelo cliente-servidor (NAS ↔ RADIUS ↔ Base de datos).
- Centraliza credenciales y políticas de red.
- Se usa masivamente en WiFi corporativos, VPNs y entornos ISP.

---

[🔼](#índice)

---

## **189. Cómo funciona la autenticación RADIUS**

### 1) Resumen rápido — qué es RADIUS y quién participa

- **RADIUS** = _Remote Authentication Dial-In User Service_. Protocolo AAA: **A**utenticación, **A**utorización, **A**contabilización.
- **Participantes**:

  - **Supplicant**: el usuario/dispositivo (cliente final).
  - **NAS (Network Access Server)**: el _authenticator_ — p. ej. AP Wi-Fi, switch, VPN concentrator, router. Actúa como cliente RADIUS.
  - **Servidor RADIUS**: valida credenciales, aplica políticas y responde (Access-Accept/Reject/Challenge).

- Transporte: UDP (por defecto) a puertos **1812** (autenticación) y **1813** (accounting). Legacy: 1645/1646.
- Seguridad inicial: **shared secret** (secreto compartido) entre NAS y servidor RADIUS.

### 2) Formas de autenticación que RADIUS puede transportar

- **PAP** (Password Authentication Protocol): contraseña en "claro" desde supplicant al NAS; RADIUS cifra la contraseña en el atributo `User-Password` (con método MD5 + shared-secret).
- **CHAP** (Challenge-Handshake Authentication Protocol) / **MS-CHAPv2**: challenge/response. RADIUS transporta los atributos de CHAP.
- **EAP** (Extensible Authentication Protocol): RADIUS encapsula paquetes EAP (atributo `EAP-Message`). Esto permite métodos TLS (EAP-TLS), PEAP, TTLS, MSCHAPv2 dentro de PEAP, etc. Es el método más usado en Wi-Fi Enterprise (802.1X / WPA/WPA2-Enterprise).

### 3) Flujo básico (sin EAP) — ejemplo PAP (pasos)

1. **Supplicant → NAS**: el usuario introduce usuario+contraseña; NAS recibe credenciales.
2. **NAS → RADIUS (Access-Request)**: NAS envía `Access-Request` al servidor RADIUS con atributos como:

   - `User-Name`, `User-Password` (cifrado con shared secret), `NAS-IP-Address`, `NAS-Port`, `Calling-Station-Id` (MAC cliente), `Called-Station-Id` (SSID)...

3. **Servidor RADIUS** valida el usuario (local DB, LDAP/AD, SQL, etc.).
4. **Servidor → NAS**:

   - `Access-Accept` → incluye atributos de autorización (p.ej. `Framed-IP-Address`, `Tunnel-Private-Group-ID` para VLAN, `Reply-Message`)
   - o `Access-Reject`
   - o `Access-Challenge` (si se requiere info adicional).

5. **NAS** aplica la decisión (concede/niega acceso, asigna VLAN o IP).

### 4) Flujo con CHAP / MS-CHAPv2 (resumen)

- NAS genera `CHAP-Challenge`, supplicant responde con `CHAP-Response`. NAS pasa challenge/response a RADIUS en `Access-Request` (atributos CHAP-Password, CHAP-Challenge).
- RADIUS valida la respuesta (p. ej. contra un hash en la DB o mediante proxy a AD) y responde `Access-Accept` o `Reject`.
- MS-CHAPv2 añade atributos para derivar claves MS-MPPE (para cifrado enlace, p. ej. VPN / PPP).

### 5) Flujo con EAP (802.1X / WPA2-Enterprise) — detalle práctico

Este es el flujo que verás en Wi-Fi Enterprise:

1. **EAPOL-Start / EAP Request/Response**

   - Supplicant <→> Authenticator (NAS/AP) usan **EAPOL** (link-layer) para intercambiar EAP frames.

2. **NAS ↔ RADIUS**

   - NAS encapsula los paquetes EAP en el atributo `EAP-Message` de RADIUS y los envía al servidor RADIUS en `Access-Request`.
   - RADIUS responde con `Access-Challenge` (encapsula una respuesta EAP) hasta que el método EAP termine (TLS handshake en EAP-TLS, PEAP inner MSCHAPv2, etc.).

3. **Éxito**

   - Si el método EAP valida al usuario, RADIUS devuelve `Access-Accept`. En `Access-Accept` el servidor puede incluir atributos con parámetros de sesión (VLAN, QoS, tiempo de sesión, o claves para cifrado si aplica).

4. **Derivación de claves**

   - Para WPA2-Enterprise, tras completarse EAP (por ejemplo EAP-TLS), el servidor RADIUS proporciona al AP material clave (MSK / keying material) vía atributos (por ejemplo `MS-MPPE-Send-Key`, `MS-MPPE-Recv-Key` en implementaciones Microsoft) o a través de intercambio definido; el AP usa ese material para completar el 4-way handshake con el supplicant y establecer las claves de enlace (PTK/GTK).

> En la práctica: _supplicant ↔ AP (EAPOL) ↔ RADIUS (EAP over RADIUS)_; el AP actúa como pasarela para EAP.

### 6) Protección del secreto de usuario en RADIUS (cómo se cifra User-Password)

Cuando se usa PAP, RADIUS no deja la password en texto en UDP:

- El `User-Password` se **cifra** por bloques de 16 bytes usando MD5 sobre `shared-secret + Request Authenticator` (RFC 2865). Es un método antiguo: **no es tan fuerte** como TLS, por eso PAP+RADIUS no se considera super seguro si el shared secret o el enlace está expuesto. Para EAP (p. ej. EAP-TLS) la autenticación ocurre dentro de TLS y es segura.

### 7) Atributos comunes en Access-Request / Access-Accept

- **Access-Request** (desde NAS): `User-Name`, `User-Password` (o `EAP-Message`), `NAS-IP-Address`, `NAS-Port`, `Calling-Station-Id`, `Called-Station-Id`, `Service-Type`.
- **Access-Accept** (respuesta): `Reply-Message`, `Framed-IP-Address`, `Filter-Id`, `Tunnel-Type`, `Tunnel-Medium-Type`, `Tunnel-Private-Group-ID` (VLAN), `Acct-Interim-Interval`, `Class`, `Vendor-Specific`.
- **Accounting**: `Acct-Status-Type` (Start/Stop/Interim), `Acct-Session-Id`, `Acct-Input-Octets`, `Acct-Output-Octets`.

### 8) Ejemplos prácticos — comandos y configs mínimas

#### a) Probar autenticación RADIUS local (FreeRADIUS) con `radtest` / `radclient`

- En servidor FreeRADIUS (ej. localhost) usa `radtest` (viene con freeradius-utils):

```bash
# sintaxis: radtest usuario contraseña servidor puerto secreto
radtest juan 12345 127.0.0.1 0 testing123
```

- Salida: verás Access-Request enviado y respuesta Access-Accept / Reject.
- Para depuración en servidor FreeRADIUS:

```bash
sudo freeradius -X
# y en otra terminal: radtest ...
```

`-X` ejecuta FreeRADIUS en modo debug muy verboso.

#### b) Ejemplo mínimo en `/etc/freeradius/3.0/mods-config/files/authorize` (archivo users)

```text
juan Cleartext-Password := "12345"
    Reply-Message = "Bienvenido Juan",
    Tunnel-Type = VLAN,
    Tunnel-Private-Group-ID = "20"
```

Cuando `juan` solicita acceso, el NAS recibirá `Tunnel-Private-Group-ID=20` y podrá ubicarlo en la VLAN 20.

#### c) WPA2-Enterprise con `wpa_supplicant` (cliente) + PEAP/MSCHAPv2

`/etc/wpa_supplicant/wpa_supplicant.conf`:

```ini
network={
    ssid="CorpSSID"
    key_mgmt=WPA-EAP
    eap=PEAP
    identity="juan@example.com"
    password="MiPassword"
    phase2="auth=MSCHAPV2"
    ca_cert="/etc/ssl/certs/ca.pem"   # opcional para validar servidor RADIUS
}
```

- AP configurado para usar RADIUS apuntará al servidor RADIUS con shared secret.
- Cuando el supplicant se conecta, verás en FreeRADIUS las transacciones EAP y la eventual `Access-Accept`.

### 9) RADIUS Accounting y CoA (Change of Authorization)

- **Accounting**: NAS envía `Accounting-Start` (Acct-Status-Type = Start) cuando inicia sesión y `Accounting-Stop` al desconectarse; incluye `Acct-Session-Id`, bytes transmitidos, timestamps. Útil para facturación y auditoría.
- **CoA (RFC 5176)**: el servidor (o un administrador) puede enviar `CoA-Request` al NAS para cambiar parámetros de sesión en caliente (ej. forzar desconexión, cambiar VLAN, modificar QoS).

### 10) Topologías avanzadas: proxy y backend

- **RADIUS Proxy**: servidores RADIUS pueden reenviar solicitudes a otros RADIUS (por realm, política). Muy usado en ISP o roaming federado (eduroam).
- **Backends de autenticación**:

  - Local DB (users file).
  - LDAP/Active Directory (vía LDAP bind o MS-RPC).
  - SQL (Postgres/MySQL).
  - Kerberos (para verificación de contraseñas en AD), etc.

### 11) Seguridad: riesgos y mitigaciones

**Riesgos**:

- Shared secret débil o expuesto → se pueden falsificar Access-Request (spoofing).
- RADIUS transporta datos por UDP → susceptible a spoofing/replay si no se protegen enlaces.
- PAP + RADIUS no es tan seguro si el enlace NAS→RADIUS no está protegido.

**Mitigaciones**:

- Usar **shared secrets fuertes** y rotarlos.
- **Proteger el transporte**: IPsec entre NAS y RADIUS o usar **RadSec** (RADIUS over TLS) cuando sea posible.
- Preferir **EAP-TLS** o PEAP (EAP con TLS túnel) en vez de PAP.
- Habilitar `Message-Authenticator` (HMAC-MD5) y validar integridad de paquetes.
- Filtrar/limitar desde qué IPs se aceptan solicitudes RADIUS.
- Monitorizar logs RADIUS e implementar rate-limit/anti-brute.

### 12) Ejemplo de flujo completo (WPA2-Enterprise, paso a paso)

1. Supplicant inicia asociación con AP (auth 802.11).
2. Supplicant envía `EAPOL-Start`; AP responde `EAP-Request/Identity`.
3. Supplicant → AP: `EAP-Response/Identity`.
4. AP → RADIUS (Access-Request): incluye `EAP-Message` con la identidad.
5. RADIUS ↔ Supplicant (vía AP): se realiza el intercambio EAP (EAP-TLS or PEAP->MSCHAPv2) encapsulado en `EAP-Message` y `State` attributes hasta completarse.
6. RADIUS → AP: `Access-Accept` + atributos (VLAN, keys if applicable).
7. AP completa 4-way handshake con supplicant usando material clave acordado y concede acceso a la red.

### 13) Buenas prácticas operativas

- Mantén reloj sincronizado (NTP) en servidores y NAS (evita problemas con EAP/TLS time checks).
- Centraliza logs y revisa eventos: picos de `Access-Request` pueden indicar ataque.
- Implementa redundancia de RADIUS (failover) y balanceo.
- Usa certificados válidos para EAP-TLS/PEAP y valida CA en el supplicant.
- Documenta secrets, IPs autorizadas y políticas de VLAN/QoS que aplicará RADIUS.

### 14) Diagnóstico rápido (herramientas)

- `radtest` / `radclient` (FreeRADIUS) — para probar Access-Request.
- `freeradius -X` — modo debug para ver exactamente qué recibe y cómo responde el servidor.
- Logs del NAS/AP (por ejemplo hostapd, controller, firewall).
- Wireshark/tshark — captura de RADIUS (filtrar `udp.port == 1812`), y para 802.1X/EAP, capturar EAPOL.

### Resumen final

- RADIUS actúa como **puente centralizado de AAA** entre elementos de acceso (APs, switches, VPNs) y la fuente de identidad (AD/LDAP/DB).
- Puede transportar **métodos inseguros (PAP/CHAP)** o, mejor, **EAP** (PEAP, EAP-TLS) para autenticación fuerte.
- La lógica consiste en: **NAS envía Access-Request → RADIUS valida → Access-Accept/Reject/Challenge → NAS aplica políticas**.
- Para producción: usar EAP-TLS/PEAP, transporte seguro (IPsec/RadSec), shared secrets fuertes y monitoreo.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 14**             | **Siguiente 16**     |
| ------------------ | ------------------------ | -------------------- |
| [🏠](../README.md) | [⏪](./7_14_Kerberos.md) | [⏩](./7_16_LDAP.md) |
