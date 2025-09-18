| **Inicio**         | **atrás 14**           | **Siguiente 16**    |
| ------------------ | ---------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_14_Router.md) | [⏩](./4_16_VPN.md) |

---

## **Índice**

| Temario                                                                |
| ---------------------------------------------------------------------- |
| [106. ¿Qué es un conmutador de red?](#106-qué-es-un-conmutador-de-red) |
| [107. ¿Qué es un Switch?](#107-qué-es-un-switch)                       |

# **Switch**

## **106. ¿Qué es un conmutador de red?**

![Switch](/img/4_IP_Terminology/Switch.jpg "Switch")

### 🔹 ¿Qué es un conmutador de red?

Un **conmutador de red** (_network switch_) es un dispositivo que se usa en redes locales (**LAN**) para **conectar varios dispositivos entre sí** (PCs, impresoras, servidores, cámaras IP, etc.) y permitir que intercambien datos de manera eficiente.

📌 Ejemplo:

En una oficina, conectas 10 computadoras al switch.

- Todas pueden comunicarse entre sí sin necesidad de salir a Internet.
- Si una PC quiere imprimir, el switch envía los datos directamente a la impresora conectada.

### 🔹 Diferencia entre conmutador y enrutador

| **Conmutador (Switch)**                                                           | **Enrutador (Router)**                                                   |
| --------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| Conecta **dispositivos dentro de la misma red local (LAN)**.                      | Conecta **redes diferentes** (ejemplo: tu LAN con Internet – WAN).       |
| Trabaja con **direcciones MAC** (identificadores físicos de cada tarjeta de red). | Trabaja con **direcciones IP** (lógicas, que pueden cambiar).            |
| No sabe qué es Internet, solo “entrega datos entre dispositivos locales”.         | Sabe cómo mover datos dentro y fuera de la red, eligiendo la mejor ruta. |
| Ejemplo: conecta PCs en una oficina.                                              | Ejemplo: conecta tu red de casa con Internet.                            |

📌 Ejemplo real:

- Switch = como el **interruptor de luz de tu casa**, que conecta cosas dentro del mismo espacio.
- Router = como el **director de tránsito**, que conecta tu ciudad con otras ciudades.

### 🔹 ¿Qué es un conmutador de la capa 2?

- Opera en la **Capa 2 del modelo OSI (Enlace de datos)**.
- Se encarga de **reenviar tramas** basándose en **direcciones MAC**.
- No entiende direcciones IP.

📌 Ejemplo:

PC A (`MAC 11:22:33`) quiere hablar con PC B (`MAC 44:55:66`).
El switch revisa la dirección MAC y envía los datos directamente al puerto donde está conectada la PC B.

### 🔹 ¿Qué es un conmutador de la capa 3?

- Opera en **Capa 3 (Red)**, igual que los routers.
- Puede entender **direcciones IP** y realizar funciones de **enrutamiento básico**.
- Mezcla funciones de switch y router.

📌 Ejemplo:

En una universidad con varias redes (administración, estudiantes, profesores), un switch capa 3 puede manejar la comunicación entre las distintas redes sin necesidad de un router dedicado.

### 🔹 ¿Qué es un conmutador no gestionado?

- Es un switch **plug-and-play**: solo lo conectas y ya funciona.
- No puedes configurarlo ni monitorearlo.
- Usado en **casas o pequeñas oficinas**.

📌 Ejemplo:

Compras un switch barato de 8 puertos, conectas tus PCs y listo.

### 🔹 ¿Qué es un conmutador gestionado?

- Permite **configuración avanzada** (VLANs, QoS, seguridad, monitoreo de tráfico).
- Usado en **empresas o centros de datos**.
- Accedes a él por **interfaz web, CLI o SNMP**.

📌 Ejemplo:

En una empresa, configuras un switch gestionado para separar la red de contabilidad, ventas y administración en **VLANs** distintas.

### 🔹 Diferencia entre dirección MAC y dirección IP

| **MAC**                                        | **IP**                                                       |
| ---------------------------------------------- | ------------------------------------------------------------ |
| Dirección física única de cada tarjeta de red. | Dirección lógica que identifica a un dispositivo en una red. |
| No cambia (ejemplo: `00:1A:2B:3C:4D:5E`).      | Puede cambiar según la red (ejemplo: `192.168.1.10`).        |
| Usada por switches.                            | Usada por routers.                                           |

📌 Ejemplo:

- Tu laptop tiene una MAC fija como **`3C:52:82:4A:B1:C0`**.
- Si te conectas en casa → tu IP puede ser `192.168.1.5`.
- Si te conectas en un café → tu IP será diferente (`10.0.0.8`).

### 🔹 ¿Cómo conocen los switches las direcciones MAC?

Los switches tienen una **tabla CAM (Content Addressable Memory)** donde guardan qué dirección MAC está en qué puerto.

Proceso:

1. Cuando un dispositivo envía datos, el switch ve la **MAC de origen**.
2. Guarda esa MAC en la tabla CAM junto con el puerto donde llegó.
3. Cuando otro dispositivo responde, el switch ya sabe a qué puerto reenviar los datos.

📌 Ejemplo:

- PC A (puerto 1 → MAC `AA:BB:CC`) manda datos.
- Switch guarda: “MAC `AA:BB:CC` está en el puerto 1”.
- Luego, si alguien le envía algo a esa MAC, el switch lo reenvía directo al puerto 1.

### 🔹 ¿Cómo protege Cloudflare los conmutadores de red?

Cloudflare no protege switches directamente (los switches son dispositivos físicos dentro de redes locales), sino que protege el **tráfico de red a nivel global en Internet**.

Lo que hace es:

- **Filtrar ataques DDoS** antes de que lleguen a la infraestructura de una empresa.
- **Ofrecer firewalls y reglas de acceso** en la nube.
- **Optimizar el enrutamiento del tráfico** para que llegue de forma rápida y segura.

📌 Ejemplo:

Si una empresa sufre un ataque DDoS (millones de conexiones falsas que podrían saturar sus routers y switches), Cloudflare actúa como un **escudo en la nube**, deteniendo el ataque antes de que alcance la red local.

✅ En resumen:

- **Switch** conecta dispositivos en una LAN (usa MAC).
- **Router** conecta redes diferentes (usa IP).
- **Switch capa 2** usa direcciones MAC, **switch capa 3** también entiende IP.
- **Switch gestionado** se configura, **no gestionado** es plug-and-play.
- **MAC = física**, **IP = lógica**.
- Los switches aprenden MAC con una tabla CAM.
- Cloudflare protege **el tráfico en Internet**, evitando que ataques saturen la red detrás de los switches.

---

[🔼](#índice)

---

## **107. ¿Qué es un Switch?**

### 🔹 ¿Qué es un Switch?

Un **Switch** (conmutador de red) es un dispositivo de red que sirve para **conectar varios dispositivos dentro de una misma red local (LAN)** y permitir que se comuniquen entre sí de forma eficiente.

👉 Piensa en él como una **central de distribución inteligente**: recibe datos de un dispositivo y los envía **solo al destinatario correcto**, en lugar de mandarlos a todos.

### 🔹 ¿Cómo funciona un Switch?

Cuando un dispositivo (PC, impresora, cámara, servidor, etc.) se conecta a un switch, este:

1. **Aprende su dirección MAC** (identificador único de la tarjeta de red).
2. Guarda esa información en una **tabla CAM** (Content Addressable Memory).
3. Cuando otro dispositivo quiere enviar datos, el switch revisa esa tabla y reenvía la información **directamente al puerto correcto**, sin molestar a los demás.

### 🔹 Ejemplo sencillo (oficina pequeña)

Imagina una oficina con 4 PCs y una impresora:

- Sin switch → todos los dispositivos estarían conectados de manera desordenada, chocando datos.
- Con switch → cada dispositivo se conecta a un puerto, y el switch se encarga de enviar los datos solo al destinatario.

📌 Caso real:

- La PC de Ana quiere imprimir un documento.
- El switch sabe que la **impresora** está en el puerto 5.
- En lugar de enviar el archivo a todos los dispositivos, lo manda **directo al puerto 5**.

### 🔹 Diferencia con un hub (para entender mejor)

- Un **hub** envía los datos a **todos los puertos**, aunque solo uno lo necesite.
- Un **switch** envía los datos **solo al puerto correcto**, evitando colisiones y ahorrando ancho de banda.

📌 Ejemplo:

Si gritas en una sala (hub), todos escuchan aunque solo hables con una persona.
Si le hablas al oído a la persona (switch), solo ella recibe el mensaje.

### 🔹 Tipos de Switch

1. **Switch no gestionado**

   - Plug-and-play, sin configuración.
   - Usado en casa o pequeñas oficinas.
   - Ejemplo: conectas tu Smart TV, laptop y consola de videojuegos.

2. **Switch gestionado**

   - Permite configuraciones avanzadas: VLANs, QoS, monitoreo, seguridad.
   - Usado en empresas y centros de datos.
   - Ejemplo: separar la red de contabilidad y la de ventas en una empresa.

3. **Switch de Capa 2**

   - Trabaja con direcciones **MAC** (enlace de datos).

4. **Switch de Capa 3**

   - También entiende **IP** (puede enrutar entre diferentes subredes).

### 🔹 Analogía de la vida real

Imagina un **edificio con buzones**:

- Cada departamento tiene un buzón único (MAC).
- El cartero (switch) entrega la carta solo en el buzón correcto.
- No va tocando todas las puertas como haría un hub.

### 🔹 Resumen clave

- Un **switch conecta dispositivos dentro de una red local (LAN)**.
- Usa **direcciones MAC** para enviar datos al destino correcto.
- Es más eficiente que un hub.
- Puede ser **no gestionado (simple)** o **gestionado (avanzado)**.
- Ejemplo en casa: conectar PC, laptop, impresora y consola a internet por cable.
- Ejemplo en empresa: conectar cientos de PCs y servidores, organizando la red.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 14**           | **Siguiente 16**    |
| ------------------ | ---------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_14_Router.md) | [⏩](./4_16_VPN.md) |
