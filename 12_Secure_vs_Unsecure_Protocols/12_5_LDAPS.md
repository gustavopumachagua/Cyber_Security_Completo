| **Inicio**         | **atrás 4**            | **Siguiente 6**      |
| ------------------ | ---------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./12_4_DNSSEC.md) | [⏩](./12_6_SRTP.md) |

---

## **Índice**

| Temario                                                                                 |
| --------------------------------------------------------------------------------------- |
| [366. Cómo habilitar LDAPS](#366-cómo-habilitar-ldaps)                                  |
| [367. LDAP vs LDAPS: ¿cuál es la diferencia?](#367-ldap-vs-ldaps-cuál-es-la-diferencia) |

# **Lightweight Directory Access Protocol Secure (LDAPS)**

## **366. Cómo habilitar LDAPS**

![LDAPS](/img/12_Secure_vs_Unsecure_Protocols/LDAPS.png "LDAPS")

### 1) ¿Qué es LDAPS y puerto que usa

- **LDAPS** = LDAP sobre TLS/SSL: es **LDAP encapsulado en TLS** (conexión cifrada de forma implícita).
- Puerto estándar de LDAPS: **TCP 636** (LDAP “seguro”). (LDAP sin seguridad normalmente usa el puerto 389; STARTTLS usa 389 con un _upgrade_ a TLS).

### 2) Requisitos del certificado (reglas generales — muy importantes)

Para que un servidor LDAP (especialmente un **Domain Controller** de Active Directory) soporte LDAPS correctamente, el certificado instalado en el servidor debe cumplir al menos:

- Tener **clave privada accesible** en el almacén del equipo local (Local Computer\Personal).
- Incluir **Server Authentication** en los EKU (Enhanced Key Usage / OID 1.3.6.1.5.5.7.3.1).
- Tener el **nombre DNS** que usarán los clientes en el **Subject** o en **Subject Alternative Name (SAN)** (ej. `dc01.ejemplo.com` y/o `ldap.ejemplo.com`). Si vas a usar un alias o un balanceador, ese nombre debe estar en el SAN.
- Usar algoritmos/longitudes modernas (RSA ≥2048 o ECDSA; firmas SHA-256+) y que la cadena de certificación sea **de confianza** para los clientes (CA raíz o intermedia conocida por los clientes).
- En Active Directory, si usas SMTP para replicación u otras funciones avanzadas, hay requisitos adicionales (p. ej. GUID en SAN) en escenarios especiales — revisa la documentación de AD.

### 3) Resumen de flujo para habilitar LDAPS

1. **Obtener / firmar** un certificado que cumpla requisitos (CA interna con AD CS o CA pública).
2. **Instalar** el certificado (PFX) en el almacén **Local Computer → Personal** del servidor LDAP / Domain Controller.
3. **Reiniciar/recargar** el servicio LDAP (en Windows normalmente AD lo detecta; en OpenLDAP reinicia `slapd`).
4. **Abrir puerto 636** en firewall/NSG y probar con cliente.

### 4) Cómo habilitar LDAPS en **Active Directory (Domain Controller)** — paso a paso

#### Opción A — (lo más sencillo) usar **Autoenrollment / AD CS (Enterprise CA)**

1. **Crear/usar** una plantilla de certificado para controladores de dominio (ej. _Domain Controller_, _Domain Controller Authentication_). Esto incluirá EKU y permitirá inscripción automática en los controladores.
2. Configurar **Group Policy** / autoenrollment (si procede) para que los DC obtengan el certificado automáticamente.
3. El DC recibe el certificado y **AD Services** habilitarán LDAPS automáticamente cuando el certificado válido esté en `Personal` del equipo. No suele requerir pasos adicionales.

> Ventaja: gestión centralizada y renovación automática.
> Si no tienes AD CS, sigue la Opción B.

#### Opción B — (manual) solicitar e instalar certificado desde una CA (p. ej. CA externa)

#### Pedido/CSR en un DC (resumen rápido)

- Abre **mmc.exe → Add/Remove Snap-in → Certificates (Computer account → Local Computer)**.
- Si vas a generar CSR desde el DC con `certreq`, puedes crear un `request.inf` y correr:

```powershell
certreq -new request.inf request.req
# ... enviar request.req a la CA (web o submit)
# luego recibir request.cer y aceptar:
certreq -accept request.cer
```

> Importante: al crear la CSR incluye el SAN con los nombres DNS que usarán los clientes. (Microsoft describe cómo añadir SAN en solicitudes y la interacción con CA).

#### Importar PFX (si te entregan .pfx)

- En el DC usa MMC → Certificates (Local Computer) → Personal → All Tasks → Import → selecciona el `.pfx` y marca que la clave sea exportable si lo necesitas (en general **no** es obligatorio que sea exportable).
- Asegúrate que la cuenta _Local Computer_ tenga acceso a la clave privada.

#### Comprobación y puesta en marcha

- Una vez instalado, el servicio LDAP en el DC detecta el certificado y escucha LDAPS en el puerto 636 (no suele requerir reinicio del servicio AD, pero a veces un reinicio facilita la detección).

### 5) Cómo habilitar LDAPS en **OpenLDAP (slapd)** — ejemplo (Linux)

1. **Generar/obtener** certificados (server cert + key + CA cert). Ejemplo con OpenSSL (CA local):

```bash
# crear CA
openssl genpkey -algorithm RSA -out ca.key -pkeyopt rsa_keygen_bits:4096
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -out ca.crt -subj "/CN=Mi-CA"

# generar llave y CSR para el servidor LDAP
openssl genpkey -algorithm RSA -out ldap.key -pkeyopt rsa_keygen_bits:2048
openssl req -new -key ldap.key -out ldap.csr -subj "/CN=ldap.ejemplo.com"

# firmar con la CA
openssl x509 -req -in ldap.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
  -out ldap.crt -days 825 -sha256
```

2. **Configurar slapd** (ejemplo muy usado en `/etc/ldap/ldap.conf` o `slapd.conf`, o vía `cn=config`) usando directivas:

```text
TLSCACertificateFile   /etc/ssl/certs/ca.crt
TLSCertificateFile     /etc/ssl/certs/ldap.crt
TLSCertificateKeyFile  /etc/ssl/private/ldap.key
```

(si usas `cn=config`, las equivalentes son `olcTLSCACertificateFile`, `olcTLSCertificateFile`, `olcTLSCertificateKeyFile`).

3. **Reiniciar `slapd`** y comprobar logs (`/var/log/syslog`, `journalctl -u slapd`).
4. **Probar** (ver sección de pruebas).

### 6) Ejemplos de comandos para **probar** LDAPS (Windows y Linux)

#### A) **En Windows** — usar LDP.exe

- Ejecuta `ldp.exe` → Connection → Connect → introduce `dc01.ejemplo.com` y puerto `636` → check **SSL** → Connect. Si conecta, verás la bind exitoso. (Herramienta incluida en RSAT/AD DS tools).

#### B) **Con OpenSSL** (comprueba la negociación TLS y presenta el certificado):

```bash
openssl s_client -connect dc01.ejemplo.com:636 -showcerts
# observas la cadena, fechas, y el CN/SAN presentados por el servidor
```

(útil para comprobar que el servidor presenta el certificado correcto).

#### C) **Con ldapsearch (OpenLDAP client)**:

```bash
# simple consulta base para ver que LDAPS responde
ldapsearch -H ldaps://dc01.ejemplo.com -x -b "" -s base '(objectclass=*)' namingContexts
```

(Si necesitas forzar validación del certificado, añade `-CAfile /ruta/ca.crt` o configura `/etc/ldap/ldap.conf` con `TLS_CACERT`).

### 7) Problemas frecuentes y cómo resolverlos (chequeo rápido)

- **No conecta en 636** → revisar firewall/NSG (puerto TCP 636) y reglas a nivel de red/VM.
- **Certificado no válido / confianza** → importar la CA en el almacén de confianza del cliente (o usar una CA pública).
- **Nombre no coincide** → el SAN/CN del certificado no contiene el nombre con el que el cliente se conecta (ej.: cliente usa `ldap.example.com` pero el cert tiene sólo `dc01.example.com`). → Reemitir cert con SAN apropiado.
- **Active Directory no "ve" el certificado** → asegúrate que el certificado está en `Local Computer\Personal` con clave privada y EKU serverAuth; revisa el Event Viewer (Directory Service, System) para mensajes LDAPS. Hay KBs de Microsoft que describen problemas de conexión LDAPS.
- **Problemas con NAT / balanceadores** → si el TLS se termina en un LB, el certificado debe coincidir con el nombre público; si el LB hace passthrough, cada DC/servidor necesita su certificado.
- **Fragmentación/MTU** (más en escenarios con TCP + TLS vía redes complicadas) → ajustar MTU / MSS si notas problemas de transferencia.

### 8) Buenas prácticas y recomendaciones

- Usa **certificados firmados por CA** (interna o pública). Evita confiar en _self-signed_ en producción sin distribuir la CA a todos los clientes.
- **Incluir SANs** necesarios (FQDNs o aliases).
- **Automatizar** la emisión/rotación (AD CS autoenrollment para DCs si es posible).
- **Monitorear** expiraciones y logs (Event Viewer, slapd logs).
- Mantener **cifras y longitudes modernas** (no usar RC4/MD5/sha1 hoy).

### 9) Resumen rápido de comandos y snippets útiles (cheat-sheet)

**OpenSSL — comprobar certificado LDAPS:**

```bash
openssl s_client -connect dc01.ejemplo.com:636 -showcerts
```

**ldapsearch sobre LDAPS:**

```bash
ldapsearch -H ldaps://ldap.ejemplo.com -x -b "dc=ejemplo,dc=com" "(cn=usuario)"
```

**Certreq (Windows) — flujo básico:**

```text
# crear request from .inf
certreq -new request.inf request.req

# submit request (enterprise CA)
certreq -submit request.req certnew.cer

# accept/install returned cert
certreq -accept certnew.cer
```

**slapd (OpenLDAP) — líneas TLS en slapd.conf:**

```text
TLSCACertificateFile   /etc/ssl/certs/ca.crt
TLSCertificateFile     /etc/ssl/certs/ldap.crt
TLSCertificateKeyFile  /etc/ssl/private/ldap.key
```

---

[🔼](#índice)

---

## **367. LDAP vs LDAPS: ¿cuál es la diferencia?**

- **LDAP** (Lightweight Directory Access Protocol) es el protocolo para consultar y modificar directorios (usuarios, grupos, dispositivos). Por defecto **no cifra** la conexión (puerto TCP **389**).
- **LDAPS** es LDAP sobre TLS/SSL (conexión “segura” implícita). Usa TLS desde el inicio de la conexión (puerto TCP **636**).
- Alternativa importante: **STARTTLS** (upgrade a TLS sobre el mismo puerto 389) — es _explicit TLS_ vs LDAPS _implícito TLS_.

### 1. ¿Cuál es la diferencia técnica?

- **Capa de transporte**

  - LDAP simple: tráfico en texto plano (credenciales y datos viajan sin cifrar si se usa simple bind).
  - LDAPS: la capa TLS se establece **antes** de cualquier intercambio LDAP → todo el tráfico (bind, consultas, respuestas) está cifrado.

- **Modo de negociación**

  - **LDAPS (implicit TLS)**: cliente abre conexión TLS en el puerto 636 y dentro de ese canal habla LDAP.
  - **STARTTLS (explicit TLS)**: cliente conecta al puerto 389 y envía un comando LDAP para “upgradear” la sesión a TLS; si el servidor acepta, se negocia TLS y después continúa la sesión LDAP cifrada.

- **Puertos**

  - LDAP (sin TLS): **389**
  - LDAPS (implicit TLS): **636**
  - STARTTLS: negocia sobre **389** y lo convierte a TLS.

### 2. Seguridad: ¿cuál es “mejor”?

- **Ambos (LDAPS y STARTTLS) pueden ser igual de seguros** siempre que: se exijan TLS, se usen versiones modernas (TLS 1.2/1.3), y los certificados sean válidos (SAN, cadena de confianza).
- STARTTLS puede ser vulnerable a _downgrade/stripping_ si el cliente no fuerza la encriptación; por eso hay que **requerir** TLS (no usar “opportunistic”).
- LDAPS evita el paso de upgrade (menos superficie para stripping) pero usa puerto distinto, lo que puede complicar balanceadores o NAT.

### 3. Ejemplos prácticos (comandos)

#### Comprobar LDAPS (con OpenSSL)

```bash
# Ver certificado presentado por servidor en ldaps (puerto 636)
openssl s_client -connect ldap.ejemplo.com:636 -showcerts
```

#### Comprobar STARTTLS (upgrade en puerto 389)

```bash
# Inicia conexión TCP a 389 y pide starttls para LDAP
openssl s_client -connect ldap.ejemplo.com:389 -starttls ldap -showcerts
```

#### Búsqueda LDAP insegura (NO recomendada)

```bash
ldapsearch -x -h ldap.ejemplo.com -p 389 -D "cn=admin,dc=ejemplo,dc=com" -W -b "dc=ejemplo,dc=com" "(objectClass=*)"
```

#### Búsqueda LDAP con STARTTLS (recomendado si el servidor soporta)

```bash
ldapsearch -H ldap://ldap.ejemplo.com -x -ZZ \
  -D "cn=admin,dc=ejemplo,dc=com" -W -b "dc=ejemplo,dc=com" "(objectClass=*)"
# -ZZ fuerza STARTTLS
```

#### Búsqueda con LDAPS (puerto 636)

```bash
ldapsearch -H ldaps://ldap.ejemplo.com -x \
  -D "cn=admin,dc=ejemplo,dc=com" -W -b "dc=ejemplo,dc=com" "(objectClass=*)"
```

#### Probar desde Windows (herramienta gráfica)

- `ldp.exe` → **Connection → Connect** → servidor: `dc01.ejemplo.com`, puerto `636`, marcar **SSL** → Connect. Si OK aparece la conexión cifrada.

### 4. Requisitos de certificado y validación

- El servidor TLS (LDAPS o el que atiende STARTTLS) debe presentar un **certificado válido**:

  - Incluya el **FQDN** en el **CN** o en **SAN** (Subject Alternative Name).
  - Tener EKU adecuado si se requiere (Server Authentication).
  - Cadena completa (certificado intermedio + raíz) o al menos la CA debe ser confiable para el cliente.

- El **cliente** debe confiar en la CA que firmó el certificado (importar CA a store de confianza).
- En AD, los Domain Controllers activan LDAPS automáticamente cuando hay un certificado válido en `Local Computer\Personal`.

### 5. Configuración mínima (ejemplos)

#### OpenLDAP (`slapd`) — TLS snippet (ej. cn=config o slapd.conf)

```text
TLSCACertificateFile   /etc/ssl/certs/ca.crt
TLSCertificateFile     /etc/ssl/certs/ldap.crt
TLSCertificateKeyFile  /etc/ssl/private/ldap.key
```

- Luego reinicia slapd; para STARTTLS se usa la misma configuración (TLS se negociará cuando el cliente pida STARTTLS).

#### Active Directory (Domain Controller)

- Obtener/instalar certificado con SAN (FQDN) en **Local Computer → Personal**. Si usas AD CS puedes habilitar autoenrollment con plantilla _Domain Controller_.
- No hace falta “activar” LDAPS explícitamente: AD comenzará a escuchar en 636 si el certificado cumple requisitos.

### 6. Casos prácticos de uso y compatibilidad

- **Legacy clients / appliances**: algunos sólo soportan LDAPS (636).
- **Modern best practice**: usar STARTTLS (si cliente y servidor lo soportan) o LDAPS con TLS 1.2/1.3; lo importante es exigir TLS y deshabilitar conexiones en texto plano.
- **Balanceadores / TLS termination**: si un LB termina TLS y reenvía LDAP en texto plano a backend, asegúrate de que el reenvío sea interno y seguro; o usa passthrough para que el backend (DC) reciba TLS directo si necesitas autenticación mutua o client certs.

### 7. Problemas comunes y cómo diagnosticarlos

- **“Unable to bind / invalid credentials”** → asegúrate que el bind se hace **dentro** del canal TLS (si usas simple bind) o usa SASL/GSSAPI (Kerberos) que no envía la contraseña en claro.
- **Hostname mismatch** → certificado no tiene el SAN/CN correcto → reemitir cert con nombre correcto.
- **Certificado no confiable** → importar CA en store de confianza del cliente.
- **Puerto 636/389 bloqueado** → abrir firewall / NSG.
- **Servidor no presenta certificado** → clave privada no está accesible o certificado no está instalado en almacén correcto.
- **Ver con OpenSSL**: `openssl s_client -connect host:636` o `-starttls ldap` te muestra la cadena y errores TLS.
- **Logs**:

  - Windows: Event Viewer (Directory Service / System) para errores LDAPS.
  - Linux/OpenLDAP: `journalctl -u slapd` o `/var/log/syslog`.

### 8. Recomendaciones y buenas prácticas

1. **Nunca** usar simple bind (usuario+password) sobre LDAP sin TLS.
2. **Forzar TLS**: si usas STARTTLS, exigir que el cliente lo use (no opportunistic).
3. **Usar TLS 1.2/1.3** y cifrados modernos (AEAD). Deshabilitar SSLv3/TLS1.0/1.1.
4. **Certificados con SAN** y cadena completa; automatizar rotación/renovación.
5. Considerar **SASL/GSSAPI (Kerberos)** para autenticación en entornos AD, ya que evita enviar contraseñas incluso dentro de TLS.
6. Si empleas balanceadores o NAT, decidir entre TLS passthrough o terminación TLS en el LB (con las implicancias de seguridad correspondientes).

### 9. Ejemplo práctico — escenario típico

- **Objetivo**: permitir que clientes remotos se autentiquen contra directorio corporativo sin exponer contraseñas en texto plano.
- **Solución**: habilitar STARTTLS en OpenLDAP/LDAPS en DCs; obligar clientes a usar STARTTLS/LDAPS; publicar CA interna en dispositivos gestionados; usar Kerberos/SASL donde sea posible para evitar envío de contraseñas.

### 10. Conclusión breve

- La diferencia principal es **cómo y cuándo** se negocia TLS: **LDAPS = TLS implícito en puerto 636**; **STARTTLS = upgrade explícito en puerto 389**.
- En seguridad real, **lo crucial** no es si usas LDAPS o STARTTLS, sino **garantizar TLS moderno** (versiones y ciphers), certificados correctos, y **no permitir binds en texto plano**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**            | **Siguiente 6**      |
| ------------------ | ---------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./12_4_DNSSEC.md) | [⏩](./12_6_SRTP.md) |
