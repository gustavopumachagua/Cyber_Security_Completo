| **Inicio**         | **atrás 6**                                  | **Siguiente 8**          |
| ------------------ | -------------------------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./1_6_Basics_of_Computer_Networking.md) | [⏩](./1_8_Bluetooth.md) |

---

## **Índice**

| Temario                                                                                                                                                           |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [18. Guía para principiantes sobre NFC](#18-guía-para-principiantes-sobre-nfc)                                                                                    |
| [19. Guía NFC: Todo lo que necesita saber sobre la comunicación de campo cercano](#19-guía-nfc-todo-lo-que-necesita-saber-sobre-la-comunicación-de-campo-cercano) |
| [20. NFC explicado: ¿Qué es NFC? ¿Cómo funciona NFC? Aplicaciones de NFC](#20-nfc-explicado-qué-es-nfc-cómo-funciona-nfc-aplicaciones-de-nfc)                     |

---

# **NFC**

## **18. Guía para principiantes sobre NFC**

![NFC](/img/1_Fundamental_IT_Skills/NFC.jpg "NFC")

### 📌 ¿Qué es NFC (Comunicación de Campo Cercano)?

NFC es una **tecnología inalámbrica de corto alcance** (hasta 10 cm, aunque lo más común es <4 cm) que permite el intercambio de datos entre dispositivos compatibles, como teléfonos, tarjetas inteligentes y terminales de pago.

👉 Ejemplo: cuando acercas tu celular a un **POS de tarjeta** para pagar con Google Pay o Apple Pay, estás usando **NFC**.

### 📌 ¿En qué se diferencia NFC de otras tecnologías inalámbricas?

- **Bluetooth:** funciona a mayor distancia (hasta 10 metros o más) y requiere emparejamiento manual.

  - Ejemplo: conectar audífonos inalámbricos.

- **Wi-Fi:** diseñado para transmitir gran cantidad de datos en redes (internet), a distancias mayores.

  - Ejemplo: ver Netflix desde tu laptop usando WiFi.

- **NFC:** es más **rápido y sencillo** de usar, no requiere emparejamiento ni configuración, pero transmite datos muy pequeños y solo a corta distancia.

  - Ejemplo: abrir una puerta con una tarjeta de acceso.

### 📌 ¿Cómo funciona la comunicación de campo cercano?

NFC se basa en **inducción electromagnética**:

1. Un dispositivo **activo** (ej. un celular o lector) genera un campo electromagnético.
2. Cuando otro dispositivo o etiqueta NFC entra en ese campo, se establece comunicación.
3. Los datos viajan en **radiofrecuencia de 13,56 MHz**.

👉 Ejemplo: al pasar tu tarjeta de transporte por el lector del bus, el lector "alimenta" a la tarjeta (que no tiene batería) y lee la información de saldo.

### 📌 Modos de funcionamiento de NFC

NFC tiene **3 modos principales**:

1. **Modo Lectura/Escritura**

   - Un dispositivo NFC (ej. un celular) **lee o escribe datos** en una etiqueta NFC.
   - Ejemplo: escaneas un póster con un chip NFC y tu celular abre una página web.

2. **Modo Peer-to-Peer (P2P)**

   - Dos dispositivos NFC **intercambian datos directamente**.
   - Ejemplo: compartir una foto entre dos celulares al acercarlos (Android Beam lo hacía).

3. **Modo Emulación de tarjeta**

   - Un celular actúa como una **tarjeta sin contacto**.
   - Ejemplo: usar tu celular en lugar de tu tarjeta bancaria para pagar en una tienda.

### 📌 Comprensión de las etiquetas NFC

Las **etiquetas NFC** son pequeños chips pasivos que **almacenan información** y se pueden programar.

- No tienen batería (se alimentan del campo NFC).
- Se pueden comprar en forma de **stickers** o tarjetas.
- Se programan con apps (ej. NFC Tools).

👉 Ejemplo: puedes pegar una etiqueta NFC en tu mesa de noche para que, al acercar tu celular, automáticamente:

- Active la alarma,
- Ponga el modo “No molestar”,
- Apague Wi-Fi.

### 📌 Ejemplos de comunicaciones de campo cercano (NFC en la vida real)

1. **Pagos móviles** → acercar celular o smartwatch en una tienda (Google Pay, Apple Pay).
2. **Transporte público** → usar tarjeta sin contacto en metro o bus.
3. **Control de accesos** → tarjetas NFC para abrir puertas de hoteles u oficinas.
4. **Interacción con productos** → etiquetas NFC en pósters, pulseras de conciertos, empaques que al escanear muestran info extra.
5. **Automatización en el hogar** → etiquetas NFC programadas para encender luces, activar Wi-Fi, abrir apps específicas.

✅ **Resumen rápido:**

- **NFC = comunicación inalámbrica de corto alcance.**
- Funciona por **inducción electromagnética**.
- Tiene 3 modos: lectura/escritura, peer-to-peer, emulación de tarjeta.
- Útil para **pagos, transporte, accesos, automatización**.

---

[🔼](#índice)

---

## **19. Guía NFC: Todo lo que necesita saber sobre la comunicación de campo cercano**

### ¿Cómo funciona NFC?

NFC funciona gracias a un **campo electromagnético** de corto alcance (menos de 10 cm, normalmente 4 cm).

- Un dispositivo **activo** (ej. un celular o terminal de pago) genera ese campo.
- El otro dispositivo (otro teléfono, tarjeta o etiqueta NFC) entra al rango y se establece la comunicación.
- La información viaja en **radiofrecuencia de 13,56 MHz**.

👉 Ejemplo: cuando pasas tu tarjeta de bus o pagas con tu celular en un POS.

### ¿Cómo funciona NFC en tu teléfono?

En los smartphones modernos, el chip NFC está integrado en la parte trasera.

1. Activar NFC desde **ajustes**.
2. Acercar el celular a otro dispositivo o terminal compatible.
3. Según el modo NFC:

   - Leer datos (ej. abrir un link desde una etiqueta NFC).
   - Pagar (el teléfono actúa como una tarjeta).
   - Compartir información (ej. contactos, fotos).

👉 Ejemplo: pagas en una tienda acercando tu celular → la terminal reconoce tu tarjeta digitalizada.

### La importancia de la tecnología NFC en los smartphones

- **Pagos móviles seguros**: Google Pay, Apple Pay, Samsung Pay.
- **Comodidad**: ya no necesitas cargar todas tus tarjetas físicas.
- **Transporte y accesos**: puedes usar el celular como tarjeta del metro, bus o para entrar al trabajo.
- **Automatización**: con etiquetas NFC puedes programar acciones (ej. poner tu celular en modo “No molestar” al tocar tu mesa de noche).

👉 Hoy en día, un smartphone sin NFC pierde valor en el mercado, porque muchos usuarios lo consideran indispensable.

### Solución de problemas de conectividad NFC

Si NFC no funciona, puede ser por:

1. **NFC desactivado** → revisa en ajustes.
2. **Distancia incorrecta** → asegúrate de acercar el celular a la zona del lector (a veces hay que encontrar el “punto exacto”).
3. **Carcasa o funda gruesa** → puede bloquear la señal.
4. **Dispositivo incompatible** → no todos los terminales o etiquetas usan NFC.
5. **Problemas de software** → reinicia el celular o borra la caché de la app de pagos.

👉 Ejemplo: si tu pago falla en una tienda, prueba a acercar tu celular por la parte trasera superior (donde está el chip).

### Etiquetas NFC: ¿Qué son y cómo funcionan?

Son **chips pasivos** (sin batería) que almacenan información.

- Al acercar un celular NFC, este los activa con su campo electromagnético.
- El teléfono puede leer o ejecutar lo que hay en el chip.

👉 Ejemplo:

- Un sticker NFC en tu puerta que, al tocarlo con el celular, enciende las luces inteligentes de tu casa.
- Un póster de cine con chip NFC que abre el tráiler en YouTube al acercar tu teléfono.

### ¿Es seguro NFC?

Sí, es considerado **muy seguro** porque:

- Solo funciona a corta distancia (<10 cm).
- Usa cifrado en pagos (tokenización → no se transmite tu número real de tarjeta).
- Requiere autenticación (PIN, huella, Face ID) en pagos móviles.

👉 Aun así, siempre es recomendable:

- Tener el **bloqueo de pantalla** activado.
- Usar apps de pago confiables (Google Pay, Apple Pay).

### ¿Cuál es la diferencia entre EMV y NFC?

- **EMV (Europay, Mastercard, Visa):** es el **estándar de seguridad** para tarjetas con chip y contactless (definen cómo se procesan las transacciones).
- **NFC:** es la **tecnología inalámbrica** que hace posible la transmisión de datos de la tarjeta al terminal.

👉 Ejemplo:

- Tu tarjeta física contactless usa **chip EMV** para la seguridad.
- El celular transmite esos datos de forma inalámbrica gracias a **NFC**.

### ¿Por qué debería aceptar NFC?

Si tienes un negocio, aceptar pagos con NFC te da ventajas:

- Rapidez en los pagos (menos tiempo en cola).
- Atraes a clientes que prefieren usar celular o smartwatch.
- Menor contacto físico (más higiénico que pasar la tarjeta).
- Te alineas con la tendencia global de pagos digitales.

👉 Ejemplo: una cafetería que acepta NFC atiende más rápido en horas pico que una que solo recibe efectivo.

### 🔹 ¿Cómo se acepta NFC?

Para aceptar NFC en tu negocio necesitas:

1. **Un terminal de pago compatible** (POS contactless).
2. **Cuenta con proveedor de pagos** (banco o fintech).
3. **Configurar pagos digitales** (activar Google Pay, Apple Pay, tarjetas sin contacto).
4. **Entrenar al personal**: solo hay que pedir al cliente que acerque su tarjeta o celular.

👉 Ejemplo: instalas un POS moderno en tu tienda → ahora tus clientes pagan acercando la tarjeta o celular sin insertar ni deslizar.

✅ **En resumen:**

- NFC es una tecnología de comunicación de corto alcance usada en pagos, accesos y automatización.
- En tu celular sirve para pagar, compartir info o interactuar con etiquetas.
- Es segura, rápida y cada vez más importante en smartphones y negocios.

---

[🔼](#índice)

---

## **20. NFC explicado: ¿Qué es NFC? ¿Cómo funciona NFC? Aplicaciones de NFC**

### ¿Qué es NFC?

**NFC** significa _Near Field Communication_ o **Comunicación de Campo Cercano**.

Es una **tecnología inalámbrica de corto alcance** (menos de 10 cm, normalmente 4 cm) que permite la comunicación entre dos dispositivos acercándolos.

👉 Piensa en NFC como un **“cable invisible”** que conecta tu celular con otro dispositivo o tarjeta, pero solo si están muy cerca.

Ejemplos:

- Pagar en el supermercado con tu celular o smartwatch.
- Pasar tu tarjeta del bus sobre el lector.
- Tocar un póster con etiqueta NFC que abre una web en tu teléfono.

### ¿Cómo funciona NFC?

NFC funciona mediante **ondas de radiofrecuencia (13,56 MHz)**.

- Un dispositivo **activo** (ej. un celular o un terminal de pago) genera un campo electromagnético.
- Otro dispositivo (puede ser **activo** o **pasivo**, como una etiqueta NFC) se comunica cuando entra en ese campo.

Hay **tres modos principales de funcionamiento**:

1. **Modo lector/escritor**:

   - El celular lee información de una **etiqueta NFC**.
   - Ejemplo: acercas tu teléfono a un cartel de un museo y automáticamente se abre la audioguía.

2. **Modo peer-to-peer (entre pares):**

   - Dos dispositivos se comunican entre sí.
   - Ejemplo: compartir un contacto o foto entre dos celulares con NFC.

3. **Modo emulación de tarjeta:**

   - El celular actúa como una tarjeta bancaria o de transporte.
   - Ejemplo: pagar en una tienda acercando el celular al POS.

### Aplicaciones de NFC

Hoy en día, NFC tiene muchísimos usos prácticos:

1. **Pagos móviles (el más popular)**

   - Google Pay, Apple Pay, Samsung Pay.
   - Ejemplo: compras un café y pagas con tu celular en vez de sacar tu tarjeta.

2. **Transporte público y accesos**

   - Tarjetas de metro, buses o tarjetas de ingreso a edificios.
   - Ejemplo: pasas tu celular o tarjeta sobre el torniquete del metro y entras sin efectivo.

3. **Automatización con etiquetas NFC**

   - Programas acciones en tu celular al tocar una etiqueta.
   - Ejemplo: colocas una etiqueta en tu mesa de noche → al tocarla con el celular activa el “modo avión” y alarma.

4. **Intercambio de datos rápido**

   - Compartir contactos, fotos o links entre dispositivos.
   - Ejemplo: acercas dos celulares y se pasa el contacto sin usar WhatsApp.

5. **Salud y deportes**

   - Smartwatches con NFC permiten pagar sin llevar cartera.
   - Ejemplo: vas a correr con tu reloj y compras agua pagando solo con él.

6. **Publicidad interactiva**

   - Pósters, tarjetas de presentación, stands de eventos.
   - Ejemplo: escaneas un póster de cine y se abre el tráiler automáticamente.

✅ **En resumen:**

- **NFC** = comunicación inalámbrica de muy corto alcance.
- **Funciona** acercando dispositivos que intercambian datos por radiofrecuencia.
- **Aplicaciones:** pagos, transporte, accesos, automatización, publicidad, intercambio de datos.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**                                  | **Siguiente 8**          |
| ------------------ | -------------------------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./1_6_Basics_of_Computer_Networking.md) | [⏩](./1_8_Bluetooth.md) |
