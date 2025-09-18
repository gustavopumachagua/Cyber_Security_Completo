| **Inicio**         | **atrás 1**                 | **Siguiente 3**       |
| ------------------ | --------------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./12_1_FTP_vs_SFTP.md) | [⏩](./12_3_IPSec.md) |

---

## **Índice**

| Temario                                                                                    |
| ------------------------------------------------------------------------------------------ |
| [360. ¿Cuál es la diferencia entre SSL y TLS?](#360-cuál-es-la-diferencia-entre-ssl-y-tls) |
| [361. TLS vs SSL: ¿Cuál es la diferencia?](#361-tls-vs-ssl-cuál-es-la-diferencia)          |

# **SSL vs TLS**

## **360. ¿Cuál es la diferencia entre SSL y TLS?**

![SSL vs TLS](/img/12_Secure_vs_Unsecure_Protocols/TLS_vs_SSL.jpg "SSL vs TLS")

### 📌 1. Diferencia entre **SSL** y **TLS**

- **SSL (Secure Sockets Layer)**

  - Fue el primer protocolo de seguridad para cifrar comunicaciones web (años 90).
  - Últimas versiones fueron SSL 2.0 y SSL 3.0 (ambas obsoletas e inseguras).
  - Usaba algoritmos más débiles y tenía vulnerabilidades (ej. POODLE).

- **TLS (Transport Layer Security)**

  - Es la evolución de SSL, más seguro y eficiente.
  - Sus versiones actuales (TLS 1.2 y TLS 1.3) son los estándares modernos.
  - Usa cifrados más fuertes, intercambio de claves más seguro (Diffie-Hellman, ECDH).

👉 **Hoy en día, cuando ves “SSL” casi siempre se refieren a TLS** (por costumbre de marketing).

### 📌 2. Similitudes entre SSL y TLS

- Ambos sirven para **cifrar la comunicación entre cliente y servidor** (ejemplo: navegador ↔ servidor web).
- Usan certificados digitales (emitidos por una CA) para verificar la identidad del servidor.
- Protegen contra ataques de sniffing, MITM (Man-in-the-Middle) y robo de datos.
- Implementan autenticación, confidencialidad e integridad en la transmisión de datos.
- Usan el mismo concepto de **handshake** (negociación entre cliente y servidor para establecer claves y algoritmos).

### 📌 3. Diferencia entre **certificados SSL** y **certificados TLS**

⚠️ **Importante**:

- **No existe una diferencia técnica real en los certificados**.
- Los certificados emitidos por autoridades (CA) funcionan tanto para SSL como para TLS.
- El término “certificado SSL” es simplemente un nombre histórico/comercial.
- Hoy en día, cuando compras un “certificado SSL”, en realidad te sirve para **TLS 1.2/1.3**.

👉 Diferencia real: **el protocolo que se negocia** (SSL o TLS), no el certificado.

### 📌 4. Resumen de diferencias: SSL vs TLS

| Característica | SSL                                              | TLS                                                        |
| -------------- | ------------------------------------------------ | ---------------------------------------------------------- |
| Estado actual  | Obsoleto, inseguro                               | Vigente y recomendado                                      |
| Última versión | SSL 3.0 (1996)                                   | TLS 1.3 (2018)                                             |
| Algoritmos     | Más débiles (MD5, RC4, SHA-1)                    | Algoritmos fuertes (AES, ChaCha20, SHA-256/384)            |
| Handshake      | Más lento, menos seguro                          | Más eficiente, soporta PFS (Perfect Forward Secrecy)       |
| Certificados   | Usaba X.509 (igual que TLS)                      | También usa X.509 (no hay diferencia real)                 |
| Uso actual     | Desaconsejado, bloqueado en navegadores modernos | Estándar global para HTTPS, SFTP, correo seguro, VPN, etc. |

### 📌 5. ¿Cómo puede satisfacer **AWS** sus requisitos de SSL/TLS?

AWS ofrece varios servicios que implementan **TLS moderno (TLS 1.2/1.3)** para cifrar comunicaciones:

1. **AWS Certificate Manager (ACM)**

   - Emite y gestiona certificados SSL/TLS gratuitos.
   - Se integran con servicios como **ELB (Elastic Load Balancer)**, **CloudFront**, **API Gateway**.

2. **Elastic Load Balancing (ELB)**

   - Termina conexiones TLS en el balanceador y reenvía tráfico seguro a las instancias.

3. **Amazon CloudFront (CDN)**

   - Usa certificados TLS para entregar contenido seguro con HTTPS en todo el mundo.

4. **Amazon API Gateway**

   - Protege APIs usando certificados TLS de ACM.

5. **AWS IoT Core**

   - Usa TLS para comunicación segura entre dispositivos IoT y AWS.

6. **Configuración de seguridad TLS**

   - AWS permite restringir las versiones mínimas de TLS (ej. exigir TLS 1.2 o TLS 1.3).
   - Se puede deshabilitar SSL y protocolos inseguros en **ACM, ELB y CloudFront**.

👉 En pocas palabras: **AWS satisface SSL/TLS usando certificados de ACM, controlando versiones mínimas de protocolo y asegurando que solo TLS moderno esté habilitado.**

---

[🔼](#índice)

---

## **361. TLS vs SSL: ¿Cuál es la diferencia?**

### TL;DR

- **SSL** (Secure Sockets Layer) es el protocolo histórico (ahora obsoleto e inseguro).
- **TLS** (Transport Layer Security) es la evolución moderna y segura de SSL.
- Hoy en día **TLS 1.2 y TLS 1.3** son los estándares que debes usar; **deshabilita SSLv2/SSLv3** y, salvo necesidad explícita de compatibilidad, también TLS 1.0/1.1.
- Los **certificados X.509** son los mismos para SSL y TLS; la diferencia es el **protocolo** que negocian cliente y servidor.

### 1) Historia breve y contexto

- SSL fue desarrollado por Netscape en los 90. Las versiones finales fueron **SSL 2.0** y **SSL 3.0**.
- TLS nace como continuación estandarizada de SSL (TLS 1.0 = SSL 3.1 en esencia). Desde entonces TLS ha evolucionado: **TLS 1.0 → 1.1 → 1.2 → 1.3**.
- Muchos ataques y debilidades en SSL (y en versiones antiguas de TLS) hicieron que la industria migrara a TLS moderno.

### 2) Diferencias técnicas clave (resumidas)

1. **Estado / Seguridad**

   - SSL: obsoleto, vulnerable (POODLE, etc.).
   - TLS: diseño más seguro; TLS 1.3 introduce mejoras grandes.

2. **Handshake (negociación)**

   - SSL / TLS ≤ 1.2: handshake más largo, puede implicar `ClientHello → ServerHello → Certificate → ServerKeyExchange → ServerHelloDone → ClientKeyExchange → ChangeCipherSpec → Finished`.
   - **TLS 1.3**: handshake simplificado y más seguro: `ClientHello (key_share) → ServerHello → EncryptedExtensions → Certificate → CertificateVerify → Finished`. Soporta **1-RTT** y **0-RTT** (resumen de sesión; 0-RTT tiene riesgos de replay).

3. **Intercambio de claves**

   - SSL/TLS antiguos permiten RSA key exchange (no PFS).
   - TLS moderno (especialmente 1.3) usa **(EC)DHE** por defecto, proporcionando **Perfect Forward Secrecy (PFS)**.

4. **Cifrado y MAC / AEAD**

   - SSL/TLS antiguas usaban combinaciones MAC + cifrado (posibles problemas: padding oracle, timing).
   - TLS 1.2/1.3 usan preferentemente **AEAD** (por ejemplo AES-GCM, ChaCha20-Poly1305) que integran cifrado + autenticación.

5. **Cifras soportadas**

   - TLS 1.3 tiene un conjunto reducido y seguro de suites (por ejemplo: `TLS_AES_128_GCM_SHA256`, `TLS_AES_256_GCM_SHA384`, `TLS_CHACHA20_POLY1305_SHA256`).
   - TLS 1.2 tiene muchas suites históricas; hay que elegir las seguras (ECDHE + AES-GCM/ChaCha20).

6. **Compatibilidad / Complejidad**

   - TLS 1.3 elimina o cambia muchas funcionalidades antiguas (renegociación, ciertos extensiones) para simplificar/asegurar el protocolo.

### 3) Handshake — comparación paso a paso (resumida)

**Handshake clásico (TLS 1.2 / SSL-style):**

1. Cliente → `ClientHello` (versiones, ciphers, random)
2. Servidor → `ServerHello` (elige cipher), `Certificate`, (opcional) `ServerKeyExchange`, `ServerHelloDone`
3. Cliente → `ClientKeyExchange` (envía datos para establecer key material), `ChangeCipherSpec`, `Finished`
4. Servidor → `ChangeCipherSpec`, `Finished`
   Resultado: canal cifrado listo.

**Handshake TLS 1.3 (más corto y seguro):**

1. Cliente → `ClientHello` (incluye key_share para DH y extensiones)
2. Servidor → `ServerHello` (responde key_share), `EncryptedExtensions`, `Certificate`, `CertificateVerify`, `Finished`
3. Cliente → `Finished`
   Resultado: claves ya derivadas antes del `Finished`, handshake más eficiente y con PFS por defecto.

_Nota:_ TLS 1.3 permite además **reanudación** y **0-RTT** para datos iniciales en sesiones resumidas (velocidad) — con riesgo de replay para operaciones no-idempotentes.

### 4) Ejemplos prácticos — comandos para probar y ver versiones / ciphers

**Usar `openssl s_client`** (prueba de conexión y versión):

- Forzar TLS 1.2:

```bash
openssl s_client -connect ejemplo.com:443 -tls1_2
```

- Forzar TLS 1.3 (si OpenSSL lo soporta):

```bash
openssl s_client -connect ejemplo.com:443 -tls1_3
```

Mira la sección `Server certificate` y `Cipher` en la salida para saber qué se negoció.

**Comprobar con `curl`:**

```bash
curl -I --tlsv1.2 https://ejemplo.com           # fuerza TLS 1.2
curl -I --tlsv1.3 https://ejemplo.com           # fuerza TLS 1.3 (si curl/OpenSSL lo permiten)
```

**Enumerar ciphers soportados por un servidor (con nmap):**

```bash
nmap --script ssl-enum-ciphers -p 443 ejemplo.com
```

### 5) Ejemplo de configuración (NGINX) — habilitar TLS moderno

Fragmento de ejemplo (mínimo y práctico — adaptar a tu distro y certs):

```nginx
server {
    listen 443 ssl;
    server_name ejemplo.com;

    ssl_certificate /etc/letsencrypt/live/ejemplo.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/ejemplo.com/privkey.pem;

    # Habilitar sólo TLS modernos
    ssl_protocols TLSv1.2 TLSv1.3;

    # Preferir server ciphers y PFS (cadena recomendada a revisar)
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-CHACHA20-POLY1305';

    # Mejoras de performance / seguridad
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;
    ssl_stapling on;
    ssl_stapling_verify on;

    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
}
```

> Consejo: usa plantillas **actualizadas** (Mozilla SSL Configuration Generator o guías del proveedor) para la mejor compatibilidad y seguridad.

### 6) Ejemplos de vulnerabilidades históricas (por qué SSL está muerto)

- **POODLE (2014)**: explotaba fallback a SSLv3 para forzar descifrado de cookies — motivo para deshabilitar SSLv3.
- **BEAST (2011)**: atacaba TLS 1.0 CBC en ciertos contextos — mitigado por actualizaciones y uso de AEAD.
- **Heartbleed (2014)**: bug en OpenSSL (no en el protocolo en sí) que filtró memoria del servidor — demuestra que además del protocolo, convivir con libs actualizadas importa.
- Otros: CRIME, FREAK, LOGJAM — problemas con compresiones, export ciphers o parámetros DH débiles.
  Resultado: la industria deshabilitó SSL y versiones antiguas de TLS.

### 7) Certificados: ¿“SSL certificate” vs “TLS certificate”?

- **No hay diferencia técnica**: un certificado X.509 sirve tanto para SSL como para TLS.
- La confusión es histórica: la industria sigue llamando “certificado SSL” aunque el protocolo actual sea TLS.

### 8) Rendimiento y compatibilidad

- **TLS 1.3** ahorra tiempo (menos viajes de ida y vuelta) y reduce latencia, especialmente en conexiones nuevas.
- **Compatibilidad**: algunos clientes antiguos (browsers muy viejos, dispositivos embebidos) no entienden TLS 1.3; por eso muchos hosts mantienen TLS 1.2 habilitado también.

### 9) Buenas prácticas (checklist)

1. **Deshabilitar** SSLv2/SSLv3 y TLS 1.0/1.1.
2. **Habilitar** TLS 1.2 (mínimo) y TLS 1.3.
3. **Usar ECDHE** para intercambio de claves (PFS).
4. **Priorizar AEAD** ciphers (AES-GCM, ChaCha20-Poly1305).
5. **Habilitar HSTS** en HTTPS (Strict-Transport-Security).
6. **Usar OCSP stapling** para mejorar verificación de revocación y latencia.
7. **Renovar y automatizar certificados** (Let's Encrypt, AWS ACM, etc.).
8. **Mantener OpenSSL/LibreSSL/boringssl/ssld libs actualizadas**.
9. **Auditar** con herramientas (SSL Labs, nmap, testssl.sh).

### 10) Ejemplo práctico: comprobar si un servidor soporta TLS 1.3

```bash
# intenta conectar con TLS 1.3
openssl s_client -connect ejemplo.com:443 -tls1_3
```

- Si la conexión falla con error de protocolo, probablemente el servidor no soporta TLS 1.3.
- Observa la línea `Cipher` y `Protocol` en la salida.

### 11) Resumen comparativo final

| Tema                  | SSL                                 | TLS                                                     |
| --------------------- | ----------------------------------- | ------------------------------------------------------- |
| Estado                | Obsoleto / inseguro                 | Actual / recomendado                                    |
| Versiones relevantes  | SSL 2.0, SSL 3.0 (no usar)          | TLS 1.0 → 1.3 (usar 1.2/1.3)                            |
| Intercambio de claves | puede usar RSA (no PFS)             | (EC)DHE por defecto → PFS                               |
| Cifrado               | numerosas suites débiles históricas | AEAD preferido; TLS1.3 tiene suites seguras por defecto |
| Handshake             | más pasos, menos seguro             | handshake más corto y seguro (TLS1.3)                   |
| Certificados          | X.509 (mismo que TLS)               | X.509                                                   |

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**                 | **Siguiente 3**       |
| ------------------ | --------------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./12_1_FTP_vs_SFTP.md) | [⏩](./12_3_IPSec.md) |
