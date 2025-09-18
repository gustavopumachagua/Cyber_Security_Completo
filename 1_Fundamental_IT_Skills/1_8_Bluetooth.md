| **Inicio**         | **atrás 7**        | **Siguiente 9**     |
| ------------------ | ------------------ | ------------------- |
| [🏠](../README.md) | [⏪](./1_7_NFC.md) | [⏩](./1_9_WiFi.md) |

---

## **Índice**

| Temario                                                                        |
| ------------------------------------------------------------------------------ |
| [21. Bluetooth en la ciberseguridad](#21-bluetooth-en-la-ciberseguridad)       |
| [22. Todo sobre la seguridad Bluetooth](#22-todo-sobre-la-seguridad-bluetooth) |

---

# **Bluetooth**

## **21. Bluetooth en la ciberseguridad**

![Bluetooth](/img/1_Fundamental_IT_Skills/Bluetooth.webp "Bluetooth")

### ¿Qué es Bluetooth en palabras sencillas?

Bluetooth es una **tecnología inalámbrica de corto alcance** que permite que dos o más dispositivos se conecten entre sí **sin necesidad de cables**.

👉 Ejemplo:

- Conectar tu celular con unos auriculares inalámbricos 🎧.
- Usar un control de videojuegos en la laptop 🎮.
- Pasar fotos entre dos celulares sin internet.

### ¿Cómo funciona Bluetooth?

Funciona mediante **ondas de radio de 2.4 GHz**.

- Un dispositivo busca otros cercanos con Bluetooth encendido.
- Ambos se **emparejan** (con PIN o confirmación).
- Una vez conectados, pueden **intercambiar datos** (audio, texto, comandos).

👉 Ejemplo:

Tu **mouse Bluetooth** manda en tiempo real la posición y clics a tu **laptop** sin cables.

### ¿Cuál es la diferencia entre Wi-Fi y Bluetooth?

| Característica     | **Bluetooth**                                                   | **Wi-Fi**                                                        |
| ------------------ | --------------------------------------------------------------- | ---------------------------------------------------------------- |
| Alcance            | Corto (10-100 m según versión)                                  | Medio/Largo (hasta 100 m o más)                                  |
| Velocidad          | Moderada (2 Mbps a 50 Mbps)                                     | Mucho más rápida (hasta Gbps)                                    |
| Uso principal      | Conectar dispositivos cercanos (auriculares, teclados, relojes) | Conectarse a internet, compartir archivos grandes, redes locales |
| Consumo de energía | Muy bajo ⚡                                                     | Más alto ⚡⚡⚡                                                  |

👉 En resumen: **Bluetooth conecta dispositivos entre sí**, mientras que **Wi-Fi conecta dispositivos a una red o internet**.

### ¿Bluetooth usa Wi-Fi?

No 🚫.

Bluetooth y Wi-Fi usan la misma **banda de 2.4 GHz**, pero son **tecnologías diferentes**.

- Bluetooth = conexiones personales de corto alcance.
- Wi-Fi = conexión a internet/red de alta velocidad.

### Beneficios del Bluetooth

✅ Sin cables.

✅ Fácil de usar y configurar.

✅ Consume poca batería (ideal en auriculares, relojes, IoT).

✅ Compatible con muchos dispositivos.

✅ Seguro si se configura correctamente.

### Desafíos y limitaciones del Bluetooth

❌ Alcance corto (no más de 100 m).

❌ Velocidad limitada (no sirve para transferir películas grandes).

❌ Puede interferir con otros dispositivos en 2.4 GHz.

❌ Riesgo de ciberataques si no está bien configurado.

### ¿Qué es un ataque Bluetooth en ciberseguridad?

Es cuando un hacker aprovecha fallas en Bluetooth para espiar, robar datos o controlar un dispositivo.

Ejemplos de ataques:

- **Bluejacking**: envío de mensajes no deseados a tu dispositivo.
- **Bluesnarfing**: robo de datos como contactos o fotos.
- **Bluebugging**: el atacante toma control del dispositivo (llamadas, mensajes).

### Bluetooth en IoT y hogares inteligentes

Bluetooth se usa mucho en **dispositivos inteligentes**:

- Cerraduras inteligentes 🔑.
- Luces que controlas con el celular 💡.
- Sensores de temperatura 🌡️.
- Relojes y pulseras deportivas ⌚.

👉 Ejemplo: llegas a casa, tu celular se conecta al parlante Bluetooth 🎶 y empieza la música automáticamente.

### Posibles vulnerabilidades y riesgos de Bluetooth

⚠️ Interceptación de datos.

⚠️ Conexiones no autorizadas.

⚠️ Malware que se transmite vía Bluetooth.

⚠️ Dispositivos siempre visibles (si no se desactiva el modo “descubrible”).

### Signos de ataques de Bluetooth

🚩 Tu dispositivo intenta emparejarse con otros que no conoces.

🚩 Aparecen mensajes extraños o llamadas no realizadas por ti.

🚩 La batería se consume rápido sin razón.

🚩 El Bluetooth se enciende solo.

### Cómo prevenir ataques de Bluetooth

✅ Mantén Bluetooth apagado cuando no lo uses.

✅ Empareja solo con dispositivos de confianza.

✅ Usa contraseñas o PIN fuertes.

✅ Instala actualizaciones de seguridad.

✅ Evita usar Bluetooth en lugares públicos si no es necesario.

### Cómo configurar Bluetooth de forma segura

1. Enciende Bluetooth solo cuando lo necesites.
2. Activa el modo **no visible** (oculto) después de emparejar.
3. Usa siempre autenticación (PIN o clave).
4. Actualiza tu celular, tablet o laptop.
5. Borra dispositivos que ya no uses de la lista.

### Tendencias futuras del Bluetooth

- **Bluetooth 5.3 y 6.0** → mayor alcance, velocidad y seguridad.
- **Bluetooth LE Audio** → sonido de mejor calidad con menos batería.
- **Auriculares multipunto** → conectar un mismo auricular a varios dispositivos.
- **IoT masivo** → más dispositivos en casas y ciudades inteligentes.

👉 Ejemplo futuro:

Podrás entrar a tu casa y que **el celular se conecte automáticamente** con luces, parlantes, cerradura y aire acondicionado, todo con Bluetooth.

✅ **En resumen:**

Bluetooth es un **cable invisible de corto alcance** que conecta dispositivos cercanos.

Es muy útil (auriculares, IoT, relojes, autos), pero hay que configurarlo bien para evitar ataques de seguridad.

---

[🔼](#índice)

---

## **22. Todo sobre la seguridad Bluetooth**

### 🔐 Seguridad en Bluetooth: Lo que debes saber

Bluetooth es muy útil, pero también puede ser un **punto débil en ciberseguridad** si no se configura bien.

Como transmite datos por el aire, es vulnerable a **escuchas, ataques y accesos no autorizados**.

### Riesgos de seguridad en Bluetooth

1. **Intercepción de datos (Eavesdropping)**

   - Un atacante puede escuchar la comunicación entre dos dispositivos.

     👉 Ejemplo: alguien intercepta lo que envías desde tu celular a tu smartwatch.

2. **Conexiones no autorizadas**

   - Un hacker se empareja con tu dispositivo sin permiso.

     👉 Ejemplo: tu celular se conecta a un parlante desconocido en la calle.

3. **Ataques conocidos en Bluetooth**

   - **Bluejacking**: te envían mensajes o notificaciones falsas.
   - **Bluesnarfing**: roban información como fotos, contactos o mensajes.
   - **Bluebugging**: el atacante toma control total del dispositivo (llamadas, mensajes, etc.).
   - **Denegación de servicio (DoS)**: saturan tu conexión Bluetooth hasta que deje de funcionar.

### Signos de que tu Bluetooth puede estar comprometido

🚩 El Bluetooth se enciende solo.

🚩 Tu celular intenta emparejarse con dispositivos extraños.

🚩 Aparecen llamadas o mensajes que no enviaste.

🚩 La batería dura mucho menos sin razón.

🚩 Escuchas ruidos raros en auriculares o manos libres.

### Cómo proteger tu Bluetooth

✅ **Desactívalo** cuando no lo uses.

✅ Activa el modo **“no visible”** después de emparejar.

✅ Empareja solo con dispositivos de confianza.

✅ Usa **PIN o contraseñas fuertes** en los emparejamientos.

✅ Mantén tus dispositivos actualizados con los **últimos parches de seguridad**.

✅ Elimina de la lista los dispositivos viejos o desconocidos.

✅ Evita emparejar en lugares públicos (cafeterías, aeropuertos, transporte).

👉 Ejemplo práctico:

Si tienes auriculares Bluetooth, conéctalos solo una vez a tu celular. Luego desactiva el modo visible para que nadie más intente conectarse.

### Seguridad en versiones de Bluetooth

- **Bluetooth 2.0 y anteriores** → Muy inseguros, fáciles de hackear.
- **Bluetooth 4.0** → Mejor seguridad, pero algunas vulnerabilidades aún existen.
- **Bluetooth 5.0 – 5.3** → Mucho más seguros, cifrado avanzado y menos consumo.
- **Bluetooth LE (Low Energy)** → Ideal para IoT, pero a veces con menos seguridad si no está bien configurado.

👉 Lo mejor es **usar dispositivos con Bluetooth 5.0 o superior**.

### Buenas prácticas en diferentes dispositivos

- **Celulares** 📱 → Desactiva “compartir archivos vía Bluetooth” si no lo usas.
- **Laptops/PCs** 💻 → Usa adaptadores y drivers actualizados.
- **IoT (casas inteligentes)** 🏠 → Configura contraseñas fuertes y actualiza firmware.
- **Autos con Bluetooth** 🚗 → Borra dispositivos que no uses, especialmente si vendes el carro.

### Ejemplos de ataques reales con Bluetooth

1. **BlueBorne (2017)** → Una vulnerabilidad que permitió a hackers controlar millones de teléfonos Android e iOS usando solo Bluetooth encendido.
2. **CVE-2020-0022** → Un fallo en Android que permitía a un atacante ejecutar código malicioso cerca de ti sin que aceptes la conexión.

👉 Estos casos muestran la importancia de mantener el **Bluetooth apagado cuando no lo necesitas**.

### Futuro de la seguridad en Bluetooth

- **Mejor cifrado** en nuevas versiones (5.3 y 6.0).
- **Autenticación más fuerte** para IoT y autos inteligentes.
- **Bluetooth LE Audio** → con seguridad mejorada para auriculares y dispositivos multipunto.
- **Detección automática de ataques** en algunos smartphones modernos.

✅ **En resumen:**

Bluetooth es como un **cable invisible** entre tus dispositivos, pero si no lo cuidas, alguien más puede colarse en esa conexión.
Lo más importante para estar seguro es:

1. Apagarlo cuando no lo uses.
2. Usar siempre emparejamientos seguros.
3. Mantener actualizados tus dispositivos.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**        | **Siguiente 9**     |
| ------------------ | ------------------ | ------------------- |
| [🏠](../README.md) | [⏪](./1_7_NFC.md) | [⏩](./1_9_WiFi.md) |
