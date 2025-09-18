| **Inicio**         | **atrás 1**                         | **Siguiente 3**                                |
| ------------------ | ----------------------------------- | ---------------------------------------------- |
| [🏠](../README.md) | [⏪](./3_1_Networking_Knowledge.md) | [⏩](./3_3_Common_Protocols_and_their_Uses.md) |

---

## **Índice**

| Temario                                                |
| ------------------------------------------------------ |
| [64. ¿Qué es el modelo OSI?](#64-qué-es-el-modelo-osi) |

---

# **Comprender el modelo OSI**

## **64. ¿Qué es el modelo OSI?**

![modelo OSI](/img/3_Networking_Knowledge/modelo_OSI.png "modelo OSI")

### 1. ¿Qué es el modelo OSI?

El **modelo OSI (Open Systems Interconnection)** es un **marco de referencia teórico** creado por la ISO (International Organization for Standardization) que divide la comunicación en redes en **7 capas**.

Cada capa tiene funciones específicas y se comunica con las capas adyacentes.
No es un protocolo en sí, sino un **modelo conceptual** que ayuda a entender cómo los datos viajan desde un dispositivo a otro.

👉 Ejemplo: Cuando envías un WhatsApp, la app (capa 7) genera el mensaje, las capas intermedias lo encapsulan y finalmente la capa física lo transmite por el aire vía WiFi o datos móviles.

### 2. ¿Por qué es importante el modelo OSI?

El modelo OSI es importante porque:

- 📚 **Estandariza la comunicación:** fabricantes y desarrolladores siguen las mismas reglas.
- 🔎 **Facilita el aprendizaje y la enseñanza:** permite estudiar redes paso a paso.
- 🛠️ **Ayuda a diagnosticar problemas:** puedes identificar si el fallo está en la capa física (cable roto), transporte (puerto bloqueado), aplicación (error de software), etc.
- 🔗 **Promueve interoperabilidad:** equipos de distintos fabricantes pueden comunicarse (ej. Cisco, Juniper, Huawei).

### 3. ¿Cuáles son las siete capas del modelo OSI?

De abajo hacia arriba:

1. **Física** → Transmite bits a través de cables o inalámbrico.

   Ej.: Cable Ethernet, WiFi, fibra óptica.

2. **Enlace de Datos** → Organiza tramas, controla errores de transmisión local, usa direcciones MAC.

   Ej.: Ethernet, WiFi (IEEE 802.11), Switches.

3. **Red** → Encaminamiento de paquetes entre redes.

   Ej.: IP, ICMP, Routers.

4. **Transporte** → Entrega confiable extremo a extremo.

   Ej.: TCP (fiable), UDP (rápido).

5. **Sesión** → Administra sesiones de comunicación entre aplicaciones.

   Ej.: NetBIOS, RPC.

6. **Presentación** → Traducción, compresión y cifrado de datos.

   Ej.: SSL/TLS, JPEG, MP3.

7. **Aplicación** → Interacción con el usuario y servicios finales.

   Ej.: HTTP, FTP, DNS, SMTP.

📌 Mnemonics para recordarlo:

- **De arriba a abajo:** _All People Seem To Need Data Processing_.
- **De abajo a arriba:** _Please Do Not Throw Sausage Pizza Away_. 🍕

### 4. ¿Cómo se produce la comunicación en el modelo OSI?

Se basa en **encapsulación y desencapsulación**:

1. El **emisor** crea un mensaje en la aplicación (ej. navegador → petición HTTP).
2. Cada capa **agrega su encabezado** (metadatos necesarios).

   - Ej.: TCP añade puertos, IP añade dirección origen/destino, Ethernet añade MAC.

3. En la **capa física**, los bits viajan por el medio (cable, aire).
4. El **receptor** recibe los datos y **cada capa elimina su encabezado**, hasta entregar el mensaje a la aplicación.

👉 Ejemplo práctico:

Al visitar `https://example.com` desde tu navegador:

- **Aplicación:** HTTP solicita la página.
- **Presentación:** TLS cifra la conexión.
- **Transporte:** TCP divide en segmentos y asegura entrega.
- **Red:** IP enruta hacia el servidor.
- **Enlace:** Ethernet/WiFi envuelve en tramas.
- **Física:** bits viajan por el cable/aire.
- En el servidor se hace el proceso inverso.

### 5. ¿Cuáles son las alternativas al modelo OSI?

El OSI es **teórico**, en la práctica se usa más el **modelo TCP/IP**, que es más simple y está directamente implementado en Internet.

📌 **Modelo TCP/IP (4 capas):**

1. **Acceso a la red** (equivale a Física + Enlace).
2. **Internet** (equivale a Red → IP, ICMP).
3. **Transporte** (TCP, UDP).
4. **Aplicación** (HTTP, DNS, FTP, SMTP, etc.).

✅ Este modelo es más usado porque Internet está basado en TCP/IP.

✅ Sin embargo, OSI sigue siendo útil para **enseñanza y diagnóstico**.

### 6. ¿Cómo puede AWS cumplir sus requisitos de redes informáticas?

Amazon Web Services (AWS) ofrece múltiples servicios que implementan estos modelos y protocolos de red:

1. **Capa Física / Enlace:**

   - AWS no expone la infraestructura física al usuario, pero maneja **centros de datos globales** interconectados.

2. **Capa de Red:**

   - **Amazon VPC (Virtual Private Cloud):** te permite crear redes privadas virtuales.
   - **Route 53:** DNS gestionado.
   - **Elastic IPs:** direcciones IP públicas fijas.
   - **AWS Transit Gateway:** conecta múltiples VPCs y redes locales.

3. **Capa de Transporte:**

   - AWS soporta **TCP y UDP** en sus instancias EC2.
   - Puedes configurar **Load Balancers (ELB/ALB/NLB)** que distribuyen tráfico entre servidores.

4. **Capa de Seguridad y Sesión/Presentación:**

   - **AWS Certificate Manager (ACM):** gestiona certificados TLS/SSL.
   - **VPN e IPsec:** para comunicaciones cifradas entre tu empresa y AWS.

5. **Capa de Aplicación:**

   - **Amazon CloudFront:** CDN para distribuir contenido.
   - **Amazon API Gateway:** para exponer APIs seguras.
   - **AWS App Mesh:** gestiona la comunicación entre microservicios.

👉 En pocas palabras:

- AWS implementa **protocolos estándar (TCP/IP, HTTPS, DNS, TLS, BGP, etc.)** sobre su infraestructura.
- Te permite diseñar arquitecturas de red **seguras, escalables y globales**, sin preocuparte por la capa física.

✅ **Resumen:**

- El **modelo OSI** es un marco conceptual con 7 capas.
- Es importante porque **estandariza, facilita el aprendizaje y la resolución de problemas**.
- La comunicación ocurre mediante **encapsulación de datos capa por capa**.
- Su alternativa práctica es el **modelo TCP/IP**.
- **AWS** usa protocolos basados en TCP/IP y ofrece servicios como VPC, Route 53, ELB, CloudFront y VPN para cumplir requisitos de red.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**                         | **Siguiente 3**                                |
| ------------------ | ----------------------------------- | ---------------------------------------------- |
| [🏠](../README.md) | [⏪](./3_1_Networking_Knowledge.md) | [⏩](./3_3_Common_Protocols_and_their_Uses.md) |
