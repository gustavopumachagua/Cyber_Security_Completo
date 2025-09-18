| **Inicio**         | **atrás 2**                                 | **Siguiente 4**                               |
| ------------------ | ------------------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./1_2_Computer_Hardware_Components.md) | [⏩](./1_4_OS_Independent_Troubleshooting.md) |

---

## **Índice**

| Temario                                                            |
| ------------------------------------------------------------------ |
| [6. ¿Qué es Ethernet?](#6-qué-es-ethernet)                         |
| [7. ¿Qué es WiFi y cómo funciona?](#7-qué-es-wifi-y-cómo-funciona) |
| [8. ¿Cómo funciona el bluetooth?](#8-cómo-funciona-el-bluetooth)   |
| [9. Cómo funciona el Bluetooth](#9-cómo-funciona-el-bluetooth)     |

---

# **Tipos de conexión y su función**

## **6. ¿Qué es Ethernet?**

![Ethernet](/img/1_Fundamental_IT_Skills/ethernet.jpg "Ethernet")

### ¿Qué es Ethernet?

Ethernet es una **tecnología de red por cable** que permite conectar computadoras, servidores, impresoras, routers y otros dispositivos para que se comuniquen entre sí y compartan datos.

📌 **Ejemplo:**

Imagina que una oficina tiene varias computadoras. Para que puedan compartir archivos e impresoras, se conectan todas a través de **cables Ethernet** a un switch o router.

### ¿Por qué se utiliza Ethernet?

Se utiliza porque ofrece una **conexión rápida, estable y segura**.
Aunque hoy en día muchos usamos **Wi-Fi**, Ethernet sigue siendo preferido en lugares donde la estabilidad y la velocidad son críticas (oficinas, gaming, servidores).

📌 **Ejemplo:**

Un gamer conecta su PC por Ethernet para evitar que el Wi-Fi cause retrasos en los juegos online.

### Ventajas de Ethernet

1. **Alta velocidad** → versiones modernas (Gigabit o 10GbE) son mucho más rápidas que la mayoría de Wi-Fi.

2. **Estabilidad** → no sufre interferencias como el Wi-Fi (paredes, microondas, otras redes).

3. **Baja latencia** → ideal para videollamadas, juegos y transmisiones en vivo.

4. **Seguridad** → más difícil de interceptar que una señal Wi-Fi.

📌 Ejemplo: En una videollamada de trabajo, con Ethernet casi no hay cortes ni retrasos, mientras que en Wi-Fi puede haber congelamientos.

### Desventajas de Ethernet

1. **Movilidad limitada** → estás atado al cable, no puedes moverte libremente.

2. **Instalación** → en oficinas grandes se necesita tender muchos cables.

3. **Estética** → puede ser incómodo o feo tener cables por toda la casa.

4. **Costo en grandes redes** → switches, routers y cableado largo aumentan el gasto.

📌 Ejemplo: Con Wi-Fi puedes usar tu laptop en la sala, cocina o dormitorio. Con Ethernet tendrías que mover el cable o tener múltiples conexiones.

### Ethernet frente a Wi-Fi

| Característica  | Ethernet (Cable)        | Wi-Fi (Inalámbrico)                              |
| --------------- | ----------------------- | ------------------------------------------------ |
| **Velocidad**   | Muy alta y constante    | Buena, pero varía según distancia/interferencias |
| **Estabilidad** | Muy estable             | Puede fallar o perder señal                      |
| **Latencia**    | Muy baja (ideal gaming) | Más alta (puede generar lag)                     |
| **Seguridad**   | Más seguro              | Puede ser hackeado si no se configura bien       |
| **Movilidad**   | Limitada (cable)        | Libre, puedes moverte                            |

📌 Resumen: **Ethernet = rendimiento y fiabilidad**, **Wi-Fi = comodidad y movilidad**.

### Cómo funciona Ethernet

1. Los dispositivos (PC, router, switch) se conectan con cables Ethernet.
2. Cada dispositivo tiene una **dirección MAC** única para identificarse.
3. Los datos se dividen en **paquetes** y viajan por el cable como señales eléctricas.
4. El switch o router dirige esos paquetes al destino correcto.

📌 Ejemplo:
Tu PC envía un archivo a la impresora → el switch lo recibe → lo entrega solo a la impresora, no al resto de dispositivos.

### Tipos de cables Ethernet

Los cables Ethernet se clasifican en **categorías (Cat)** según su velocidad y ancho de banda.

- **Cat 5e** → hasta 1 Gbps (hogares, oficinas pequeñas).
- **Cat 6** → hasta 10 Gbps a corta distancia (uso común actual).
- **Cat 6a** → mejorado, hasta 10 Gbps en distancias largas.
- **Cat 7 / Cat 8** → muy rápidos, hasta 40 Gbps (centros de datos, servidores).

📌 Ejemplo:
En casa, un cable **Cat 6** es más que suficiente para tener internet rápido. En un **centro de datos**, usan Cat 7 u 8 para manejar mucho tráfico.

✅ En resumen:

- **Ethernet** = conexión por cable, rápida, segura y estable.
- Se usa en oficinas, servidores y gaming.
- Tiene ventajas claras sobre Wi-Fi en velocidad y estabilidad, pero limita movilidad.
- Tipos de cables van de Cat 5e (básico) hasta Cat 8 (ultra rápido).

---

[🔼](#índice)

---

## **7. ¿Qué es WiFi y cómo funciona?**

![WiFi](/img/1_Fundamental_IT_Skills/wifi.webp "WiFi")

### ¿Qué es Wi-Fi?

Wi-Fi es una tecnología de **red inalámbrica** que permite conectar dispositivos (computadoras, celulares, tablets, Smart TVs, etc.) a internet o entre ellos **sin necesidad de cables**.

- Usa ondas de radio para transmitir datos.
- Funciona en frecuencias de **2.4 GHz** (más alcance, menos velocidad) y **5 GHz** (más velocidad, menos alcance).

📌 **Ejemplo:** cuando te conectas al internet de tu casa con tu celular sin usar cables, estás usando Wi-Fi.

### Puntos de acceso Wi-Fi

Un **punto de acceso (Access Point)** es un dispositivo que crea una red Wi-Fi para que otros equipos se conecten.

- Puede ser un router Wi-Fi, un repetidor, o un equipo dedicado en oficinas.
- Amplía la señal y permite que más dispositivos se conecten.

📌 **Ejemplo:** en una cafetería, el módem del local actúa como punto de acceso para que los clientes se conecten al Wi-Fi gratis.

### 🔹 Construyendo una red inalámbrica

Para tener una red Wi-Fi en casa u oficina necesitas:

1. **Proveedor de Internet (ISP)** → te da el servicio (Movistar, Claro, etc.).
2. **Módem/Router Wi-Fi** → recibe internet del ISP y crea la señal inalámbrica.
3. (Opcional) **Extensores o repetidores Wi-Fi** → amplían la cobertura en áreas con poca señal.

📌 **Ejemplo:**

En tu casa el router está en la sala → en el cuarto la señal llega débil → colocas un repetidor para mejorar la conexión.

### ¿Qué hace un enrutador Wi-Fi?

El **router Wi-Fi** es el corazón de la red.

- Crea la señal inalámbrica.
- Asigna direcciones IP a cada dispositivo conectado.
- Envía datos entre tu dispositivo e internet.
- Incluye seguridad (contraseñas, firewall, filtros).

📌 **Ejemplo:** cuando entras a YouTube desde tu celular, el router toma tu solicitud, la envía a internet y devuelve el video a tu dispositivo.

### ¿Cómo puedo probar la velocidad de mi Wi-Fi?

1. Conéctate a tu Wi-Fi.
2. Entra a una página como **speedtest.net** o usa la app **FAST.com** de Netflix.
3. Revisa tres valores:

   - **Velocidad de descarga (Download):** qué tan rápido bajas datos (ej. videos).
   - **Velocidad de subida (Upload):** qué tan rápido envías datos (ej. subir fotos).
   - **Ping/Latencia:** qué tan rápido responde la red (importante en juegos y videollamadas).

📌 **Ejemplo:** si tu plan es 100 Mbps y la prueba marca 90 Mbps de descarga → tu Wi-Fi va bien.

### Diferencia entre Google Nest WiFi y Google WiFi

- **Google WiFi (2016):** sistema de malla (mesh) que usa varios puntos para dar cobertura en toda la casa.
- **Google Nest WiFi (2019):** versión más moderna, más rápida y con funciones extra como altavoz inteligente (Google Assistant).

📌 **Ejemplo:**

Si tu casa tiene 2 pisos, puedes usar **Google Nest WiFi** para que cada habitación tenga buena señal, y además pedirle con la voz que ponga música o te diga el clima.

### ¿Pueden hackear mi Wi-Fi?

Sí, es posible si tu red no está bien protegida. Los atacantes pueden:

- Robar tu contraseña.
- Espiar tu tráfico (qué páginas visitas).
- Usar tu internet para actividades ilegales.

🔒 **Cómo protegerte:**

- Usa **WPA3 o WPA2** (cifrado seguro).
- Pon una **contraseña fuerte** (mezcla de letras, números y símbolos).
- Cambia la contraseña de fábrica del router.
- Mantén el firmware del router actualizado.

📌 **Ejemplo:** si tu contraseña es “123456”, alguien podría adivinarla y usar tu red gratis. Si usas algo como “Gus2025#Net!”, es mucho más difícil de hackear.

✅ **Resumen rápido:**

- **Wi-Fi** = red inalámbrica para conectarse a internet sin cables.
- **Puntos de acceso** crean la señal.
- **Router Wi-Fi** distribuye internet y gestiona la red.
- Puedes medir la velocidad con Speedtest o FAST.
- **Google Nest WiFi** = versión más moderna que Google WiFi, con funciones de asistente.
- Sí, tu Wi-Fi puede ser hackeado, pero con buena seguridad reduces el riesgo.

---

[🔼](#índice)

---

## **8. ¿Cómo funciona el bluetooth?**

![Bluetooth](/img/1_Fundamental_IT_Skills/Bluetooth.webp "Bluetootht")

### ¿Cómo funciona Bluetooth?

Bluetooth es una tecnología de **comunicación inalámbrica de corto alcance** que permite que dos dispositivos se conecten directamente **sin cables**.

- Usa ondas de radio en la banda de **2.4 GHz**.
- Crea una pequeña red llamada **piconet** (1 dispositivo principal + varios secundarios).

📌 **Ejemplo:** conectar tu celular a unos audífonos Bluetooth para escuchar música sin cables.

### Conexiones Bluetooth

- **Punto a punto:** entre dos dispositivos (celular ↔ auriculares).
- **Multipunto:** un dispositivo se conecta a varios (ej. un celular a un smartwatch y auriculares al mismo tiempo).

📌 **Ejemplo:** en tu carro puedes tener el celular conectado al Bluetooth del auto y al mismo tiempo a tus audífonos.

### Cómo funciona la tecnología Bluetooth

1. El dispositivo **busca otros cercanos** con Bluetooth encendido.
2. Se envía una solicitud de conexión.
3. Ambos aceptan y crean un enlace seguro (emparejamiento).
4. Intercambian datos en forma de señales de radio.

📌 **Ejemplo:** cuando conectas tu celular a un parlante Bluetooth, primero lo detecta, lo emparejas, y luego se transmite la música como datos inalámbricos.

### Alcance de Bluetooth

Depende de la versión y potencia:

- **Clase 1:** hasta **100 m** (usado en equipos profesionales).
- **Clase 2:** hasta **10 m** (lo más común, celulares y audífonos).
- **Clase 3:** hasta **1 m** (corto alcance, casi en desuso).

📌 **Ejemplo:** si te alejas más de 10 m de tus audífonos Bluetooth, la música empieza a cortarse.

### Seguridad de Bluetooth

Aunque es seguro, puede ser vulnerable si no lo configuras bien.

- Usa cifrado y emparejamiento para proteger datos.
- Riesgos: _bluejacking_ (mensajes no deseados), _bluesnarfing_ (robo de datos), _bluebugging_ (control del dispositivo).

🔒 **Buenas prácticas:**

- Desactiva el Bluetooth si no lo usas.
- No aceptes conexiones de desconocidos.
- Usa siempre contraseñas o PIN.

📌 **Ejemplo:** si dejas tu Bluetooth abierto en un lugar público, alguien podría intentar conectarse a tu celular.

### Diferencia entre Wi-Fi y Bluetooth

| Característica         | Wi-Fi                                           | Bluetooth                                |
| ---------------------- | ----------------------------------------------- | ---------------------------------------- |
| **Propósito**          | Internet y redes de alta velocidad              | Conexión directa entre dispositivos      |
| **Alcance**            | 30 m (router casero), hasta 100 m (profesional) | 1 a 100 m según clase                    |
| **Velocidad**          | Mucho más rápida (hasta Gbps)                   | Menos rápida (Mbps)                      |
| **Consumo de energía** | Más alto                                        | Muy bajo (ideal para audífonos, relojes) |

📌 **Ejemplo:**

- Usas **Wi-Fi** para ver Netflix en tu laptop.
- Usas **Bluetooth** para escuchar ese audio en tus audífonos inalámbricos.

### ¿Qué es un controlador Bluetooth?

Es un **software (driver)** que permite que el sistema operativo (Windows, Linux, etc.) reconozca y gestione el hardware Bluetooth.

📌 **Ejemplo:** si tu PC no detecta auriculares Bluetooth, probablemente necesitas instalar o actualizar el **driver Bluetooth**.

### ¿Qué dispositivos utilizan Bluetooth?

Hoy en día, muchísimos:

- Celulares, tablets, laptops.
- Auriculares y parlantes.
- Smartwatches y pulseras fitness.
- Teclados, ratones y controles de videojuegos.
- Automóviles (manos libres).
- Electrodomésticos inteligentes (IoT).

📌 **Ejemplo:** tu **mouse inalámbrico** funciona con Bluetooth para evitar cables en la mesa.

### ¿Quién regula la tecnología Bluetooth?

La regula el **Bluetooth Special Interest Group (SIG)**, una organización creada en 1998 por empresas como **Ericsson, IBM, Intel, Nokia y Toshiba**.

- Ellos definen los estándares y certifican los dispositivos.

📌 **Ejemplo:** gracias al SIG, unos auriculares Sony pueden conectarse a un celular Samsung sin problemas.

### ¿Cuál es la última versión de Bluetooth?

- **Bluetooth 5.4 (2023)** → última versión disponible.

  - Mayor alcance.
  - Menor consumo de energía.
  - Mejor seguridad.
  - Pensado especialmente para dispositivos IoT (Internet de las Cosas).

📌 **Ejemplo:** un smartwatch con **Bluetooth 5.4** dura más días con una sola carga porque gasta menos batería.

### ¿Cómo agregar Bluetooth a una PC?

Si tu PC no tiene Bluetooth integrado, puedes añadirlo:

1. **Adaptador USB Bluetooth:** pequeño dispositivo que se conecta al puerto USB.
2. Instalas el **driver** si es necesario.
3. Activas Bluetooth en la configuración de tu sistema operativo.

📌 **Ejemplo:** compras un **dongle Bluetooth USB** en una tienda, lo conectas a tu PC de escritorio, y ya puedes usar auriculares inalámbricos.

✅ **Resumen final:**

- **Bluetooth** conecta dispositivos de corto alcance sin cables.
- Tiene diferentes alcances y versiones, la más nueva es **5.4**.
- Se usa en audífonos, relojes, autos, teclados, parlantes, etc.
- Lo regula el **Bluetooth SIG**.
- Puedes agregarlo a una PC con un adaptador USB.

---

[🔼](#índice)

---

## **9. Cómo funciona el Bluetooth**

### ¿Cómo funciona el **Bluetooth**?

Bluetooth funciona como un **"cable invisible"** que conecta dos o más dispositivos cercanos mediante **ondas de radio de corto alcance (2.4 GHz)**.

Básicamente hace esto:

1. **Búsqueda:** un dispositivo (ej. tu celular) busca otros con Bluetooth encendido.
2. **Emparejamiento:** se crea un enlace seguro con un PIN o confirmación.
3. **Conexión:** se establece una mini-red llamada **piconet** (un dispositivo principal controla a los demás).
4. **Comunicación:** los datos se transmiten en pequeños paquetes de radio.

### 📌 Ejemplo práctico

Imagina que quieres escuchar música en tus auriculares Bluetooth:

- El **celular** busca dispositivos cercanos.
- Encuentra los **auriculares**.
- Aceptas emparejar → ahora tienen un enlace.
- La **música se convierte en datos** y viaja por ondas de radio de tu celular a los audífonos.
- El audio suena en tus auriculares como si estuvieran conectados con cable. 🎧

### Características clave

- **Corto alcance:** normalmente hasta 10 m (aunque versiones nuevas llegan a 100 m).
- **Bajo consumo de energía:** ideal para baterías pequeñas como las de audífonos o relojes.
- **Velocidad moderada:** suficiente para audio, periféricos, pero no para transmitir películas enteras rápido (para eso se usa WiFi).

### 📌 Otro ejemplo sencillo

- Tu **mouse Bluetooth** envía la posición y clics a la **laptop** en tiempo real.
- No necesitas cables ni un receptor especial, porque ambos dispositivos hablan el mismo “idioma” Bluetooth.

✅ En resumen: **Bluetooth funciona enviando datos mediante ondas de radio de corto alcance, creando un enlace seguro entre dispositivos para intercambiar información sin cables.**

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**                                 | **Siguiente 4**                               |
| ------------------ | ------------------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./1_2_Computer_Hardware_Components.md) | [⏩](./1_4_OS_Independent_Troubleshooting.md) |
