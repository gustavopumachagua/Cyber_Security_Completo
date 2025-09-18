| **Inicio**         | **atrás 20**         | **Siguiente 22**             |
| ------------------ | -------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./12_20_ACL.md) | [⏩](./12_22_Preparation.md) |

---

## **Índice**

| Temario                                                                                                                                          |
| ------------------------------------------------------------------------------------------------------------------------------------------------ |
| [394. ¿Qué es la seguridad Wi-Fi? Diferencias entre WEP, WPA, WPA2 y WPA3](#394-qué-es-la-seguridad-wi-fi-diferencias-entre-wep-wpa-wpa2-y-wpa3) |
| [395. Seguridad WiFi: ¿Qué es WEP, WPA y WPA2?](#395-seguridad-wifi-qué-es-wep-wpa-y-wpa2)                                                       |

# **WPA vs WPA2 vs WPA3 vs WEP**

## **394. ¿Qué es la seguridad Wi-Fi? Diferencias entre WEP, WPA, WPA2 y WPA3**

![WPA vs WPA2 vs WPA3 vs WEP](/img/12_Secure_vs_Unsecure_Protocols/WPA_vs_WPA2_vs_WPA3_vs_%20WEP.png "WPA vs WPA2 vs WPA3 vs WEP")

### 🔹 ¿Qué es la seguridad Wi-Fi?

La **seguridad Wi-Fi** es el conjunto de protocolos, configuraciones y medidas diseñadas para **proteger las redes inalámbricas** contra accesos no autorizados, espionaje de tráfico y ciberataques.
Su objetivo es:

- Garantizar la **confidencialidad** (que los datos viajen cifrados).
- Asegurar la **integridad** (que no sean alterados en tránsito).
- Verificar la **autenticidad** (que los usuarios y el punto de acceso sean legítimos).

👉 Ejemplo: cuando te conectas a una red Wi-Fi con contraseña, esa clave no es solo para conectarte, también forma parte del cifrado de tus comunicaciones.

### 🔹 ¿Cómo funciona la seguridad Wi-Fi?

- Se basa en **protocolos de autenticación y cifrado** entre el router (punto de acceso) y los dispositivos (clientes).
- El router utiliza una clave compartida o credenciales más complejas (según el protocolo) para:

  1. **Autenticar** al cliente (validar que puede conectarse).
  2. **Cifrar la comunicación** (para que no pueda ser leída por terceros).

- Dependiendo del protocolo elegido (WEP, WPA, WPA2, WPA3), el cifrado puede ser más o menos fuerte.

### 🔹 Tipos de protocolos de seguridad inalámbrica

#### 1. **WEP (Wired Equivalent Privacy)**

- 📅 Año: 1997 (primer estándar).
- 🔑 Usa claves de 40 o 104 bits.
- 🚨 Debilidades:

  - Algoritmo de cifrado RC4 roto.
  - Se puede crackear en minutos con herramientas como Aircrack-ng.

- ❌ Considerado inseguro y obsoleto.

Ejemplo: si encuentras un Wi-Fi con seguridad **WEP**, puedes asumir que es vulnerable y no deberías usarlo.

#### 2. **WPA (Wi-Fi Protected Access)**

- 📅 Año: 2003 (mejora temporal de WEP).
- 🔑 Usa **TKIP (Temporal Key Integrity Protocol)** para generar claves dinámicas.
- 🚨 Debilidades:

  - TKIP también presenta vulnerabilidades.
  - Hoy es inseguro para redes modernas.

Ejemplo: un router antiguo que aún usa WPA puede ser hackeado con ataques de diccionario o inyección de paquetes.

#### 3. **WPA2 (Wi-Fi Protected Access II)**

- 📅 Año: 2004 (estándar oficial).
- 🔑 Introduce **AES (Advanced Encryption Standard)** mucho más seguro que TKIP.
- 🔒 Versión más común: **WPA2-PSK (Personal)**, donde todos los usuarios comparten la misma clave.
- 🚨 Debilidad: ataques de **handshake** con diccionario si la contraseña es débil.

Ejemplo: una red WPA2 con clave “12345678” puede ser crackeada en minutos. Pero si usas “XyT!92\$dL\@2024”, sería prácticamente imposible.

#### 4. **WPA3 (Wi-Fi Protected Access III)**

- 📅 Año: 2018 (más reciente).
- 🔑 Introduce **SAE (Simultaneous Authentication of Equals)**, que dificulta ataques de diccionario.
- 🔒 Mejora el cifrado en redes públicas con **OWE (Opportunistic Wireless Encryption)**.
- 🚀 Es la opción recomendada actualmente, aunque no todos los dispositivos lo soportan.

Ejemplo: en una red de cafetería con WPA3-OWE, tu conexión estará cifrada incluso sin contraseña compartida.

### 🔹 Riesgos de las redes Wi-Fi no seguras

- **Intercepción de datos (sniffing)** → un atacante puede capturar tráfico sin cifrado.
- **Ataques Man-in-the-Middle (MITM)** → el atacante se coloca entre tu dispositivo y el router.
- **Puntos de acceso falsos (Evil Twin)** → redes falsas que imitan el nombre de la red legítima.
- **Acceso no autorizado** → intrusos conectados consumen ancho de banda y pueden atacar otros dispositivos de la red.
- **Robo de credenciales** → contraseñas, cookies de sesión o información bancaria.

Ejemplo: conectarte a un Wi-Fi abierto en un aeropuerto podría permitir que alguien capture tu sesión de redes sociales.

### 🔹 Formas de proteger una red Wi-Fi

✅ Usar **WPA3** (o WPA2 si no hay soporte).

✅ Configurar contraseñas largas y complejas (mínimo 12 caracteres, combinando mayúsculas, minúsculas, números y símbolos).

✅ Deshabilitar **WPS (Wi-Fi Protected Setup)**, ya que es vulnerable al ataque por PIN.

✅ Cambiar la **contraseña por defecto** del router (admin/admin es fácil de adivinar).

✅ Actualizar el **firmware del router**.

✅ Segmentar redes → una red para invitados separada de la red principal.

✅ Usar un **firewall** y sistemas de detección de intrusos (IDS).

### 🔹 Soluciones de seguridad Wi-Fi para proteger la red

- **VPN (Virtual Private Network):** cifra el tráfico incluso si la red Wi-Fi es insegura.
- **Firewalls perimetrales y UTM** en empresas.
- **IDS/IPS inalámbricos** (ej. Cisco WIPS) para detectar accesos sospechosos.
- **Autenticación 802.1X con RADIUS** en empresas para control centralizado de usuarios.

#### 🔹 Ataques comunes contra redes Wi-Fi

- **Ataque de diccionario/brute force** contra WPA/WPA2-PSK.
- **Captura de handshake WPA2** y crackeo offline.
- **Evil Twin / Rogue AP** → crear un punto de acceso falso.
- **Deautenticación (ataques DoS)** → desconectar clientes para capturar el handshake.
- **KRACK (Key Reinstallation Attack)** → vulnerabilidad en WPA2 (ya parchada).

### 🔹 Cómo proteger una red Wi-Fi empresarial

1. Implementar **WPA3-Enterprise** con 802.1X y servidor RADIUS.
2. Separar redes: **producción, invitados y BYOD** (Bring Your Own Device).
3. Monitorizar el tráfico inalámbrico (WIDS/WIPS).
4. Políticas de acceso con **RBAC y NAC (Network Access Control)**.
5. Actualizar firmwares y aplicar parches de seguridad regularmente.
6. Educar a los empleados sobre riesgos de conectarse a redes inseguras.

### 🔹 Elimine las conjeturas sobre la seguridad de Wi-Fi

- Usa siempre **WPA3** (o WPA2 si no es posible).
- Nunca uses WEP o WPA.
- Complementa con VPN, firewall y contraseñas fuertes.
- Monitorea accesos y revisa logs del router.
- Si es red empresarial: 802.1X con RADIUS es obligatorio.

👉 En resumen: la seguridad Wi-Fi depende del **protocolo usado** y de las **buenas prácticas de configuración**.

---

[🔼](#índice)

---

## **395. Seguridad WiFi: ¿Qué es WEP, WPA y WPA2?**

- **WEP (Wired Equivalent Privacy)**: estándar antiguo (1997) pensado para dar “privacidad equivalente” a la red cableada. Usa RC4 y vectores de inicialización (IV) cortos. **Inseguro y obsoleto**.
- **WPA (Wi-Fi Protected Access)**: parche intermedio introducido en 2003 para corregir fallos de WEP sin cambiar todo el hardware. Usa **TKIP** (mezcla de claves con RC4) y añade mecanismos de integridad. Mejor que WEP, pero **hoy también inseguro**.
- **WPA2 (Wi-Fi Protected Access II)**: introducido en 2004, reemplaza TKIP por **AES-CCMP** (cifrado moderno y robusto). Es el estándar aceptado durante muchos años; **seguro si se configura bien** (por ejemplo, con claves fuertes o en modo Enterprise).

### Cómo funciona cada uno (a nivel técnico pero claro)

#### WEP — idea y por qué falló

- **Cifrado:** RC4 (flujo) con una **clave estática** (por ejemplo 40 o 104 bits) + **IV de 24 bits** que se transmite en claro.
- **Problema principal:** IV corto (24 bits) provoca **reutilización de claves** en tráfico intenso; además, el algoritmo de generación de claves de RC4 y la forma en que se concatenaba el IV con la clave permiten recuperar la clave con relativamente pocos paquetes capturados.
- **Consecuencia práctica:** con herramientas de auditoría se puede romper WEP en minutos u horas.
- **Ejemplo real:** un punto de acceso doméstico configurado con WEP — cualquier atacante en rango puede capturar tráfico y, con unos pocos miles/millones de paquetes, deducir la clave y acceder a la red.

#### WPA — transición con TKIP

- **Cifrado:** **TKIP** usa RC4 internamente pero introduce:

  - claves por paquete (evita reutilización directa),
  - contador/nonce,
  - MIC (mensaje de integridad) llamado _Michael_ para detectar modificaciones.

- **Objetivo:** permitir que equipos WEP viejos siguieran funcionando con una actualización de firmware.
- **Debilidades:** TKIP fue pensado como solución temporal; más adelante se encontraron vectores de ataque (ej. ataques de inyección y bypass de integridad) que lo hacen inseguro para redes actuales.
- **Ejemplo práctico:** un router antiguo que ofrece "WPA/TKIP" todavía está vulnerable frente a ciertas técnicas modernas; lo recomendable es migrar a AES.

#### WPA2 — AES-CCMP (la mejora real)

- **Cifrado:** **AES** en modo **CCMP** (ofrece confidencialidad, integridad y protección contra repeticiones).
- **Modos de autenticación:**

  - **WPA2-PSK (Personal):** todos usan la misma contraseña (passphrase). Es sencillo para hogares.
  - **WPA2-Enterprise (802.1X + RADIUS):** autenticación individual basada en EAP (certificados, usuario/contraseña, tokens). Recomendado en empresas.

- **Handshake:** se usa un _4-way handshake_ para derivar claves temporales entre cliente y AP.
- **Riesgos reales:** WPA2 es seguro **si**:

  - la contraseña PSK es fuerte (en modo PSK), y
  - el equipo está parcheado (por ejemplo, mitigar KRACK).

- **Ejemplo práctico:** una red doméstica con WPA2-PSK y contraseña `C0sa$egura_2025!` es razonablemente segura; una red empresarial debería usar WPA2-Enterprise con EAP-TLS (certificados).

### Comparación práctica (tabla corta)

| Protocolo       |  Año | Cifrado / método     | Nivel de seguridad (hoy)            | Vulnerabilidades típicas                            |
| --------------- | ---: | -------------------- | ----------------------------------- | --------------------------------------------------- |
| WEP             | 1997 | RC4 + IV 24-bit      | **Inseguro** (no usar)              | Reutilización IV → recuperación de clave            |
| WPA (TKIP)      | 2003 | TKIP (basado en RC4) | **Debilitado** (temporal)           | Ataques de inyección, problemas de MIC              |
| WPA2 (AES-CCMP) | 2004 | AES-CCMP             | **Seguro si está bien configurado** | Ataques offline contra PSK débil; KRACK (parcheado) |

---

### Ejemplos concretos (uso cotidiano)

#### Ejemplo A — Hogar con WEP (malo)

- Router viejo usa **WEP 64-bit**.
- Resultado: cualquier vecino con herramientas puede recuperar la clave y navegar usando tu red, acceder a dispositivos compartidos e interceptar tráfico. **Solución:** actualizar a un router que soporte WPA2/WPA3 y desactivar WEP.

#### Ejemplo B — Hogar con WPA2-PSK (común)

- Router configurado con **WPA2-PSK (AES)** y contraseña `MiCasa2025!`.
- Si la contraseña es **débil** (`12345678`), un atacante que capture el _handshake_ puede realizar un ataque offline por diccionario y obtenerla.
- Si la contraseña es **fuerte** (frase larga y única), la red doméstica es segura para la mayoría de usos.

#### Ejemplo C — Empresa con WPA2-Enterprise (recomendado)

- La compañía usa **WPA2-Enterprise** con servidor RADIUS y **EAP-TLS** (certificados cliente/servidor).
- Cada empleado se autentica individualmente; si un dispositivo se compromete se puede revocar su certificado sin cambiar la red entera.

### Ataques y limitaciones — sin enseñar cómo explotarlos

- **Cracking de WEP:** explotación de IVs repetidos y debilidades de RC4 (rápido y trivial con tráfico suficiente).
- **Ataques sobre WPA/WPA2-PSK:** captura del 4-way handshake seguida de ataque offline por diccionario/rueda si la passphrase es débil.
- **KRACK (2017):** vulnerabilidad en la implementación del 4-way handshake de WPA2 que permitía re-instalar claves; fue parcheada en la mayoría de dispositivos.
- **Evil Twin / Captive portal:** crear un AP falso para engañar usuarios y robar credenciales.

> Nota: mencionar estos ataques sirve para entender riesgos; no proporcionaré pasos para explotarlos.

### Buenas prácticas y recomendaciones (qué hacer hoy)

1. **No uses WEP ni WPA/TKIP.** Desactívalos y actualiza firmware/hardware si tu equipo solo soporta eso.
2. **Usa WPA2-AES (CCMP) o WPA3** si tu hardware lo permite.
3. **En hogares:** WPA2-PSK con una contraseña larga (frase de paso de 16+ caracteres) — por ejemplo: `MiCasa!Segura2025Entrega`. Mejor: usa una passphrase tipo frase.
4. **En empresas:** WPA2/WPA3-Enterprise (802.1X + RADIUS), preferentemente EAP-TLS con certificados.
5. **Desactiva WPS** (botón/PIN) porque es un vector de ataque conocido.
6. **Mantén firmware actualizado** para mitigar vulnerabilidades (p. ej. KRACK).
7. **Segmenta la red**: red de invitados separada y políticas de firewall.
8. **Si trabajas en redes públicas**, usa **VPN** para cifrar tu tráfico adicionalmente.

### Resumen final (en una frase)

- **WEP = inseguro (no usar)**.
- **WPA (TKIP) = parche temporal (evitar)**.
- **WPA2 (AES-CCMP) = estándar seguro si se configura correctamente** (o mejor aún: WPA3 / WPA2-Enterprise para entornos corporativos).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 20**         | **Siguiente 22**             |
| ------------------ | -------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./12_20_ACL.md) | [⏩](./12_22_Preparation.md) |
