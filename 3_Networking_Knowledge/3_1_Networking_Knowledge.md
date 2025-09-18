| **Inicio**         | **Siguiente 2**                         |
| ------------------ | --------------------------------------- |
| [🏠](../README.md) | [⏩](./3_2_Understand_the_OSI_model.md) |

---

## **Índice**

| Temario                                                                  |
| ------------------------------------------------------------------------ |
| [63. ¿Qué son los protocolos de red?](#63-qué-son-los-protocolos-de-red) |

---

# **Conocimiento de redes**

## **63. ¿Qué son los protocolos de red?**

![protocolos de red](/img/3_Networking_Knowledge/protocolos_de_red.jpg "protocolos de red")

### 1. Definición de protocolos de red

Un **protocolo de red** es un **conjunto de reglas y estándares** que permiten que **dos o más dispositivos** se comuniquen entre sí en una red.
Define **cómo se envían, reciben, interpretan y validan los datos**.

👉 Ejemplo:

- Cuando escribes `https://www.google.com` en el navegador:

  - Tu PC usa el **protocolo HTTP/HTTPS** para solicitar la página web.
  - Usa **DNS** para convertir `google.com` en una dirección IP.
  - Usa **TCP/IP** para transportar los datos hasta el servidor de Google y recibir la respuesta.

### 2. Tipos de protocolos de red

Podemos clasificarlos según su función:

1. **Protocolos de comunicación y transporte de datos**

   - **TCP (Transmission Control Protocol):** fiable, orientado a conexión (ej. web, email).
   - **UDP (User Datagram Protocol):** rápido, sin conexión (ej. streaming, juegos online).
   - **IP (Internet Protocol):** direccionamiento y envío de paquetes en la red.

2. **Protocolos de aplicación (servicios de usuario final)**

   - **HTTP/HTTPS:** transferencia de páginas web.
   - **FTP / SFTP:** transferencia de archivos.
   - **SMTP, POP3, IMAP:** correo electrónico.
   - **DNS:** traduce nombres de dominio en direcciones IP.

3. **Protocolos de administración y control**

   - **ICMP:** mensajes de control (ej. `ping`).
   - **SNMP:** administración de dispositivos de red.
   - **DHCP:** asignación automática de direcciones IP.

4. **Protocolos de seguridad**

   - **SSL/TLS:** cifrado de comunicaciones (HTTPS).
   - **IPsec:** cifrado a nivel de red (VPN).

### 3. ¿Cómo funcionan los protocolos de red?

Imagina que **los datos son una carta** 📩 enviada por correo:

1. **Se escribe la carta** → Datos de aplicación (ej. el contenido de un email).
2. **Se mete en un sobre con dirección** → Dirección IP del destinatario.
3. **El cartero la transporta** → Protocolos de transporte (TCP/UDP).
4. **Se entregan en la casa correcta** → Dirección MAC dentro de la red local.
5. **Se abre y se lee la carta** → La aplicación interpreta los datos.

➡️ Cada protocolo **cumple una función específica** en el viaje de la información.

### 4. ¿Qué son las capas del modelo OSI?

El **modelo OSI** (Open Systems Interconnection) divide la comunicación en **7 capas**.
Cada capa tiene una función específica y se apoya en la capa inferior.

📚 **Capas del OSI (de abajo hacia arriba):**

1. **Física:** Transmite bits por medios físicos (cables, ondas).
   👉 Ejemplo: cable Ethernet, WiFi.

2. **Enlace de datos:** Organiza tramas, maneja direcciones MAC, detecta errores.
   👉 Ejemplo: Ethernet, WiFi (802.11), Switches.

3. **Red:** Direcciona y enruta paquetes.
   👉 Ejemplo: IP, ICMP, routers.

4. **Transporte:** Asegura entrega de datos entre procesos.
   👉 Ejemplo: TCP (fiable), UDP (rápido).

5. **Sesión:** Establece, mantiene y finaliza sesiones de comunicación.
   👉 Ejemplo: NetBIOS, RPC.

6. **Presentación:** Traduce, comprime y cifra datos.
   👉 Ejemplo: SSL/TLS, JPEG, MP3.

7. **Aplicación:** Interacción con el usuario.
   👉 Ejemplo: HTTP, FTP, SMTP, DNS.

### 5. ¿Cómo funcionan los protocolos de red en cada capa del modelo OSI?

📦 **Ejemplo: Abrir una página web con HTTPS**

- **Capa 7 (Aplicación):** El navegador usa **HTTPS** para pedir `www.example.com`.
- **Capa 6 (Presentación):** Se cifra la comunicación con **TLS**.
- **Capa 5 (Sesión):** Se establece una sesión segura entre cliente y servidor.
- **Capa 4 (Transporte):** **TCP** divide los datos en segmentos y garantiza que lleguen.
- **Capa 3 (Red):** **IP** coloca las direcciones origen/destino y los routers encaminan.
- **Capa 2 (Enlace):** **Ethernet/WiFi** encapsula en tramas y usa direcciones MAC.
- **Capa 1 (Física):** Los bits viajan por el cable o por ondas de radio.

Cuando los datos llegan al servidor, **se deshace el proceso en orden inverso** hasta que el servidor web recibe la solicitud.

✅ En resumen:

- Los **protocolos de red** son reglas de comunicación.
- Se clasifican en **comunicación, aplicación, administración y seguridad**.
- Funcionan como capas de un **sistema de correo** que lleva datos desde tu PC hasta otro dispositivo.
- El **modelo OSI** ayuda a entender **qué protocolo actúa en qué parte** de la comunicación.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**                         |
| ------------------ | --------------------------------------- |
| [🏠](../README.md) | [⏩](./3_2_Understand_the_OSI_model.md) |
