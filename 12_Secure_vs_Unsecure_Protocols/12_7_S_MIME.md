| **Inicio**         | **atrás 6**          | **Siguiente 8**           |
| ------------------ | -------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./12_6_SRTP.md) | [⏩](./12_8_Antivirus.md) |

---

## **Índice**

| Temario                                                                                                                            |
| ---------------------------------------------------------------------------------------------------------------------------------- |
| [369. S MIME para firma y cifrado de mensajes en Exchange Online](#369-s-mime-para-firma-y-cifrado-de-mensajes-en-exchange-online) |
| [370. S MIME Protocolo MIME seguro Funciones, Servicios](#370-s-mime--protocolo-mime-seguro-funciones-servicios)                   |

# **S MIME**

## **369. S MIME para firma y cifrado de mensajes en Exchange Online**

![S MIME](/img/12_Secure_vs_Unsecure_Protocols/S_MIME.webp "S MIME")

### 📌 1. ¿Qué es S/MIME?

- **S/MIME (Secure/Multipurpose Internet Mail Extensions)** es un estándar que permite:

  - **Firmar digitalmente** correos electrónicos.
  - **Cifrarlos** para que solo el destinatario pueda leerlos.

- Se basa en el uso de **certificados digitales X.509** emitidos por una Autoridad de Certificación (CA).
- Está soportado en **Exchange Online (Microsoft 365)** y funciona en clientes como **Outlook para Windows, Mac y Outlook Web (OWA)**.

### ✍️ 2. Firmas digitales S/MIME

- Una **firma digital** asegura que:

  1. El mensaje **no ha sido alterado** (integridad).
  2. El remitente es realmente quien dice ser (autenticidad).

- Funciona aplicando una operación criptográfica con la **clave privada** del remitente.
- El destinatario valida la firma usando la **clave pública** (del certificado en el correo).

👉 Ejemplo práctico:

- Gustavo firma un correo en Outlook con S/MIME.
- El destinatario recibe el mensaje y ve un icono de **firma válida**.
- Si alguien modifica el mensaje en tránsito, la firma se rompe y aparece como **inválida**.

### 🔐 3. Cifrado S/MIME

- Permite que solo el destinatario pueda leer el correo.
- El remitente cifra el mensaje usando la **clave pública** del destinatario (incluida en su certificado).
- El destinatario descifra con su **clave privada**.

👉 Ejemplo práctico:

- Gustavo envía un correo cifrado con S/MIME a su colega.
- El correo viaja por Internet **cifrado**: aunque alguien lo intercepte, no puede leerlo.
- Solo el destinatario, con su certificado y clave privada, puede abrirlo en Outlook.

### 🛠️ 4. Configuración de S/MIME en Exchange Online

1. Obtener un **certificado digital** para el usuario (de una CA interna o externa).
2. Instalar el certificado en el cliente de correo (Outlook o navegador si se usa OWA).
3. Configurar Exchange Online para permitir el uso de **S/MIME add-in**.
4. Los usuarios ya pueden firmar y/o cifrar mensajes desde la interfaz de Outlook.

### 🔗 5. Tecnologías relacionadas con cifrado de mensajes

Microsoft 365 ofrece **otras formas de proteger correos** además de S/MIME:

#### 🔹 5.1. Office 365 Message Encryption (OME)

- Basado en **Azure Rights Management (Azure RMS)**.
- Permite cifrar correos incluso si el destinatario no usa S/MIME.
- Ejemplo: Gustavo envía un correo cifrado a un Gmail. El destinatario recibe un link seguro para abrirlo en un portal de Microsoft.

#### 🔹 5.2. IRM (Information Rights Management)

- Controla qué puede hacer el destinatario con el mensaje (restringir copiar, reenviar, imprimir).
- Ejemplo: Gustavo envía un informe confidencial y activa “No reenviar”. Aunque el receptor copie el contenido, no puede reenviarlo.

#### 🔹 5.3. TLS (Transport Layer Security)

- Cifra el canal de transmisión entre servidores de correo.
- No cifra el correo en sí, solo el transporte.

👉 Diferencia clave:

- **S/MIME y OME** → cifran el contenido del mensaje.
- **TLS** → cifra el canal por donde viaja el mensaje.

### 📊 6. Comparación rápida

| Tecnología | ¿Qué protege?                            | Requisitos                                 |
| ---------- | ---------------------------------------- | ------------------------------------------ |
| **S/MIME** | Contenido del mensaje (firma + cifrado)  | Certificados X.509 en usuarios             |
| **OME**    | Contenido, incluso a externos sin S/MIME | Licencia Microsoft 365 con Azure RMS       |
| **IRM**    | Derechos de uso del mensaje              | Azure AD RMS                               |
| **TLS**    | Transporte (servidor ↔ servidor)         | Configuración de conectores y certificados |

✅ **En resumen**:

- **S/MIME** es ideal si tu organización necesita **firmas digitales y cifrado punto a punto con certificados**.
- **OME** y **IRM** son más flexibles para escenarios modernos (usuarios externos, control de derechos).
- **TLS** asegura que los mensajes no viajen en texto claro entre servidores, pero no protege el contenido final.

---

[🔼](#índice)

---

## **370. S MIME Protocolo MIME seguro Funciones, Servicios**

### ¿Qué es S/MIME (en una frase)

S/MIME es un estándar que permite **firmar digitalmente** y **cifrar** mensajes MIME (correo electrónico) usando **certificados X.509**. Su objetivo: garantizar **autenticidad**, **integridad**, **confidencialidad** y **no repudio** del correo electrónico.

### Fundamentos técnicos (resumen rápido)

- Basado en **PKI** (infraestructura de claves públicas): certificados X.509 emitidos por CAs.
- Usa la **Cryptographic Message Syntax (CMS / antes PKCS#7)** para empaquetar firmas y/o datos cifrados.
- Integra con el **formato MIME**, por eso se llama S/MIME (secure MIME).
- Soporta algoritmos modernos (RSA, ECC; SHA-256/384; AES, ChaCha20-Poly1305 según implementaciones).
- Tipos MIME frecuentes:

  - `multipart/signed` (protocol = "application/pkcs7-signature") → para mensajes firmados.
  - `application/pkcs7-mime; smime-type=enveloped-data` (comúnmente `.p7m`) → para mensajes cifrados.

### Funciones clave de S/MIME

1. **Firma digital**

   - Asegura que el remitente es quien dice ser y que el mensaje no fue modificado.
   - Firma con la **clave privada** del remitente; el receptor verifica con la **clave pública** (certificado).

2. **Cifrado de mensaje**

   - Confidencialidad: el remitente cifra el contenido usando la **clave pública del destinatario**; sólo el destinatario con su clave privada lo descifra.

3. **Integración con MIME**

   - Permite firmar y cifrar tanto el cuerpo del mensaje como los adjuntos (sin cambiar demasiado la estructura de correo).

4. **No-repudio (en parte)**

   - La firma digital ofrece evidencia de autoría, útil legalmente si la PKI y la custodia de claves están gestionadas.

5. **Distribución de certificados / descubrimiento**

   - Los clientes pueden obtener certificados desde directorios (LDAP/Active Directory) o pueden aprender el certificado del remitente cuando este envía un mensaje firmado (el certificado suele incluirse en la firma).

### Servicios que aporta S/MIME en un entorno empresarial

- **Correo cifrado punto a punto** entre empleados/partners.
- **Firmas corporativas** para comprobación de integridad y auditable.
- **Integración con directorios** (AD) para descubrimiento y despliegue de certificados.
- **Recuperación/escrow de claves** (en empresas: almacén/escrow de claves privadas para recuperación de correos cifrados si un empleado pierde su clave).
- **Políticas y cumplimiento**: pruebas de firma/cifrado para auditoría, cumplimiento legal (ej. requerimientos regulatorios).
- **Gateways de correo** que reescriben o inspeccionan correo: pueden integrarse con PKI empresarial o usar soluciones de descifrado controladas para DLP/antimalware.

### Cómo funciona (flujo básico — firmar / cifrar / verificar / descifrar)

1. **Firma (emisor)**

   - El cliente calcula un hash (ej. SHA-256) del cuerpo+adjuntos → cifra el hash con la clave privada del remitente → genera un bloque CMS/PKCS#7 con la firma (RRSIG equivalente).
   - El mensaje se envía con un `multipart/signed` donde una de las partes es `application/pkcs7-signature` que contiene la firma (y normalmente el certificado del firmante).

2. **Verificación (receptor)**

   - El cliente extrae la firma, obtiene la clave pública (del certificado del remitente o de un directorio), valida la cadena de certificación y verifica el hash.
   - Si coincide y la cadena es de confianza → firma válida.

3. **Cifrado (emisor)**

   - El emisor obtiene el certificado público del destinatario.
   - Genera una clave simétrica de sesión, cifra el contenido con esa clave (ej. AES), cifra la clave de sesión con la clave pública del destinatario y empaqueta todo en `application/pkcs7-mime` (smime-type=enveloped-data).

4. **Descifrado (receptor)**

   - El receptor usa su clave privada para descifrar la clave de sesión y luego descifra el contenido simétrico para acceder al mensaje.

5. **Firmar + cifrar (best practice)**

   - Generalmente: **firmar primero, luego cifrar**. Así el receptor puede verificar la firma sobre el contenido original (firmado) y además permanece confidencial.

### Ejemplos prácticos con OpenSSL (comandos reales)

> Nota: estos ejemplos sirven para pruebas y para entender el flujo. En producción se usan CAs y flujos corporativos.

#### 1) Crear una CA local (prueba)

```bash
# generar clave CA
openssl genpkey -algorithm RSA -out ca.key -pkeyopt rsa_keygen_bits:4096

# crear cert autofirmado CA
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 \
  -out ca.crt -subj "/CN=Mi-CA/O=MiOrg/C=PE"
```

#### 2) Crear certificado de usuario (firma + email) y firmarlo por la CA

```bash
# clave del usuario
openssl genpkey -algorithm RSA -out user.key -pkeyopt rsa_keygen_bits:2048

# CSR (incluye email en subject; para SAN real usar fichero de config)
openssl req -new -key user.key -out user.csr \
  -subj "/CN=Gustavo/emailAddress=gustavo@example.com"

# firmar CSR con la CA; añadir rfc822Name en SAN usando un archivo extfile.cnf
cat > user-ext.cnf <<EOF
subjectAltName = RFC822:gustavo@example.com
extendedKeyUsage = emailProtection
EOF

openssl x509 -req -in user.csr -CA ca.crt -CAkey ca.key -CAcreateserial \
  -out user.crt -days 365 -sha256 -extfile user-ext.cnf
```

#### 3) Crear PKCS#12 (para importar en Outlook / clientes)

```bash
openssl pkcs12 -export -out user.p12 -inkey user.key -in user.crt -certfile ca.crt \
  -name "Gustavo" -passout pass:MiPassP12
# luego importas user.p12 en el store de Windows / macOS o en Thunderbird
```

#### 4) Firmar un mensaje (archivo `mensaje.txt`) — produce un `.eml` firmado

```bash
openssl smime -sign -in mensaje.txt -text \
  -signer user.crt -inkey user.key -certfile ca.crt \
  -out mail-signed.eml -outform PEM
```

#### 5) Verificar la firma

```bash
openssl smime -verify -in mail-signed.eml -CAfile ca.crt -out mensaje-verificado.txt
# si la verificación falla, el comando dará error
```

#### 6) Encriptar para un destinatario (usar su certificado público)

```bash
openssl smime -encrypt -in mensaje.txt -out mail-encrypted.eml -outform PEM recipient.crt
```

#### 7) Desencriptar (el destinatario)

```bash
openssl smime -decrypt -in mail-encrypted.eml -recip user.crt -inkey user.key -out mensaje-descifrado.txt
```

## 8) Firmar y luego cifrar (workflow seguro)

```bash
# 1) firmar en formato DER/P7M
openssl smime -sign -in mensaje.txt -text -signer user.crt -inkey user.key \
  -out signed.p7m -outform PEM -nodetach

# 2) cifrar el resultado firmado para el destinatario
openssl smime -encrypt -in signed.p7m -out signed-encrypted.eml -outform PEM recipient.crt
```

### Ejemplo de estructura MIME típica

#### Mensaje firmado (multipart/signed)

```
Content-Type: multipart/signed; protocol="application/pkcs7-signature"; micalg=sha-256; boundary="----=_NextPart"

------=_NextPart
Content-Type: text/plain; charset="utf-8"

Hola, este es el mensaje en claro.

------=_NextPart
Content-Type: application/pkcs7-signature; name="smime.p7s"
Content-Transfer-Encoding: base64
Content-Disposition: attachment; filename="smime.p7s"

<BASE64 DEL BLOQUE PKCS#7 CON LA FIRMA>
------=_NextPart--
```

#### Mensaje cifrado (enveloped-data)

```
Content-Type: application/pkcs7-mime; smime-type=enveloped-data; name="smime.p7m"
Content-Transfer-Encoding: base64
Content-Disposition: attachment; filename="smime.p7m"

<BASE64 DEL BLOQUE PKCS#7/CMS CON EL CONTENIDO CIFRADO>
```

### Distribución de certificados y descubrimiento

- **Incluido en mensajes firmados**: cuando firmas, tu certificado (o la cadena) suele incluirse en la firma → los destinatarios futuros pueden usarlo para cifrar respuestas.
- **Directorios LDAP / Active Directory**: en empresas los certs S/MIME se depositan en AD para que clientes busquen el cert por dirección de correo.
- **Public Key Discovery** puede también usarse vía HTTP/LDAP o endpoints de directorio público.

### Revocación y verificación de validez

- S/MIME depende de validar la **cadena de certificación** y comprobar revocación: **CRL** y/o **OCSP**.
- Problemas típicos: CRLs grandes, servidores OCSP no disponibles; por eso la política de revocación y disponibilidad de la CA es importante.

### Integración con clientes y servicios

- Clientes: **Outlook (Windows/Mac/OWA con add-on)**, **Apple Mail**, **Thunderbird**, clientes móviles iOS/Android (soporte varía).
- Servicios: Exchange / Exchange Online (soporta S/MIME; OWA necesita S/MIME control/extension), gateways de correo pueden aplicar políticas S/MIME, y MDM/PKI empresarial facilita despliegue de certs.

### Comparativa breve: S/MIME vs OpenPGP (PGP/MIME)

- **Modelo de confianza**: S/MIME → **PKI centralizada** (CAs); PGP → **web-of-trust** (o claves distribuidas).
- **Integración empresarial**: S/MIME suele ser preferido en empresas por integración con AD/CA.
- **Interoperabilidad**: ambos son ampliamente soportados, pero elección depende política/infraestructura.

### Limitaciones, riesgos y consideraciones prácticas

- **Gestión de claves compleja**: emisión, renovación, revocación, escrow y backup de claves privadas.
- **Pérdida de clave privada = pérdida de acceso** a correos cifrados (por eso el escrow es crítico donde se requiera recuperación).
- **Dificultad para inspección de correo en gateways** (DLP/antivirus) si está cifrado; se requieren soluciones de descifrado controlado.
- **Compatibilidad móvil** y de usuarios externos puede ser un reto (no todos los clientes manejan S/MIME igual).
- **Revocación**: si una CA no está disponible o CRL/OCSP no funciona, la validación puede fallar.

### Buenas prácticas (rápido)

- Usar **certificados gestionados** por una CA corporativa o CA pública confiable.
- Incluir **rfc822Name (email)** en SAN del certificado.
- Algoritmos modernos: **RSA ≥ 2048** o mejor **ECC**, y **SHA-256+**.
- **Escrow / key recovery** si la política lo exige (gestión segura de la clave privada).
- Automatizar la emisión y la rotación de certificados en la empresa (AD CS, ACME para soluciones compatibles, MDM).
- Educar usuarios: firmar siempre antes de cifrar, verificar las firmas recibidas, no aceptar certificados no confiables sin comprobación.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**          | **Siguiente 8**           |
| ------------------ | -------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./12_6_SRTP.md) | [⏩](./12_8_Antivirus.md) |
