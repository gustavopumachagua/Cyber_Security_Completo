| **Inicio**         | **atrás 5**               |
| ------------------ | ------------------------- |
| [🏠](../README.md) | [⏪](./5_5_HTTP_HTTPS.md) |

---

## **Índice**

| Temario                                                                           |
| --------------------------------------------------------------------------------- |
| [143. ¿Qué es SSL? Definición de SSL](#143-qué-es-ssl-definición-de-ssl)          |
| [144. Conceptos básicos de TLS](#144-conceptos-básicos-de-tls)                    |
| [145. TLS vs SSL: ¿Cuál es la diferencia?](#145-tls-vs-ssl-cuál-es-la-diferencia) |

# **SSL TLS**

## **143. ¿Qué es SSL? Definición de SSL**

![SSL TLS](/img/5_Network_Topologies/SSL_TLS.png "SSL TLS")

### 🔹 1. ¿Qué es **SSL**?

- **SSL (Secure Sockets Layer)** es un protocolo criptográfico creado en los 90 por Netscape para **cifrar la comunicación en Internet**.
- Permite que los datos entre un cliente (ej. tu navegador) y un servidor (ej. un sitio web) viajen de forma **confidencial, íntegra y segura**.

📌 Ejemplo:

- Conexión HTTP normal → datos viajan en texto plano.
- Conexión HTTPS con SSL/TLS → datos viajan cifrados.

### 🔹 2. ¿Cómo funciona el SSL/TLS?

El protocolo crea un **canal cifrado** sobre TCP, normalmente en el puerto **443** (para HTTPS).

#### 🔑 Etapas:

1. **Handshake (apretón de manos):**

   - El cliente se conecta y pide seguridad.
   - El servidor envía su **certificado SSL/TLS**.
   - Se valida que el certificado sea confiable.
   - Cliente y servidor acuerdan un algoritmo de cifrado.

2. **Intercambio de claves:**

   - Se genera una **clave de sesión única** para cifrar los datos.

3. **Comunicación cifrada:**

   - Todo el tráfico posterior (contraseñas, formularios, cookies) viaja encriptado.

📌 Ejemplo:

Si mandas una contraseña por HTTP:

```
usuario=gustavo&password=1234
```

➡️ Cualquiera que intercepte el tráfico puede verla.

Con HTTPS (SSL/TLS):

```
6#4sd9fjsd8f0asd8... (texto cifrado)
```

➡️ Inútil para un atacante.

---

### 🔹 3. ¿Por qué es importante el SSL/TLS?

- **Seguridad:** protege contra espionaje e intercepción.
- **Integridad:** evita que los datos sean alterados en tránsito.
- **Autenticidad:** certifica que el servidor es legítimo.
- **Confianza:** los navegadores muestran el candado 🔒 al usar HTTPS.
- **SEO:** Google favorece sitios con HTTPS.

📌 Ejemplo real:

- En una tienda online, si no usas SSL/TLS, tus clientes exponen sus tarjetas de crédito.

### 🔹 4. ¿SSL y TLS son lo mismo?

- **No exactamente.**

  - **SSL** fue el protocolo original, pero ya está obsoleto.
  - **TLS (Transport Layer Security)** es la **versión más segura y moderna**, sucesora de SSL.

👉 Hoy, cuando decimos **“SSL”** (ej. “certificado SSL”), en realidad hablamos de **TLS**.

El término “SSL” se sigue usando por costumbre comercial.

### 🔹 5. ¿SSL aún está actualizado?

- ❌ **No.**

  - SSL 2.0 y SSL 3.0 están completamente obsoletos e inseguros.
  - TLS 1.0 y TLS 1.1 también están desaconsejados.
  - 🔑 Actualmente se usan **TLS 1.2 y TLS 1.3** (este último es el más rápido y seguro).

### 🔹 6. ¿Qué es un certificado SSL?

Un **certificado SSL/TLS** es un archivo digital emitido por una **Autoridad de Certificación (CA)** que:

- Verifica la identidad del servidor o empresa.
- Permite el cifrado de la comunicación con el cliente.

📌 Ejemplo:

Cuando entras a `https://banco.com`, tu navegador comprueba que el certificado:

- Fue emitido por una CA confiable.
- Pertenece al dominio correcto.
- No está vencido ni revocado.

### 🔹 7. ¿Cuáles son los tipos de certificado SSL?

Los certificados varían en el **nivel de validación** y **cantidad de dominios** que protegen:

#### 🔑 Según validación:

1. **DV (Domain Validation):**

   - Solo valida que el dominio sea tuyo.
   - Rápido y barato (incluso gratis con _Let’s Encrypt_).
   - Ejemplo: blogs personales.

2. **OV (Organization Validation):**

   - Valida dominio + empresa.
   - Muestra más confianza.
   - Ejemplo: negocios medianos.

3. **EV (Extended Validation):**

   - Validación completa de empresa.
   - Antes mostraba el nombre de la empresa en el navegador (ahora la mayoría solo muestra el candado).
   - Ejemplo: bancos, e-commerce grandes.

#### 🔑 Según cobertura:

- **Single Domain:** protege solo un dominio (ej. `midominio.com`).
- **Wildcard:** protege dominio y subdominios (ej. `*.midominio.com`).
- **Multi-domain (SAN/UCC):** protege varios dominios distintos en un solo certificado.

### 🔹 8. ¿Cómo puede una empresa obtener un certificado SSL?

1. Elegir una **Autoridad de Certificación (CA)**:

   - Gratuitas: _Let’s Encrypt_.
   - Comerciales: DigiCert, GlobalSign, Sectigo, GoDaddy, etc.

2. Generar una **CSR (Certificate Signing Request)** en el servidor.

3. Validar el dominio/empresa según el tipo de certificado.

4. Instalar el certificado en el servidor web (Apache, Nginx, IIS, etc.).

5. Configurar redirección de HTTP → HTTPS.

📌 Ejemplo en Ubuntu con Apache:

```bash
sudo a2enmod ssl
sudo a2ensite default-ssl
sudo systemctl reload apache2
```

### 🔹 9. Más acerca de SSL/TLS

- SSL/TLS no solo se usa en **HTTPS**. También protege otros protocolos:

  - **FTPS** → FTP seguro con TLS.
  - **SMTPS** → correos electrónicos seguros.
  - **IMAPS / POP3S** → acceso a correo seguro.

- Hoy en día, **TLS 1.3** es el estándar recomendado por ser más seguro y rápido.

✅ **Resultado final:**

- **SSL** fue el primer protocolo de seguridad en Internet, pero hoy está reemplazado por **TLS**.
- Un **certificado SSL/TLS** permite cifrar datos, validar la identidad del servidor y generar confianza.
- Existen varios tipos (DV, OV, EV; Single, Wildcard, Multi-domain).
- Toda empresa puede obtener uno fácilmente mediante una CA.
- Usar HTTPS (con TLS) es obligatorio hoy en día para proteger usuarios y cumplir estándares.

---

[🔼](#índice)

---

## **144. Conceptos básicos de TLS**

### 🔹 1. ¿Qué es **TLS**?

- **TLS (Transport Layer Security)** es un protocolo criptográfico que protege la comunicación en Internet.
- Es el **sucesor de SSL (Secure Sockets Layer)**.
- Se usa principalmente en **HTTPS**, pero también en **correo electrónico, VPNs, FTP seguro, etc.**

📌 Ejemplo:

- Cuando visitas `http://midominio.com`, los datos viajan en **texto plano**.
- Cuando visitas `https://midominio.com`, viajan **cifrados con TLS**.

### 🔹 2. ¿Por qué debería importarme TLS?

TLS es esencial porque:

✅ **Confidencialidad:** evita que alguien “espíe” tus datos (man-in-the-middle).

✅ **Integridad:** asegura que los datos no sean modificados en tránsito.

✅ **Autenticidad:** garantiza que hablas con el servidor correcto.

✅ **Confianza:** los navegadores muestran el candado 🔒 si la conexión es segura.

📌 Ejemplo práctico:

- Si entras a tu banco sin TLS (solo HTTP), un atacante en la misma red Wi-Fi podría ver tu usuario y contraseña.
- Con TLS, esos datos están cifrados, y aunque intercepte la conexión, verá solo texto ilegible.

### 🔹 3. ¿Cómo funciona TLS?

El funcionamiento de TLS se divide en **dos fases principales**:

#### 1️⃣ **Handshake (apretón de manos)**

- Cliente (ej. navegador) y servidor (ej. web) se saludan.
- El servidor envía su **certificado digital**.
- El cliente valida que el certificado sea válido y confiable.
- Se acuerdan algoritmos de cifrado.

#### 2️⃣ **Cifrado de la comunicación**

- Se genera una **clave de sesión única** (secreta).
- Todos los datos se cifran con esa clave antes de enviarse.

📌 Ejemplo de flujo TLS en HTTPS:

1. Cliente: “Quiero conectarme a `https://banco.com` de forma segura”.
2. Servidor: “Aquí está mi certificado SSL/TLS, emitido por una CA confiable”.
3. Cliente: “Verificado. Vamos a usar AES-256 como cifrado”.
4. Ambos generan claves de sesión.
5. Todo lo que envías (contraseña, tarjeta de crédito) se cifra y viaja seguro.

### 🔹 4. ¿Qué es una **CA (Certificate Authority)**?

- Una **CA (Autoridad de Certificación)** es una entidad de confianza que emite **certificados digitales**.
- Estos certificados verifican que un servidor realmente es quien dice ser.

📌 Ejemplo:

Cuando entras a `https://google.com`, tu navegador no le cree directamente al servidor, sino que confía en que **DigiCert** (una CA reconocida) certifica que ese dominio pertenece a Google.

#### Principales CAs:

- DigiCert
- GlobalSign
- Sectigo
- Let’s Encrypt (gratuita)

✅ **Resultado final:**

- **TLS** es el protocolo que garantiza seguridad en Internet.
- Te importa porque protege tus **contraseñas, correos, pagos online y datos personales**.
- Funciona mediante un **handshake + cifrado** para asegurar comunicaciones.
- Una **CA** respalda la autenticidad del servidor con un **certificado digital**.

---

[🔼](#índice)

---

## **145. TLS vs SSL: ¿Cuál es la diferencia?**

### 1. Resumen corto (TL;DR)

- **SSL** fue el protocolo original de cifrado (creado por Netscape).
- **TLS** es su sucesor modernizado y seguro.
- Hoy **SSL está obsoleto**; se usan **TLS 1.2** y **TLS 1.3**.
- En el día a día la gente sigue diciendo “certificado SSL”, pero técnicamente hablamos de **certificados TLS**.

### 2. Historia y estado actual

- SSL 2.0 / SSL 3.0 → antiguas versiones que quedaron vulnerables (POODLE, etc.).
- TLS 1.0 / 1.1 → fueron mejoras sobre SSL, pero hoy también están desaconsejadas.
- **TLS 1.2** (muy usado, maduro) y **TLS 1.3** (más moderno, más seguro y más rápido) son los estándares actuales.
- Las principales organizaciones y navegadores **han retirado el soporte** para SSL y TLS < 1.2.

### 3. Diferencias técnicas clave

#### 3.1 Diseño y seguridad

- **SSL** = progenitor. **TLS** = evolución con correcciones, mejoras criptográficas y mitigaciones a ataques descubiertos contra SSL.
- **TLS 1.3** rediseñó el handshake para:

  - eliminar mecanismos inseguros (p. ej. intercambio RSA estático),
  - exigir cifrados con _forward secrecy_ (ECDHE),
  - usar solo ciphers AEAD (ej. AES-GCM, ChaCha20-Poly1305),
  - reducir el número de _round-trips_ (mejor latencia) y habilitar 0-RTT opcional.

#### 3.2 Cifrado y suites

- En **SSL/TLS anteriores** se negociaban suites que incluían key-exchange, autenticación, cifrado y MAC (ej. `ECDHE-RSA-AES256-GCM-SHA384`).
- **TLS 1.3** simplifica: las suites de TLS1.3 solo indican el algoritmo AEAD (p. ej. `TLS_AES_128_GCM_SHA256`) y separa negociación de key-exchange y autenticación (esto reduce combinaciones inseguras).

#### 3.3 Handshake

- **SSL / TLS ≤1.2**: handshake más largo, posibles modos inseguros (RSA key exchange sin PFS).
- **TLS 1.3**: handshake más corto, PFS por defecto (ECDHE), menos superficie de ataque y mejora del rendimiento (menos RTTs).

### 4. Vulnerabilidades históricas (por qué SSL ya no sirve)

- **POODLE** (SSLv3): padding oracle que permitió romper confidencialidad.
- **BEAST/BULL** (TLS/SSL antiguas): ataques contra cifrados en bloque con modos CBC.
- **Heartbleed** fue una vulnerabilidad en OpenSSL (implementación) que afectó sistemas TLS, pero no del protocolo en sí.
  → Por estas y otras razones, **no se debe usar SSL ni TLS < 1.2**.

### 5. Impacto práctico (qué significa para admins / desarrolladores)

- No habilites SSLv2/SSLv3 ni TLS 1.0/1.1 en tus servidores.
- Prioriza **TLS 1.3** y asegura compatibilidad con **TLS 1.2** para clientes más viejos.
- Usa suites que ofrezcan **Forward Secrecy** (ECDHE) y **AEAD** (AES-GCM, ChaCha20-Poly1305).
- Habilita HSTS, OCSP stapling, y configura correctamente certificados y cadenas intermedias.

### 6. Ejemplos prácticos — cómo **comprobar** y **configurar**

#### 6.1 Comprobar qué versiones soporta un servidor (OpenSSL)

```bash
# Probar TLS 1.3
openssl s_client -connect ejemplo.com:443 -tls1_3

# Probar TLS 1.2
openssl s_client -connect ejemplo.com:443 -tls1_2

# Probar TLS 1.1 (debería fallar en servidores modernos)
openssl s_client -connect ejemplo.com:443 -tls1_1
```

Salida útil: fíjate en `Protocol  : TLSv1.3` o `TLSv1.2` y en la línea `Cipher    :`.

#### 6.2 Ver la versión negociada con curl

```bash
curl -v https://ejemplo.com 2>&1 | grep -i "SSL connection" -A 1
# o simplemente
curl -sI -o /dev/null -w "%{ssl_version}\n" https://ejemplo.com
```

#### 6.3 Configuración recomendada en **Nginx** (ejemplo)

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers off; # TLS1.3 ignora esta lista
ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:
             ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:
             ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384';

ssl_session_cache shared:TLS:10m;
ssl_session_timeout 10m;
ssl_stapling on;
ssl_stapling_verify on;

add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
```

> Nota: las listas de `ssl_ciphers` varían según OpenSSL/LibreSSL y requisitos; ajusta para tu plataforma y compatibilidad.

#### 6.4 Apache (ejemplo)

```apache
SSLProtocol all -SSLv2 -SSLv3 -TLSv1 -TLSv1.1
SSLCipherSuite ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:...
SSLHonorCipherOrder on
```

### 7. Recomendaciones / checklist (qué hacer ya)

1. **Deshabilitar** SSLv2/SSLv3/TLS 1.0/1.1 en servidores.
2. **Habilitar TLS 1.3** si tu stack (OpenSSL, server) lo soporta; mantener TLS 1.2 para compatibilidad.
3. Usar **ECDHE** (ephemeral) para key exchange → PFS.
4. Prefiere **AEAD ciphers**: AES-GCM o ChaCha20-Poly1305.
5. Implementar **HSTS**, **OCSP stapling**, **Forward secrecy**, y **strong certificate chain**.
6. Revisar con tools públicas (p. ej. SSL Labs) y `openssl s_client` periódicamente.
7. Mantener OpenSSL / LibreSSL / bibliotecas actualizadas (vulnerabilidades se corrigen en implementaciones).

### 8. Diferencias resumidas en una tabla

|                Aspecto | SSL                                | TLS                                  |
| ---------------------: | ---------------------------------- | ------------------------------------ |
|          Estado actual | Obsoleto/inseguro                  | Estándar (1.2 y 1.3)                 |
| Soporte en navegadores | Retirado                           | Sí (1.2/1.3)                         |
|              Handshake | Más pasos, permite modos inseguros | TLS1.3 simplifica y mejora seguridad |
|    Cifrado recomendado | No aplicable (obsoleto)            | AEAD (AES-GCM, ChaCha20), ECDHE      |
|        Forward Secrecy | No obligatorio                     | PFS recomendado/forzado en TLS1.3    |

### 9. Ejemplos concretos de diferencias en la práctica

- **Antes (SSL / TLS ≤1.2)**: servidor podía ofrecer `RSA` como key-exchange (RSA static), lo que permitía descifrar sesiones si la clave privada era comprometida.
- **Ahora (TLS1.3)**: RSA key-exchange está eliminado; se requiere ECDHE (ephemeral), por tanto aún si la clave privada se filtra, las sesiones pasadas permanecen seguras (PFS).

### 10. Conclusión / Resultado final

- **SSL ≠ TLS**, pero en la conversación diaria “SSL” se usa para referirse a certificados y a la capa de cifrado en general.
- Para **seguridad real** debes usar **TLS 1.2** mínimo y **TLS 1.3** cuando sea posible.
- Actualiza bibliotecas, revisa configuraciones y usa herramientas de comprobación para garantizar que no estás exponiendo protocolos/ciphers inseguros.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**               |
| ------------------ | ------------------------- |
| [🏠](../README.md) | [⏪](./5_5_HTTP_HTTPS.md) |
