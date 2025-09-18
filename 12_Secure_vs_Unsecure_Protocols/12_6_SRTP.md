| **Inicio**         | **atrás 5**           | **Siguiente 7**        |
| ------------------ | --------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./12_5_LDAPS.md) | [⏩](./12_7_S_MIME.md) |

---

## **Índice**

| Temario                                        |
| ---------------------------------------------- |
| [368. SRTP (RTP seguro)](#368-srtp-rtp-seguro) |

# **SRTP**

## **368. SRTP (RTP seguro)**

![SRTP](/img/12_Secure_vs_Unsecure_Protocols/SRTP.png "SRTP")

### 🎯 1. ¿Qué es RTP?

- **RTP (Real-time Transport Protocol)** es un protocolo de nivel de aplicación que se usa para transmitir **audio y video en tiempo real** sobre redes IP.
- Está definido en **RFC 3550**.
- **No garantiza entrega ni orden**, porque normalmente se usa junto con **UDP**, pero incluye **marcas de tiempo** y **números de secuencia** para que las aplicaciones puedan reconstruir el flujo multimedia.

👉 Ejemplos de uso:

- Videollamadas (Zoom, Teams, Meet).
- Streaming de medios (VoIP, IPTV).
- Juegos en línea con chat de voz.

**Características principales de RTP**:

- Funciona sobre **UDP** (normalmente en puertos dinámicos).
- Contiene cabecera con:

  - **Número de secuencia** (para detectar pérdidas/reordenar).
  - **Marca de tiempo (timestamp)** (para sincronizar audio/video).
  - **SSRC** (identificador del flujo).

- Normalmente se acompaña de **RTCP (RTP Control Protocol)** para estadísticas y sincronización entre flujos.

### 🔐 2. ¿Qué es SRTP?

- **SRTP (Secure Real-time Transport Protocol)** es una extensión de RTP definida en **RFC 3711**.
- Añade **cifrado, autenticación e integridad** al tráfico RTP.
- No cambia cómo funcionan las cabeceras RTP, pero encapsula la carga útil (payload) para proteger el contenido multimedia.

**Características principales de SRTP**:

- Usa algoritmos como **AES** para cifrar la voz/video.
- Usa **HMAC-SHA1** (o equivalentes) para integridad/autenticación.
- Puede **cifrar, autenticar, o ambas** cosas, dependiendo de la configuración.
- Las claves de SRTP suelen negociarse mediante protocolos como **SDES**, **MIKEY** o más comúnmente **DTLS-SRTP** (usado en WebRTC).

### ⚖️ 3. Diferencias entre RTP y SRTP

| Característica | RTP                                    | SRTP                                          |
| -------------- | -------------------------------------- | --------------------------------------------- |
| Seguridad      | Ninguna (datos en claro)               | Cifrado + autenticación                       |
| Puerto         | Dinámico sobre UDP (ej. 16384-32767)   | Igual que RTP (puertos dinámicos)             |
| Cabecera       | Simple, sin protección                 | Incluye campos adicionales para autenticación |
| Integridad     | No protegida                           | Protegida (HMAC)                              |
| Uso típico     | Telefonía IP antigua, streaming básico | WebRTC, VoIP moderno, videollamadas seguras   |

### 🛠️ 4. Ejemplo simplificado de RTP vs SRTP

#### RTP normal (sin cifrado)

Un paquete de voz VoIP contiene algo como:

```
[ Cabecera RTP ] [ Payload (audio G.711 en claro) ]
```

Cualquier atacante con Wireshark puede reproducir la llamada porque la voz está sin protección.

#### SRTP

El mismo paquete, pero con cifrado:

```
[ Cabecera RTP ] [ Payload cifrado AES ] [ Autenticador HMAC ]
```

- El atacante puede ver cabeceras RTP (timestamps, secuencia), pero **no puede descifrar la voz/video**.
- Además, si intenta modificar el paquete, el HMAC detecta la manipulación y descarta el paquete.

### 📞 5. Ejemplo de uso en VoIP y WebRTC

#### En VoIP (ejemplo SIP)

- Un cliente SIP inicia una llamada y negocia el uso de **SRTP** en el SDP (Session Description Protocol).
- Se acuerda la clave mediante SDES o DTLS.
- Los paquetes de voz viajan cifrados con SRTP en lugar de RTP.

#### En WebRTC (ejemplo videollamada en navegador)

- Los navegadores modernos **solo usan SRTP**.
- La negociación de claves se hace con **DTLS-SRTP**.
- Esto asegura que audio/video estén cifrados de extremo a extremo (entre navegadores, o navegador ↔ servidor).

### 🚨 6. Ventajas y desventajas

✅ **Ventajas de SRTP**:

- Confidencialidad (nadie escucha la llamada).
- Integridad (no se alteran paquetes).
- Autenticidad (solo fuente legítima).

⚠️ **Desventajas**:

- Sobrecarga de procesamiento (cifrado/descifrado).
- Sobrecarga en tamaño de paquetes (autenticador adicional).
- Compatibilidad (equipos VoIP antiguos no soportan SRTP).

### 📊 7. Resumen visual

- **RTP** = envío de audio/video en tiempo real → rápido, pero sin seguridad.
- **SRTP** = RTP + **cifrado (AES)** + **autenticación (HMAC)** → rápido y seguro.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**           | **Siguiente 7**        |
| ------------------ | --------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./12_5_LDAPS.md) | [⏩](./12_7_S_MIME.md) |
