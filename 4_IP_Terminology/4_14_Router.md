| **Inicio**         | **atrás 13**       | **Siguiente 15**       |
| ------------------ | ------------------ | ---------------------- |
| [🏠](../README.md) | [⏪](./4_13_IP.md) | [⏩](./4_15_Switch.md) |

---

## **Índice**

| Temario                                                                                      |
| -------------------------------------------------------------------------------------------- |
| [102. ¿Qué es un enrutador?](#102-qué-es-un-enrutador)                                       |
| [103. ¿Qué es un router y cómo funciona?](#103-qué-es-un-router-y-cómo-funciona)             |
| [104. Todo lo que hacen los enrutadores](#104-todo-lo-que-hacen-los-enrutadores)             |
| [105. ¿Cómo reenvían paquetes los enrutadores?](#105-cómo-reenvían-paquetes-los-enrutadores) |

# **Router**

## **102. ¿Qué es un enrutador?**

![Router](/img/4_IP_Terminology/Router.jpg "Router")

### 🔹 ¿Qué es un enrutador?

Un **enrutador** (o _router_) es un dispositivo de red que se encarga de **conectar diferentes redes entre sí** y dirigir el tráfico de datos de manera eficiente.

En términos simples, es como un **“director de tránsito”** de internet: recibe información y decide por dónde enviarla para que llegue a su destino.

📌 Ejemplo:

- Tienes una laptop y un celular conectados a la misma red WiFi.
- El router recibe las peticiones de ambos (por ejemplo, abrir YouTube en la laptop y WhatsApp en el celular).
- El router envía cada petición a Internet y devuelve las respuestas correctas a cada dispositivo.

### 🔹 ¿Cómo funciona un router?

El router funciona a través de **direcciones IP**. Cada dispositivo conectado a él recibe una IP local (ejemplo: `192.168.1.10`).

El proceso general es:

1. Un dispositivo (ejemplo: tu PC) pide acceder a una página web (ejemplo: `www.google.com`).
2. El router recibe la solicitud y la envía al **módem** (que conecta con el proveedor de internet).
3. El módem la manda a internet y recibe la respuesta (la página web).
4. El router decide a qué dispositivo de la red local debe devolver esa respuesta.

📌 Ejemplo:

- Tu PC abre Facebook y tu hermano abre TikTok.
- El router se asegura de que la respuesta de Facebook llegue a tu PC y no al celular de tu hermano.

### 🔹 Diferencia entre un router y un módem

Aunque muchas veces vienen en un mismo dispositivo, son **cosas diferentes**:

| Dispositivo | Función principal                                                                             | Ejemplo                                                              |
| ----------- | --------------------------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| **Módem**   | Se conecta al proveedor de internet (ISP) y convierte la señal para que pueda usarse en casa. | Es como el **puente entre tu casa e internet**.                      |
| **Router**  | Distribuye la conexión entre varios dispositivos dentro de tu red local.                      | Es como el **repartidor** que da internet a tu laptop, celular y TV. |

📌 Ejemplo real:

- Si solo tuvieras un módem → podrías conectar un solo dispositivo por cable.
- Con un router → todos tus dispositivos tienen acceso a internet al mismo tiempo.

### 🔹 Tipos de routers

Existen varios tipos, según su uso:

1. **Router doméstico (inalámbrico/WiFi)**

   - El más común.
   - Permite conectar dispositivos por WiFi o cable.
   - Ejemplo: el router de tu casa que te da WiFi.

2. **Router empresarial**

   - Más potente, diseñado para manejar muchos dispositivos.
   - Ejemplo: routers que se usan en universidades o empresas grandes.

3. **Router inalámbrico portátil (MiFi)**

   - Se conecta a través de una tarjeta SIM (4G/5G).
   - Ejemplo: los routers portátiles que usan turistas para tener internet móvil.

4. **Router virtual**

   - Software que convierte una PC en un router.
   - Ejemplo: cuando compartes internet desde tu laptop o celular.

### 🔹 ¿Qué es un SSID?

El **SSID (Service Set Identifier)** es el **nombre de la red WiFi**.

- Es lo que ves cuando buscas redes desde tu celular o laptop.
- Ejemplo: `WiFi-Casa`, `Gustavo_5G`, `Movistar1234`.

Los routers permiten cambiar el SSID para personalizarlo.

📌 Ejemplo:

Si en tu celular aparecen varias redes disponibles:

- `WiFi-Mamá`
- `Vecino_Internet`
- `CaféLibre`

  → cada uno de esos nombres es un **SSID**.

### 🔹 Retos de seguridad asociados a los routers

Los routers son **puntos críticos de seguridad**, porque si alguien accede a tu router, puede espiar o controlar tu tráfico.

Algunos retos son:

1. **Contraseñas débiles o por defecto**

   - Muchos routers vienen con claves como `admin/admin` o `12345678`.
   - Ejemplo: si no cambias esa clave, cualquiera puede entrar a tu configuración.

2. **Ataques de fuerza bruta o diccionario**

   - Hackers intentan adivinar tu contraseña WiFi probando muchas combinaciones.

3. **Firmware desactualizado**

   - Los routers tienen un sistema operativo interno. Si no se actualiza, puede tener vulnerabilidades.

4. **Redes abiertas o mal configuradas**

   - Si tu router no tiene cifrado WPA2/WPA3, cualquiera puede conectarse.

5. **Secuestro de DNS (DNS Hijacking)**

   - El atacante cambia la configuración del router para redirigir el tráfico a páginas falsas.

📌 Ejemplo real:

Si un atacante entra a tu router, puede hacer que al escribir `www.banco.com` en tu navegador, en lugar de ir al banco verdadero, te lleve a una página falsa que roba tus datos.

👉 En resumen:

- **Router = director de tráfico** entre tu red local e internet.
- **Módem = puente** con el proveedor de internet.
- **SSID = nombre de tu WiFi.**
- Hay distintos tipos de routers (domésticos, empresariales, portátiles, virtuales).
- Su seguridad es **fundamental** para proteger tus datos y dispositivos.

---

[🔼](#índice)

---

## **103. ¿Qué es un router y cómo funciona?**

### 🔹 ¿Qué es un router?

Un **router** (enrutador) es un dispositivo de red que sirve para **conectar diferentes redes entre sí** y dirigir el tráfico de datos.

👉 En palabras simples:

El router es como un **director de tránsito** en una ciudad. Cada auto (paquete de datos) tiene una dirección (dirección IP), y el router decide qué camino debe tomar para llegar a su destino.

📌 Ejemplo:

- En tu casa tienes una laptop, un celular y una TV conectados al WiFi.
- Todos quieren entrar a internet al mismo tiempo (por ejemplo, la laptop abre YouTube, el celular WhatsApp y la TV Netflix).
- El router organiza el tráfico, envía cada petición a internet y se asegura de devolver la respuesta al dispositivo correcto.

### 🔹 ¿Cómo funciona un router?

El router trabaja usando **direcciones IP** y **tablas de enrutamiento** para saber a dónde mandar cada paquete de datos.

El proceso básico es este:

1. **Asignación de IP**

   - Cuando conectas tu celular al WiFi, el router le da una dirección IP interna (ejemplo: `192.168.1.5`).
   - Así, cada dispositivo tiene su “placa única” dentro de la red.

2. **Recepción de datos**

   - Tu laptop pide abrir `www.google.com`.
   - Esa solicitud llega primero al router.

3. **Envío hacia Internet**

   - El router envía la petición al módem, que está conectado al proveedor de internet (ISP).

4. **Respuesta de Internet**

   - Google responde enviando la página.
   - El router recibe esa respuesta y la entrega específicamente a tu laptop (no al celular ni a la TV).

5. **Gestión del tráfico**

   - Repite lo mismo para todos los dispositivos en casa, manteniendo el orden y evitando que los datos se crucen.

### 📌 Ejemplo práctico

Imagina tu casa como un **condominio**:

- Cada departamento (dispositivo: laptop, celular, tablet) tiene un número (dirección IP).
- El portero (router) recibe todos los paquetes (datos) que llegan desde fuera.
- Si llega un paquete para el departamento 3 (tu laptop), el portero se asegura de entregarlo ahí, no al departamento 5 (tu celular).

### 🔑 Resumen

- El **router conecta tu red local (casa/empresa)** con Internet.
- Se encarga de **dirigir los datos** al dispositivo correcto.
- Funciona asignando **IP internas**, consultando **tablas de enrutamiento**, y organizando el tráfico.

---

[🔼](#índice)

---

## **104. Todo lo que hacen los enrutadores**

### 1. **Conectar redes diferentes**

- El trabajo principal de un router es **conectar dos o más redes distintas**.
- En casa, conecta tu **red local (LAN)** con la **red del proveedor de Internet (WAN)**.

📌 Ejemplo:

Tu laptop (LAN → `192.168.1.10`) quiere entrar a YouTube (WAN → `142.250.72.206`).
El router hace de **puente** entre ambas.

### 2. **Asignar direcciones IP a los dispositivos (DHCP)**

- El router tiene un servidor **DHCP** que entrega direcciones IP automáticamente a cada dispositivo conectado.
- Así no tienes que configurar la IP manualmente.

📌 Ejemplo:

- Celular → `192.168.1.2`
- Laptop → `192.168.1.3`
- Smart TV → `192.168.1.4`

### 3. **Enrutamiento de paquetes**

- El router decide **la mejor ruta** para que los datos lleguen a su destino.
- Utiliza **tablas de enrutamiento** y protocolos como RIP, OSPF o BGP en redes grandes.

📌 Ejemplo:

Si tienes dos caminos para llegar a una ciudad, el router elegirá el más rápido o el disponible.

### 4. **Traducción de direcciones (NAT)**

- Los routers usan **NAT (Network Address Translation)** para que varios dispositivos en tu casa compartan **una sola IP pública** hacia Internet.

📌 Ejemplo:

- Tus dispositivos en casa → IP privadas (`192.168.1.x`).
- El router → una única IP pública (`45.67.89.10`).
- Para internet, parece que **todos salen con la misma dirección**.

### 5. **Crear y gestionar redes inalámbricas (WiFi)**

- Muchos routers también son **puntos de acceso WiFi**.
- Permiten conectar celulares, laptops y tablets sin necesidad de cables.

📌 Ejemplo:

El SSID (nombre de red) `Casa-Gustavo` es creado por tu router para que te conectes.

### 6. **Firewall básico y filtrado de tráfico**

- Muchos routers incluyen un **firewall incorporado** que bloquea accesos no autorizados desde Internet.
- También pueden filtrar sitios web o bloquear puertos.

📌 Ejemplo:

Un padre puede configurar el router para que bloquee páginas de juegos en la red WiFi de sus hijos.

### 7. **Priorizar el tráfico (QoS – Quality of Service)**

- El router puede dar **prioridad de ancho de banda** a ciertas aplicaciones o dispositivos.

📌 Ejemplo:

Configuras el router para que **las videollamadas tengan más prioridad que las descargas**, evitando que tu Zoom se congele cuando alguien baja un archivo pesado.

### 8. **Soporte para VPN**

- Muchos routers permiten crear o conectar redes privadas virtuales (**VPNs**).
- Esto asegura que los datos viajen cifrados.

📌 Ejemplo:

Una empresa configura un router con VPN para que los empleados accedan de forma segura desde casa a la red corporativa.

### 9. **Seguridad de la red**

Los routers tienen varias funciones de seguridad:

- **WPA2/WPA3**: Cifran las contraseñas de WiFi.
- **Filtrado MAC**: Solo permite que ciertos dispositivos se conecten.
- **Actualizaciones de firmware** para parchar vulnerabilidades.

📌 Ejemplo:

Si tu vecino intenta conectarse a tu WiFi, el router puede bloquearlo si no está en la lista de dispositivos permitidos.

### 10. **Gestión remota y monitoreo**

- Puedes entrar a la configuración del router (ejemplo: `192.168.1.1`) para ver quién está conectado, cambiar la contraseña, abrir puertos, etc.

📌 Ejemplo:

Desde tu celular, puedes apagar el WiFi de los dispositivos conectados cuando quieras.

### 🔑 Resumen de lo que hacen los routers

1. Conectan diferentes redes (LAN ↔ WAN).
2. Asignan IPs automáticamente (DHCP).
3. Enrutan paquetes usando tablas de rutas.
4. Hacen NAT para compartir una sola IP pública.
5. Crean redes WiFi.
6. Actúan como firewall básico.
7. Priorizan el tráfico (QoS).
8. Soportan conexiones VPN.
9. Proporcionan seguridad (WPA2/WPA3, filtrado MAC, etc.).
10. Permiten administración remota y monitoreo.

👉 En resumen:

El **router es mucho más que un repartidor de internet**: es **el guardia, organizador, traductor y protector de tu red**.

---

[🔼](#índice)

---

## **105. ¿Cómo reenvían paquetes los enrutadores?**

### 🔹 ¿Cómo reenvían paquetes los enrutadores?

Los **routers** son dispositivos que leen la **información de destino** en los paquetes de datos (principalmente la **dirección IP de destino**) y deciden **por qué camino enviarlos**.

Ese proceso se llama **reenvío de paquetes (packet forwarding)**.

### 🔹 Proceso paso a paso

1. **Recepción del paquete**

   - El router recibe un paquete en una de sus interfaces de red (ejemplo: por el puerto de tu red local – LAN).

2. **Lectura de la cabecera IP**

   - Abre el paquete y revisa la **dirección IP de destino**.
   - No se fija en el contenido (los datos), solo en la cabecera.

3. **Consulta en la tabla de enrutamiento**

   - El router busca en su **tabla de enrutamiento** cuál es la mejor salida para llegar a esa IP.
   - Si no encuentra ruta, descarta el paquete.

4. **Decisión de la ruta**

   - El router elige la **mejor interfaz de salida** (puerto o conexión hacia otra red).

5. **Reescritura de cabecera (si es necesario)**

   - Si usa **NAT**, el router cambia la dirección IP de origen antes de enviarlo.

6. **Reenvío**

   - El paquete es enviado por la interfaz de salida hacia el siguiente salto (**next hop**), que puede ser otro router o el destino final.

### 🔹 Ejemplo práctico (casa)

1. Tu laptop (`192.168.1.10`) quiere abrir `www.google.com` (IP pública `142.250.72.206`).
2. El paquete sale con:

   - **Origen**: `192.168.1.10`
   - **Destino**: `142.250.72.206`

3. El router lo recibe y busca en su tabla:

   - Red `142.250.0.0/16` → enviar por la interfaz WAN (hacia internet).

4. El router cambia la IP de origen a su IP pública (`45.67.89.10`) usando NAT.
5. Reenvía el paquete hacia el siguiente router en Internet.
6. Google responde → y el router hace el proceso inverso para entregarlo de vuelta a tu laptop.

### 🔹 Ejemplo práctico (empresa con varios routers)

Imagina que estás en una empresa en Lima y quieres mandar un correo a alguien en México:

1. Tu PC → envía paquete al router de la empresa.
2. Router 1 (empresa) → ve que no es una IP local, lo manda al router del ISP.
3. Router 2 (ISP en Perú) → consulta en su tabla: mejor camino es vía Miami.
4. Router 3 (en Miami) → lo reenvía hacia México.
5. Router 4 (ISP en México) → lo pasa a la red local del destinatario.

Cada router en el camino **no sabe el contenido** del correo, solo mira:

👉 "Destino: IP X → ¿cuál es la mejor ruta para llegar allá?"

### 🔹 Tipos de reenvío de paquetes

1. **Reenvío directo**

   - Si el destino está en la **misma red local** del router.

     📌 Ejemplo: tu PC manda un archivo a otra PC de tu casa.

2. **Reenvío indirecto**

   - Si el destino está en otra red, el router lo manda al **siguiente salto (next hop)**.

     📌 Ejemplo: tu PC accediendo a un servidor en otro país.

### 🔹 Analogía sencilla

Piensa en un **oficio con una carta**:

- El remitente pone su dirección (IP de origen).
- El destinatario pone la dirección de entrega (IP de destino).
- El cartero (router) **no abre la carta**, solo mira la dirección del sobre.
- Consulta su mapa (tabla de enrutamiento).
- Decide la mejor ruta: “esta carta va en avión a Miami y luego a México”.
- Se la pasa a otro cartero (router intermedio) hasta que llega al destino final.

✅ En resumen:

Los **routers reenvían paquetes** mirando la IP de destino, usando su tabla de enrutamiento para decidir el mejor camino, aplicando NAT si es necesario, y enviando el paquete al **siguiente salto** hasta que llega al destino final.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 13**       | **Siguiente 15**       |
| ------------------ | ------------------ | ---------------------- |
| [🏠](../README.md) | [⏪](./4_13_IP.md) | [⏩](./4_15_Switch.md) |
