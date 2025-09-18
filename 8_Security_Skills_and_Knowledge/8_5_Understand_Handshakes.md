| **Inicio**         | **atrás 4**                                 | **Siguiente 6**                     |
| ------------------ | ------------------------------------------- | ----------------------------------- |
| [🏠](../README.md) | [⏪](./8_4_Basics_of_Threat_Intel_OSINT.md) | [⏩](./8_6_Understand_CIA_Triad.md) |

---

## **Índice**

| Temario                                                                                      |
| -------------------------------------------------------------------------------------------- |
| [209. Explicación del protocolo de enlace TLS](#209-explicación-del-protocolo-de-enlace-tls) |

# **Understand Handshakes**

## **209. Explicación del protocolo de enlace TLS**

![Understand Handshakes](/img/8_Security_Skills_and_Knowledge/Understand_Handshakes.png "Understand Handshakes")

### ¿Qué es TLS y para qué sirve?

TLS (Transport Layer Security) es el protocolo estándar que protege las comunicaciones entre un cliente y un servidor en Internet, proporcionando **confidencialidad**, **integridad** y **autenticidad** de los datos (p. ej. HTTPS). TLS define varios sub-protocolos y mensajes para negociar parámetros criptográficos y cifrar el tráfico de aplicación.

### Breve historia (muy resumida)

- **SSL** fue el ancestro (Netscape).
- Evolucionó a **TLS 1.0 / 1.1 / 1.2** (TLS 1.2📜 RFC5246) y más recientemente **TLS 1.3** (RFC8446), que simplifica el handshake y mejora seguridad y rendimiento.

### Objetivos principales de TLS

1. **Confidencialidad:** cifrado simétrico de los datos de aplicación.
2. **Integridad:** detección de modificación de mensajes (MAC / AEAD).
3. **Autenticidad:** verificación del servidor (y opcionalmente del cliente) mediante certificados X.509.
4. **Perfect Forward Secrecy (PFS):** evitar que la rotura de la clave privada del servidor permita descifrar sesiones pasadas (cuando se usa ECDHE/DHE).

### Arquitectura por capas (visión simple)

- **Record Layer:** fragmenta, comprime (si está habilitada), aplica cifrado/MAC y transporta mensajes de aplicación o handshake.
- **Handshake Layer:** mensajes para negociar versión, cifrados, intercambiar certificados y derivar claves.
- **ChangeCipherSpec:** señala el cambio a parámetros criptográficos activos (en TLS ≤1.2).
- **Alert Protocol:** envía errores (fatal / warning).
  (En TLS 1.3 muchas cosas se reordenan y las partes del handshake se cifran antes que en TLS 1.2).

### Handshake: TLS 1.2 (pasos clave — simplificado)

Secuencia típica (cliente → servidor, mensajes básicos):

1. **ClientHello** — versión, lista de cipher suites, extensiones (SNI, supported_groups, etc.), nonce (Random).
2. **ServerHello** — versión seleccionada, cipher suite elegido, server random.
3. **Certificate** — servidor envía su certificado X.509 (cadena).
4. **ServerKeyExchange** — (si aplica) parámetros para el intercambio de claves (ej. DHE/ECDHE).
5. **CertificateRequest** — (opcional) pide certificado cliente (para mTLS).
6. **ServerHelloDone**.
7. **ClientCertificate** — (si fue solicitado).
8. **ClientKeyExchange** — cliente envía datos para crear la clave de sesión (p. ej. pre-master secret RSA o clave ECDHE ephemeral).
9. **CertificateVerify** — (si cliente presentó certificado) prueba de posesión de clave privada.
10. **ChangeCipherSpec** → ambas partes activan los parámetros negociados.
11. **Finished** → mensajes autenticados con los nuevos parámetros; a partir de aquí va **Application Data** (cifrada).

    (En TLS 1.2 el intercambio de claves y el ChangeCipherSpec ocurren en más pasos y en general requiere más RTTs en comparación con TLS 1.3).

### Handshake: TLS 1.3 (qué cambia — puntos clave)

- **Handshake más corto**: combina y reordena pasos para reducir RTT (normalmente 1 RTT para full handshake). TLS 1.3 cifra casi todos los mensajes del handshake después del ServerHello, reduciendo la exposición de información.
- **Key exchange por defecto es (EC)DHE** → PFS por defecto; se eliminan los intercambios RSA para key-exchange puro.
- **0-RTT resumptions**: permite que clientes que ya se han conectado antes envíen datos “rápidos” (0-RTT) en la primera ida — útil para latencia pero con restricciones de replay.
- **Cipher suites simplificadas**: se dejan solo los algoritmos AEAD (ej. AES-GCM, ChaCha20-Poly1305).

  TLS 1.3 fue diseñado para ser más simple, más seguro y con mejor rendimiento.

### Ejemplo práctico — cómo interpretar un nombre de _cipher suite_

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` (TLS 1.2 style) → desglosado:

- `ECDHE` → intercambio de claves: ECDH ephemeral (proporciona PFS).
- `RSA` → firma/autenticación: certificado del servidor usa RSA (firma).
- `AES_128_GCM` → algoritmo de cifrado simétrico y modo AEAD.
- `SHA256` → usado en la función PRF/HMAC en el contexto de TLS 1.2.

En **TLS 1.3** las suites tienen nombres como `TLS_AES_128_GCM_SHA256` → el intercambio de claves y autenticación no forman parte del nombre de la suite (ECDHE es obligatorio y separado).

### Comandos útiles (no los ejecuto aquí — son para que los pruebes)

- Ver handshake / certificados de un servidor (OpenSSL):

  ```bash
  # Forzar TLS1.2
  openssl s_client -connect example.com:443 -servername example.com -tls1_2

  # Probar TLS1.3
  openssl s_client -connect example.com:443 -servername example.com -tls1_3

  # Mostrar certificados
  openssl s_client -connect example.com:443 -showcerts
  ```

  En la salida fija la línea `Cipher` (cipher seleccionado) y la sección `Certificate chain` con la cadena X.509. Para debug también `-msg` muestra mensajes handshake.

- Extraer info de un certificado (PEM):

  ```bash
  openssl x509 -in cert.pem -noout -text
  ```

### Mutual TLS (mTLS) — ejemplo corto

En mTLS, **ambas** partes presentan certificados: el servidor verifica el certificado del cliente y el cliente verifica el del servidor. Es común en APIs internas, B2B o servicios bancarios como mecanismo fuerte de autenticación (en vez de solo usuario/clave). La negociación añade `CertificateRequest` por parte del servidor y `ClientCertificate` + `CertificateVerify` por parte del cliente (en TLS ≤1.2; en TLS 1.3 están integrados en el flujo cifrado).

### Perfect Forward Secrecy (PFS)

PFS asegura que la **compromiso futuro** de la clave privada del servidor **no** permita descifrar sesiones pasadas, porque cada sesión utiliza claves efímeras (p. ej. ECDHE). Por eso las configuraciones modernas **prefieren ECDHE** sobre RSA key-exchange.

### Riesgos históricos y mitigaciones

- **POODLE / SSLv3, BEAST, CRIME** → vulnerabilidades históricas que motivaron deshabilitar SSLv3 y TLS 1.0/1.1 y desactivar compresión TLS.
- **Downgrade attacks** → mitigados en TLS 1.3 y mediante la protección de versiones (TLS_FALLBACK_SCSV).
- **Mala configuración (cipher suites débiles, certificados expirados, DH pequeño)** → se mitiga con buenas políticas de configuración (ver más abajo).

  TLS 1.3 reduce muchas de estas superficies de ataque al simplificar opciones inseguras.

### Buenas prácticas operativas (configuración segura)

- **Soportar TLS 1.3** y TLS 1.2; **deshabilitar TLS 1.0/1.1 y SSLv3**.
- **Priorizar ECDHE** (PFS) y AEAD ciphers: AES-GCM, ChaCha20-Poly1305.
- **Habilitar OCSP stapling** y monitorizar revocaciones.
- **Usar certificados de buena procedencia** (CA confiable), automatizar rotación (ACME/Let’s Encrypt) y controlar expiraciones.
- **HSTS** (HTTP Strict Transport Security) para forzar HTTPS desde el navegador.
- **Testear** con herramientas como SSL Labs / configuradores (y seguir guías como la de Mozilla). ([MDN Web Docs][8], [Reddit][9])

### Ejemplo de snippet (NGINX) — configuración básica moderna

```nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate /etc/ssl/certs/example.com.crt;
    ssl_certificate_key /etc/ssl/private/example.com.key;

    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers off;

    # Cipher list for TLS 1.2 (TLS 1.3 ciphers are configured separately by the stack)
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:...';

    ssl_session_timeout 1d;
    ssl_session_cache shared:SSL:10m;
    ssl_session_tickets off;

    ssl_stapling on;
    ssl_stapling_verify on;
    resolver 1.1.1.1 8.8.8.8 valid=300s;

    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
}
```

Ajusta `ssl_ciphers` según la guía de Mozilla/tu política y prueba con SSL Labs después de desplegar.

### Diagnóstico y troubleshooting rápido

- **Cadena de certificados rota**: `openssl s_client -connect host:443 -showcerts` y revisar `Verify return code`.
- **Negociación de versión/cipher fallida**: forzar `-tls1_2` o `-tls1_3` para aislar problema.
- **PFS ausente**: comprobar que la suite elegida incluye `DHE`/`ECDHE`.
- **OCSP stapling**: revisar logs y respuesta stapled en `openssl s_client -status`.

### Resumen ejecutivo (lo más importante)

- TLS protege privacidad e integridad en la capa de transporte; **TLS 1.3** es la versión recomendada por seguridad y rendimiento.
- Habilita **ECDHE** (PFS), AEAD cipher suites y OCSP stapling; deshabilita versiones y ciphers antiguos.
- Testea tu server con herramientas externas (SSL Labs) y automatiza la rotación de certificados.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**                                 | **Siguiente 6**                     |
| ------------------ | ------------------------------------------- | ----------------------------------- |
| [🏠](../README.md) | [⏪](./8_4_Basics_of_Threat_Intel_OSINT.md) | [⏩](./8_6_Understand_CIA_Triad.md) |
