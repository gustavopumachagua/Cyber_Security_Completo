| **Inicio**         | **atrás 19**        | **Siguiente 21**    |
| ------------------ | ------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_19_WAN.md) | [⏩](./4_21_NTP.md) |

---

## **Índice**

| Temario                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------ |
| [117. ¿Qué es una LAN inalámbrica?](#117-qué-es-una-lan-inalámbrica)                                                           |
| [118. Explicación de las redes inalámbricas Cisco CCNA 200-301](#118-explicación-de-las-redes-inalámbricas-cisco-ccna-200-301) |
| [119. Tecnologías inalámbricas](#119-tecnologías-inalámbricas)                                                                 |

# **WLAN**

## **117. ¿Qué es una LAN inalámbrica?**

![WLAN](/img/4_IP_Terminology/WLAN.jpg "WLAN")

### 🔹 ¿Cómo se beneficia una empresa de una WLAN?

1. **Movilidad y flexibilidad**

   Los empleados se conectan desde cualquier lugar de la oficina sin depender de cables.

   📌 Ejemplo: En un hospital, doctores usan tablets conectadas por WLAN para ver historiales médicos mientras caminan entre salas.

2. **Menores costos de cableado**

   No hace falta tirar cables por todo el edificio.

   📌 Ejemplo: En un coworking, cada nuevo cliente se conecta al WiFi sin instalar cables.

3. **Trabajo colaborativo más ágil**

   Los equipos pueden compartir archivos, impresoras y aplicaciones desde cualquier dispositivo.

4. **Escalabilidad**

   Si la empresa crece, se añaden más puntos de acceso sin rehacer toda la infraestructura.

### 🔹 ¿Cómo funciona una WLAN?

- Una WLAN usa **puntos de acceso inalámbricos (AP – Access Points)** para transmitir la señal WiFi.
- Los dispositivos (laptops, smartphones, tablets) se conectan usando ondas de radio.
- El **router o switch central** gestiona la comunicación con la red local y con Internet.

📌 Ejemplo:

En una tienda, los cajeros usan tablets conectadas a la WLAN para procesar pagos y actualizar inventario en tiempo real.

### 🔹 ¿Cómo se crea una WLAN?

1. **Router inalámbrico o Access Point (AP)**
   Se coloca un dispositivo que emite la señal WiFi.
2. **Configuración de SSID y contraseña**
   Se define el nombre de la red y una clave (WPA2/WPA3).
3. **Conexión a Internet o LAN cableada**
   El router/AP conecta la WLAN a la red principal.
4. **Dispositivos clientes**
   Los usuarios se conectan con su laptop o móvil seleccionando el SSID.

📌 Ejemplo:

En tu casa, tu ISP instaló un **router WiFi** → eso creó tu WLAN.

### 🔹 ¿Es segura una WLAN?

Depende de la configuración:

- **Segura** si usa cifrado fuerte (WPA3 o al menos WPA2), contraseñas robustas y firewall.
- **Insegura** si está abierta (sin clave) o usa protocolos antiguos como WEP.

📌 Ejemplo:

- **Segura** → Red de un banco con WPA3 y autenticación por certificado.
- **Insegura** → WiFi abierto en una cafetería, donde cualquiera puede interceptar datos.

### 🔹 ¿Cómo funciona el roaming en una WLAN?

- El **roaming** permite que un dispositivo se mueva dentro de un edificio sin perder conexión WiFi.
- Se logra colocando **múltiples puntos de acceso (APs)** configurados con el mismo SSID y autenticación.
- El dispositivo “salta” de un AP a otro de forma automática.

📌 Ejemplo:

En un campus universitario, los estudiantes caminan de la biblioteca al comedor y siguen conectados al mismo WiFi sin interrupciones.

### 🔹 ¿Qué es una red en malla (Mesh Network)?

- Una **red en malla** es un tipo de WLAN donde **cada nodo (AP o router) se conecta con los demás**.
- No depende solo de un router principal → si un AP falla, la red busca otra ruta.

📌 Ejemplo:

En un hotel, cada piso tiene un AP y todos forman una red en malla, garantizando cobertura sin zonas muertas.

### 🔹 Arquitectura WLAN

1. **Infraestructura básica**: Router + AP → clientes se conectan.
2. **Distribuida**: Varios AP conectados entre sí para ampliar cobertura.
3. **En malla (Mesh)**: Todos los AP se comunican entre sí y reparten la señal.

### 🔹 Beneficios de una WLAN

- Movilidad y flexibilidad 📱💻
- Menor costo de instalación 💰
- Fácil escalabilidad 📈
- Acceso compartido a recursos (impresoras, servidores, Internet)
- Soporte para **BYOD (Bring Your Own Device)** → empleados usan sus propios móviles.

📌 Ejemplo final:

Una empresa de delivery usa una **WLAN en malla** en su almacén. Los trabajadores escanean productos con dispositivos móviles conectados por WiFi, y la base de datos se actualiza en tiempo real sin necesidad de cables.

---

[🔼](#índice)

---

## **118. Explicación de las redes inalámbricas Cisco CCNA 200-301**

En el examen CCNA 200-301, el tema de redes inalámbricas es fundamental porque hoy casi todas las organizaciones usan **LAN inalámbricas (WLAN)** para dar conectividad. Cisco se enfoca en explicar conceptos básicos, arquitectura y seguridad.

### 🔹 1. ¿Qué es una WLAN?

Una **WLAN (Wireless Local Area Network)** es una **red LAN que usa señales de radio (Wi-Fi)** en lugar de cables Ethernet.

- Se basa en el estándar **IEEE 802.11**.
- Permite que laptops, smartphones, impresoras y otros dispositivos se conecten sin cables.

📌 **Ejemplo real:**

En una oficina de Lima, los empleados se conectan a la red "Oficina_2025" vía Wi-Fi en lugar de usar cables Ethernet.

### 🔹 2. Componentes principales de una red inalámbrica

Según el CCNA, en una WLAN encontramos:

1. **Access Point (AP):**

   Dispositivo que **conecta la red inalámbrica con la red cableada**.

   - Puede ser **autónomo** (funciona por sí solo) o **controlado** por un **Wireless LAN Controller (WLC)**.

2. **Wireless LAN Controller (WLC):**

   Centraliza la gestión de múltiples APs.

   - Configura SSID, seguridad, canales, potencia, roaming.

3. **Clientes inalámbricos:**

   Dispositivos que se conectan al AP: laptops, smartphones, IoT.

📌 **Ejemplo real (Cisco):**

Un hospital en Cusco usa **20 APs** gestionados por un **WLC** para dar Wi-Fi en todas las áreas.

### 🔹 3. Arquitectura inalámbrica

En el CCNA se estudian dos modelos principales:

- **Autonomous AP:**

  Cada AP se configura manualmente (independiente).

  👉 Útil en casas pequeñas o negocios chicos.

- **Lightweight AP + WLC (arquitectura centralizada):**

  El AP depende del WLC para configurarse.

  👉 Ideal en universidades, aeropuertos, hospitales.

📌 Ejemplo:

- Tu router Wi-Fi en casa = **Autonomous AP**.
- El aeropuerto Jorge Chávez con cientos de APs = **WLC centralizado**.

### 🔹 4. SSID y BSS

- **SSID (Service Set Identifier):** Nombre de la red Wi-Fi (ejemplo: "Cisco_Training").
- **BSS (Basic Service Set):** Conjunto de dispositivos conectados a un AP.
- **ESS (Extended Service Set):** Varios BSS conectados (ej. red Wi-Fi en todo un campus).

📌 Ejemplo:

- En tu casa → SSID: "Casa_Gustavo" (un BSS).
- En la Universidad → varios AP con el mismo SSID "UNMSM_WiFi", formando un ESS.

### 🔹 5. Seguridad inalámbrica

El CCNA enfatiza la seguridad en WLAN:

- **WEP (obsoleto, inseguro).**
- **WPA y WPA2 (más seguros, con cifrado AES).**
- **WPA3 (el estándar más moderno).**

📌 Ejemplo:

Una cafetería que ofrece Wi-Fi abierto sin contraseña usa **red insegura** (como si fuera WEP).

En cambio, tu router de Movistar con **WPA2-Personal** es mucho más seguro.

### 🔹 6. Roaming inalámbrico

El **roaming** permite que un cliente (ej. tu celular) cambie de un AP a otro **sin perder la conexión**.

- Se usa en ESS con múltiples APs.

📌 Ejemplo:

En una universidad, caminas de la biblioteca al aula y tu laptop sigue conectada porque pasa del **AP de la biblioteca** al **AP del aula** automáticamente.

### 🔹 7. Canales y frecuencias

- Bandas comunes: **2.4 GHz** y **5 GHz**.
- Problema: **interferencia** si varios AP usan el mismo canal.
- Solución: Planificación de canales (ej. AP1 en canal 1, AP2 en canal 6, AP3 en canal 11).

📌 Ejemplo:

En un edificio de oficinas, si todos los AP usan el mismo canal, habrá lentitud. Con buena planificación, se evitan interferencias.

### 🔹 8. Beneficios de una WLAN (según CCNA)

- ✅ Movilidad (no dependes de un cable).
- ✅ Escalabilidad (fácil agregar más usuarios).
- ✅ Flexibilidad (dispositivos móviles, IoT).
- ✅ Ahorro de costos en cableado.

📌 Ejemplo:

Un call center en Lima puede pasar de **50 a 100 empleados** solo agregando más APs, sin necesidad de tender cables nuevos.

### 🔑 Resumen simplificado (al estilo CCNA)

- Una **WLAN** = LAN pero inalámbrica, basada en **802.11 (Wi-Fi)**.
- Usa **APs y WLCs** para conectarse a la red cableada.
- Se organiza en **SSID, BSS y ESS**.
- Requiere **seguridad (WPA2/WPA3)**.
- Soporta **roaming** para movilidad.
- Se deben planificar **canales/frecuencias** para evitar interferencias.

---

[🔼](#índice)

---

## **119. Tecnologías inalámbricas**

Las **tecnologías inalámbricas** son aquellas que permiten la **transmisión de datos sin necesidad de cables**, utilizando el espectro electromagnético (ondas de radio, microondas, infrarrojos, etc.).

Hoy en día son clave en **redes de computadoras, telecomunicaciones, IoT, seguridad y movilidad**.

### 🔹 1. Tipos principales de tecnologías inalámbricas

#### 1.1. **Wi-Fi (IEEE 802.11)**

- Es la **más común en redes locales (WLAN)**.
- Permite conectar laptops, smartphones, impresoras y otros dispositivos.
- Funciona en bandas de **2.4 GHz y 5 GHz** (y ahora 6 GHz con Wi-Fi 6E).

📌 **Ejemplo:**

En tu casa, el router de Movistar crea un Wi-Fi con SSID "Casa_Gustavo". Tu laptop y tu celular se conectan sin cables.

#### 1.2. **Bluetooth (IEEE 802.15.1)**

- Diseñado para **conexiones de corto alcance (10 m a 100 m)**.
- Bajo consumo de energía.
- Usado en auriculares, relojes inteligentes, teclados, ratones, IoT.

📌 **Ejemplo:**

Tus audífonos Bluetooth se conectan al celular para escuchar música sin cables.

#### 1.3. **Redes celulares (2G, 3G, 4G, 5G)**

- Se basan en **antenas de telecomunicaciones** y permiten **conexión a Internet y llamadas**.
- **5G** mejora la velocidad (Gbps), baja latencia y soporta millones de dispositivos IoT.

📌 **Ejemplo:**

Cuando sales de casa y no tienes Wi-Fi, tu celular cambia a **4G/5G** para seguir en Internet.

#### 1.4. **NFC (Near Field Communication)**

- Tecnología de **muy corto alcance (menos de 10 cm)**.
- Usada en **pagos móviles (Google Pay, Yape NFC), tarjetas de transporte, llaves electrónicas**.

📌 **Ejemplo:**

Pagas un café en Starbucks acercando tu celular al POS con NFC.

#### 1.5. **Infrarrojo (IR)**

- Transmite datos mediante luz infrarroja.
- Requiere **línea de visión directa**.
- Hoy en día casi en desuso, pero se usa en **controles remotos**.

📌 **Ejemplo:**

El control remoto de tu televisor envía señales infrarrojas al TV.

#### 1.6. **Satélite**

- Comunicación vía satélites en órbita.
- Permite **conexión global**, incluso en lugares sin cableado ni antenas.
- Útil para zonas rurales, barcos, aviones.

📌 **Ejemplo:**

**Starlink** de Elon Musk ofrece Internet vía satélite en áreas rurales de Perú.

#### 1.7. **WiMAX (Worldwide Interoperability for Microwave Access – IEEE 802.16)**

- Tecnología de **banda ancha inalámbrica**.
- Mayor cobertura que Wi-Fi, pero menos popular hoy (muchos países migraron a 4G/5G).

📌 **Ejemplo:**

En algunas zonas rurales, se usó WiMAX para dar Internet antes de que llegara el 4G.

#### 1.8. **Zigbee / Z-Wave (IoT)**

- Tecnologías inalámbricas de **bajo consumo** y **alcance limitado**.
- Usadas en **casas inteligentes** (domótica): luces, cámaras, sensores.

📌 **Ejemplo:**

Un sensor Zigbee en tu casa detecta movimiento y avisa al sistema de alarma.

### 🔹 2. Beneficios de las tecnologías inalámbricas

- ✅ Movilidad: puedes moverte sin perder conexión.
- ✅ Escalabilidad: fácil conectar más dispositivos.
- ✅ Ahorro: menos cables y menor infraestructura.
- ✅ Flexibilidad: conectan desde casas hasta satélites.

📌 **Ejemplo global:**

En una empresa en Lima, los empleados usan **Wi-Fi** en la oficina, **VPN móvil 4G** en la calle y **Bluetooth** en sus dispositivos. Todo integrado.

### 🔹 3. Retos y seguridad en tecnologías inalámbricas

- 🔓 **Interferencia:** múltiples señales pueden chocar (ej. varios Wi-Fi en el mismo canal).
- 🔓 **Alcance limitado:** no todas llegan lejos (Bluetooth vs Wi-Fi vs Satélite).
- 🔓 **Seguridad:** redes abiertas son vulnerables a hackers.
- 🔓 **Consumo de energía:** afecta a dispositivos móviles.

📌 **Ejemplo de riesgo:**

Si un café ofrece Wi-Fi abierto sin contraseña, un atacante puede hacer un **ataque Man-in-the-Middle** y robar contraseñas.

### 🔑 Resumen final

Las **tecnologías inalámbricas** abarcan múltiples campos:

- **Wi-Fi** (redes locales).
- **Bluetooth** (corto alcance).
- **4G/5G** (red celular).
- **NFC** (pagos y accesos).
- **Satélite y WiMAX** (conectividad global).
- **Zigbee/Z-Wave** (IoT).

Todas permiten **conectividad sin cables**, cada una con **ventajas, usos y limitaciones**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 19**        | **Siguiente 21**    |
| ------------------ | ------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_19_WAN.md) | [⏩](./4_21_NTP.md) |
