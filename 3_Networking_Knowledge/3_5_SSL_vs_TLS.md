| **Inicio**         | **atrás 4**                                | **Siguiente 6**                      |
| ------------------ | ------------------------------------------ | ------------------------------------ |
| [🏠](../README.md) | [⏪](./3_4_Common_Ports_and_their_Uses.md) | [⏩](./3_6_Basics_of_NAS_and_SAN.md) |

---

## **Índice**

| Temario                                                                                  |
| ---------------------------------------------------------------------------------------- |
| [69. ¿Cuál es la diferencia entre SSL y TLS?](#69-cuál-es-la-diferencia-entre-ssl-y-tls) |
| [70. TLS vs SSL: ¿Cuál es la diferencia?](#70-tls-vs-ssl-cuál-es-la-diferencia)          |

---

# **SSL vs TLS**

## **69. ¿Cuál es la diferencia entre SSL y TLS?**

![SSL vs TLS](/img/3_Networking_Knowledge/SSL_y_TLS.avif "SSL vs TLS")

### 🔐 Diferencia entre SSL y TLS

**SSL (Secure Sockets Layer)** y **TLS (Transport Layer Security)** son protocolos criptográficos que aseguran la comunicación en redes, principalmente en **HTTPS**, correo, VPNs y otros servicios.

- **SSL** fue el primero (años 90, Netscape).
- **TLS** es su evolución y reemplazo (desde 1999).
- Hoy en día, cuando la gente dice “SSL”, en realidad casi siempre se refiere a **TLS**, porque **SSL está obsoleto e inseguro**.

#### Diferencias principales:

| Característica            | SSL                                  | TLS                                               |
| ------------------------- | ------------------------------------ | ------------------------------------------------- |
| Seguridad                 | Obsoleto, vulnerable (SSL 2.0, 3.0)  | Mucho más seguro y vigente (TLS 1.2 y 1.3)        |
| Año introducción          | 1995 (SSL 2.0), 1996 (SSL 3.0)       | 1999 (TLS 1.0), mejoras hasta TLS 1.3 (2018)      |
| Algoritmos criptográficos | Antiguos (MD5, RC4, DES) → inseguros | Algoritmos modernos (AES, ChaCha20, SHA-256)      |
| Handshake                 | Más lento, menos eficiente           | Más rápido, soporta Perfect Forward Secrecy (PFS) |
| Vigencia actual           | **Deprecado, no usar**               | **Estándar actual**                               |

👉 **Ejemplo real**:

Si entras a `https://google.com` y revisas en el navegador el certificado, verás que dice algo como:
`TLS 1.3` → ya no se usa SSL.

### 🤝 Similitudes entre SSL y TLS

A pesar de sus diferencias, comparten lo esencial:

- Ambos **cifran la comunicación** entre cliente y servidor (nadie puede leer el tráfico en texto plano).
- Ambos **verifican la identidad** del servidor mediante certificados digitales.
- Ambos funcionan en protocolos como **HTTPS, SMTP, IMAP, POP3, FTPS, etc.**
- Ambos utilizan un **handshake** inicial para negociar claves y algoritmos.

En resumen: TLS es **SSL mejorado**, pero el **concepto de seguridad de transporte cifrado es el mismo**.

### ⚖️ Diferencias clave: SSL vs TLS

1. **Seguridad**: SSL ya no es seguro, TLS sí.
2. **Compatibilidad**: navegadores y servidores modernos **solo aceptan TLS**.
3. **Velocidad**: TLS 1.3 es más rápido que SSL y hasta que versiones viejas de TLS.
4. **Soporte**: certificados actuales ya no usan SSL; todos los “certificados SSL” en realidad son certificados para TLS.

### 📜 Diferencia entre certificados SSL y certificados TLS

👉 Aquí hay mucha confusión en el nombre.

- **Un certificado digital X.509** (emitido por una CA) es lo que asegura que un sitio web es legítimo.
- El **certificado es el mismo** tanto si usas SSL o TLS: contiene clave pública, datos de la organización, fechas de validez, etc.
- Cuando hoy compras un “**certificado SSL**” de una empresa (ej. DigiCert, Let’s Encrypt, GoDaddy), en realidad estás comprando un **certificado para usar con TLS**, porque SSL ya no se usa.

⚠️ **En conclusión**: No hay diferencia técnica entre “certificado SSL” y “certificado TLS”, es solo marketing. El protocolo que se usa realmente es TLS.

### ☁️ ¿Cómo puede AWS satisfacer requisitos de SSL/TLS?

AWS ofrece varios servicios para manejar SSL/TLS, según la necesidad:

1. **AWS Certificate Manager (ACM)**

   - Emite y administra certificados SSL/TLS gratis.
   - Los certificados se integran automáticamente con **Elastic Load Balancers (ELB)**, **CloudFront (CDN)** y **API Gateway**.
   - Se renuevan solos, sin intervención manual.

2. **AWS ELB (Elastic Load Balancing)**

   - Termina la conexión TLS en el balanceador y puede reenviar tráfico cifrado al backend o en texto plano.
   - Permite usar certificados de ACM o certificados importados.

3. **Amazon CloudFront (CDN)**

   - Distribuye contenido globalmente con HTTPS usando certificados TLS.
   - Puede usar certificados de ACM en la región `us-east-1`.

4. **Amazon API Gateway**

   - Expone APIs de forma segura con TLS.
   - Soporta certificados personalizados de ACM.

5. **AWS IAM**

   - Permite subir certificados para usar con servicios de AWS.

6. **Configuración TLS moderna**

   - AWS recomienda usar **TLS 1.2 o 1.3**.
   - Se pueden deshabilitar protocolos inseguros (SSLv3, TLS 1.0, TLS 1.1).

👉 Ejemplo práctico en AWS:

- Tienes una app en EC2 detrás de un **Load Balancer**.
- Instalas un certificado con **ACM** en el ELB.
- Los usuarios acceden por `https://miapp.com`.
- El tráfico se cifra con **TLS 1.3** hasta el ELB.
- Entre ELB y EC2 puedes elegir:

  - **Reenviar TLS** (end-to-end encryption).
  - **Usar HTTP interno** (más rápido, pero menos seguro).

### ✅ Resumen final

- **SSL** = obsoleto. **TLS** = estándar actual.
- **Similitudes**: ambos aseguran la comunicación.
- **Diferencias clave**: seguridad, velocidad, soporte.
- **Certificados SSL y TLS** son lo mismo en práctica: certificados X.509 usados para TLS.
- **AWS** cubre SSL/TLS con ACM, ELB, CloudFront, API Gateway y configuraciones de seguridad modernas.

---

[🔼](#índice)

---

## **70. TLS vs SSL: ¿Cuál es la diferencia?**

### Resumen rápido (TL;DR)

- **SSL** (Secure Sockets Layer) es el **protocolo antiguo** creado por Netscape en los 90 — **obsoleto e inseguro**.
- **TLS** (Transport Layer Security) es su **sucesor**: más seguro, más rápido y el que se usa hoy (TLS 1.2 y TLS 1.3).
- Cuando alguien dice “certificado SSL” hoy en la práctica se refiere a un **certificado X.509** usado por **TLS**.

### Historia y versiones (muy breve)

- **SSL 2.0 / 3.0** → viejos; vulnerables (p. ej. POODLE).
- **TLS 1.0 / 1.1** → heredados, también obsoletos y deben deshabilitarse.
- **TLS 1.2** → aún muy usado y seguro si se configura bien.
- **TLS 1.3** → diseño más moderno: handshake más simple, mejores cifrados, rendimiento mejorado y mejor privacidad.

### Diferencias técnicas clave

#### 1) Seguridad y algoritmos

- **SSL** admite algoritmos y construcciones ahora inseguros (RC4, MD5, MACs débiles).
- **TLS** modernizó el conjunto: soporta AES-GCM, ChaCha20-Poly1305, SHA-256/384, y en TLS1.3 obliga a ciphers AEAD y (EC)DHE para forward secrecy.

#### 2) Handshake (inicio de conexión)

- **SSL / TLS1.0–1.2**: handshake más largo (varios mensajes separados). Soportan intercambio de claves RSA estático (sin perfect forward secrecy).
- **TLS1.3**: handshake simplificado (menos RTT — más rápido); por defecto usa (EC)DHE para **Perfect Forward Secrecy (PFS)**; soporta 0-RTT resumption (para reconexiones) con ciertas advertencias de replays.

Ejemplo conceptual (muy simplificado):

**TLS 1.2 (clásico)**

```
ClientHello
  -> ServerHello
  -> Certificate (+ ServerKeyExchange si aplica)
  -> ServerHelloDone
ClientKeyExchange
ChangeCipherSpec
Finished
-> Server ChangeCipherSpec
-> Server Finished
```

**TLS 1.3 (simplificado)**

```
ClientHello (incluye key shares)
-> ServerHello (produce keys rápidamente)
-> EncryptedExtensions, Certificate, CertificateVerify, Finished
(1 RTT para completar la autenticación mutua)
```

#### 3) Eliminación de cosas inseguras

TLS 1.3 **eliminó** muchas características peligrosas (compresión que facilitaba CRIME, RSA key exchange sin PFS, ciphers inseguros). SSL no las eliminó.

#### 4) Privacidad y rendimiento

- **TLS1.3** reduce latencia de conexión (mejor experiencia) y mejora privacidad (menos metadatos sin cifrar durante handshake).
- En resumen: **más seguro + más rápido**.

### Similitudes importantes

- Ambos **cifran el canal** entre cliente y servidor y usan **certificados X.509** para probar identidad.
- El objetivo (confidencialidad, integridad, autenticidad) es el mismo; TLS es la evolución práctica.

### Certificados: “SSL” vs “TLS”

- Un **certificado X.509** es el mismo sea que lo uses con SSL o con TLS.
- No existe un “certificado TLS” distinto en su estructura. Cuando compras “certificado SSL” te dan un X.509 que se usará en TLS.
- Diferencia práctica: **usar el certificado siempre sobre TLS**, no sobre SSL.

### Ejemplos prácticos (comprobaciones y comandos)

1. **Comprobar si un servidor acepta TLS 1.3 / TLS 1.2 (OpenSSL)**:

```bash
# Forzar TLS 1.3
openssl s_client -connect example.com:443 -tls1_3

# Forzar TLS 1.2
openssl s_client -connect example.com:443 -tls1_2
```

Salida útil: busca la línea `Protocol  : TLSv1.3` y `Cipher` para saber qué se negoció.

2. **Ver el certificado remoto**:

```bash
echo | openssl s_client -connect example.com:443 2>/dev/null | openssl x509 -noout -text
```

Verás fechas de validez, emisor, subject, SANs, clave pública, etc.

3. **Usar curl para forzar versión TLS**:

```bash
curl -I --tlsv1.3 https://example.com
curl -I --tlsv1.2 https://example.com
```

### Ejemplo de configuración segura (Nginx)

Fragmento recomendado para nginx moderno:

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers 'ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384
             ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305';
ssl_prefer_server_ciphers on;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 10m;
ssl_stapling on;
ssl_stapling_verify on;
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
```

**Notas:** TLS1.3 ciphers se negocian aparte en implementaciones modernas. Ajusta según tu OpenSSL/NGINX.

### Vulnerabilidades famosas (contexto)

- **POODLE** → afecta SSLv3 (otro motivo para DESHABILITAR SSL).
- **BEAST/CRIME** → aprovecharon diseño de bloques y compresión en versiones antiguas.
- **Heartbleed** → fallo de implementación en OpenSSL (no es diseño SSL/TLS, sino bug en la librería).
  → Conclusión: **no solo importa el protocolo, también la implementación** (bibliotecas: OpenSSL, BoringSSL, NSS, SChannel).

### Recomendaciones prácticas (checklist para administradores)

1. **Deshabilitar** SSLv2, SSLv3, TLS 1.0 y TLS 1.1.
2. **Habilitar** TLS 1.2 y TLS 1.3 (si tu plataforma lo soporta).
3. **Priorizar PFS** (ECDHE) en la lista de cifrados.
4. **Usar ciphers AEAD**: AES-GCM o ChaCha20-Poly1305.
5. **Habilitar OCSP Stapling** y monitorear revocaciones.
6. **Activar HSTS** para forzar HTTPS en navegadores.
7. **Renovar certificados automáticamente** (Let’s Encrypt o ACM en AWS).
8. **Mantener la pila TLS actualizada** (OpenSSL/LibreSSL/BoringSSL/OS updates).
9. **Probar regularmente** con herramientas de análisis (SSL Labs, scanners internos).
10. **Monitorear logs y alertas** de fallos TLS o renegociaciones inusuales.

### Breve nota sobre compatibilidad

- Algunos clientes/IoT viejos sólo soportan TLS1.0/1.1; al deshabilitarlos hay que evaluar impacto.
- Balance: **seguridad primero**, pero si tienes clientes legados, considera TLS termination en un gateway que soporte re-negociación segura y autenticación de clientes legados de forma controlada.

### Cómo lo implementa AWS (rápido)

- AWS **recomienda y soporta TLS 1.2 y 1.3** en servicios como **ELB/ALB/NLB, CloudFront, API Gateway**.
- Con **AWS Certificate Manager (ACM)** obtienes certificados X.509 gestionados y auto-renovables.
- En AWS configuras políticas de seguridad TLS (ciphers, versiones) en load balancers o CloudFront.

### Conclusión

- **TLS es la evolución segura y rápida de SSL**.
- Nunca uses SSL hoy: **habilita TLS 1.2/1.3**, configura ciphers modernos y gestiona certificados correctamente.
- La diferencia real para un administrador es: **TLS = usarlo y configurarlo bien; SSL = deshabilitarlo**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**                                | **Siguiente 6**                      |
| ------------------ | ------------------------------------------ | ------------------------------------ |
| [🏠](../README.md) | [⏪](./3_4_Common_Ports_and_their_Uses.md) | [⏩](./3_6_Basics_of_NAS_and_SAN.md) |
