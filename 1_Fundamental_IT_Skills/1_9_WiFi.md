| **Inicio**         | **atrás 8**              | **Siguiente 10**         |
| ------------------ | ------------------------ | ------------------------ |
| [🏠](../README.md) | [⏪](./1_8_Bluetooth.md) | [⏩](./1_10_Infrared.md) |

---

## **Índice**

| Temario                                                                                |
| -------------------------------------------------------------------------------------- |
| [23. Redes inalámbricas - Howstuffworks](#23-redes-inalámbricas---howstuffworks)       |
| [24. Así funciona el Wi-Fi](#24-así-funciona-el-wi-fi)                                 |
| [25. Explicación de las redes inalámbricas](#25-explicación-de-las-redes-inalámbricas) |

---

# **WiFi**

## **23. Redes inalámbricas - Howstuffworks**

![Wi-Fi](/img/1_Fundamental_IT_Skills/wifi.webp "Wi-Fi")

### 1) ¿Qué es una red inalámbrica?

Una red inalámbrica (o Wi-Fi cuando aplica la familia 802.11) transmite datos entre dispositivos usando **ondas de radio** en lugar de cables. En la práctica, tu teléfono, laptop o TV convierten datos digitales en señales de radio; un punto de acceso (o router inalámbrico) recibe esas señales, las decodifica y las envía al resto de la red o a Internet. Esto es análogo a una emisora de radio bidireccional: todos “hablan” y “escuchan” en el aire.

### 2) Cómo funciona — paso a paso (muy práctico)

1. La **tarjeta Wi-Fi** del dispositivo (NIC/Wireless adapter) convierte paquetes de datos en una señal de radio y la transmite por su antena.
2. El **router / punto de acceso (AP)** recibe esa señal, la decodifica y la envía al resto de la red (por ejemplo, al módem y luego al proveedor de Internet).
3. Cuando llega una respuesta desde Internet, el proceso se invierte: el router envía la señal por radio al dispositivo correcto (identificado por su dirección MAC).
4. Todo esto ocurre en pequeñas ráfagas y usando protocolos (802.11) que gestionan quién habla y cuándo para evitar colisiones.

### 3) Componentes principales (y su papel)

- **Adaptador inalámbrico (NIC)**: en laptops, móviles, cámaras; convierte datos ↔ radio.
- **Router / Punto de acceso (AP)**: centraliza las conexiones inalámbricas; puede también enrutar tráfico entre subredes. Muchos routers domésticos son “router + AP” en un solo equipo.
- **Módem**: conecta la red doméstica con el proveedor de Internet (ISP).
- **SSID / BSSID**: nombre de la red (SSID) y el identificador único del AP (BSSID).
- **Canal y ancho de banda**: el AP transmite en un canal (por ejemplo 20/40/80/160 MHz); mayor ancho → más datos pero mayor probabilidad de interferencia.

### 4) Bandas de frecuencia y canales — ¿qué elegir?

- **2.4 GHz**: mayor alcance, atraviesa mejor paredes, pero **mucho más congestionada** (Bluetooth, microondas, otros Wi-Fi). En EE. UU. hay 11 canales y los **canales no solapados** recomendados son **1, 6 y 11** (evitan interferencia por solapamiento).
- **5 GHz**: menor alcance que 2.4 GHz pero **más canales** y menos interferencia → mejor para streaming/ gaming. Algunas bandas 5 GHz usan DFS (deben evitar radares).
- **6 GHz (Wi-Fi 6E / Wi-Fi 7)**: espectro nuevo para menos congestión y canales muy anchos (ideal para altas tasas).

**Consejo práctico:** para un hogar, usa SSID dual (p. ej. `Casa-2.4G` y `Casa-5G`), conecta dispositivos con baja latencia (console, TV) a 5 GHz y dispositivos IoT que sólo necesitan alcance a 2.4 GHz.

### 5) Estándares (rápido panorama histórico y actual)

IEEE 802.11 evolucionó así (nombres comerciales): 802.11b/g → 802.11n (Wi-Fi 4) → 802.11ac (Wi-Fi 5) → 802.11ax (Wi-Fi 6 / 6E) → 802.11be (Wi-Fi 7). Cada generación mejora velocidad, capacidad y eficiencia en entornos con muchos clientes. Por ejemplo, Wi-Fi 6 tiene mejoras (OFDMA, MU-MIMO), y Wi-Fi 7 introduce canales todavía más anchos y Multi-Link Operation.

**Números orientativos (teóricos):** Wi-Fi 5 (ac) ofrece decenas de cientos de Mbps a varios Gbps teóricos; Wi-Fi 6 puede llegar a \~9.6 Gbps en condiciones ideales; Wi-Fi 7 promete cifras aún mayores (teóricas). En la práctica siempre verás menos por interferencia, distancia y limitaciones del ISP.

### 6) Seguridad: WEP (obsoleto) → WPA2 → WPA3

- **WEP**: inseguro — no usar.
- **WPA2** (AES): estándar durante años; aún ampliamente usado.
- **WPA3**: mejora la protección contra ataques offline (handshake SAE), ofrece cifrado por cliente y requiere mejores prácticas; es la recomendación para equipo nuevo y para usar en 6 GHz.

**Acciones prácticas para mayor seguridad:** usar **WPA3** si tus dispositivos lo soportan, cambiar la contraseña del router (por defecto son débiles), desactivar WPS si no lo necesitas, habilitar red de invitados para visitas y mantener el firmware actualizado.

### 7) Tipos de topologías y casos de uso (con ejemplos)

- **Infraestructura (más común):** dispositivos se conectan a un AP/Router. Ej.: casa, oficina pequeña.

  _Ejemplo real:_ en casa tienes un router dual-band; la laptop se conecta a 5 GHz para Zoom; la lámpara inteligente a 2.4 GHz.

- **Ad-hoc / Peer-to-peer:** dispositivos se comunican directamente (sin AP) — útil para transferencias inmediatas entre 2 PCs.
- **Mesh (malla):** muchos nodos que se conectan entre sí para cubrir áreas grandes (vecindarios, campus). Ideal si tu casa es grande o con muchas paredes; los nodos reparten la carga y eliminan “zonas muertas”.

### 8) Factores que degradan la señal o la velocidad (y soluciones)

- **Distancia y paredes**: más distancia = menor señal; materiales como concreto/metal empeoran mucho. Solución: mover router a punto central o usar cableado/mesh.
- **Interferencia con otros dispositivos**: microondas, Bluetooth o redes vecinas. Solución: cambiar canal (2.4 GHz → 1/6/11) o usar 5 GHz.
- **Saturación de clientes**: muchos dispositivos conectados reducen la velocidad para cada uno. Solución: QoS en el router o agregar APs.
- **Firmware antiguo / seguridad débil**: actualiza y habilita WPA3 cuando sea posible.

### 9) Ejemplo práctico paso-a-paso (configurar una red Wi-Fi doméstica básica)

1. Coloca el router en un lugar central y elevado (no en closet).
2. Conecta el router al módem y enciéndelo.
3. Entra a la página de administración (IP del router) y cambia la contraseña de administración.
4. Crea dos SSID: `Casa-5G` y `Casa-2.4G`. Activa WPA3 (o al menos WPA2) para ambas.
5. Si hay zonas muertas, añade un nodo mesh o un AP conectado por Ethernet.
6. Usa una app (Wi-Fi Analyzer) para revisar qué canal usan los vecinos y elige 1/6/11 en 2.4 GHz según saturación.

### 10) Troubleshooting rápido (chequeo en 3 minutos)

- ¿Tu ISP funciona? — prueba el cable directo al módem.
- Reinicia router/modem.
- Mide señal cerca del router (debe ser muy fuerte).
- Cambia canal 2.4 GHz a 1/6/11 y prueba 5 GHz si tu dispositivo lo soporta.
- Actualiza firmware y revisa si hay reglas de QoS o límite de clientes.

### Resumen rápido / “cheat-sheet”

- **Si quieres alcance:** usa 2.4 GHz.
- **Si quieres velocidad y bajas latencias:** prioriza 5 GHz / Wi-Fi 6+.
- **Para casas grandes:** considera **mesh** o APs por cable.
- **Seguridad mínima:** WPA2; **ideal:** WPA3.
- **Canales 2.4 GHz:** usa 1, 6, 11 para evitar solapamiento.

---

[🔼](#índice)

---

## **24. Así funciona el Wi-Fi**

### 1) ¿Qué es Wi-Fi, en pocas palabras?

Wi-Fi es la tecnología que permite enviar y recibir datos usando **ondas de radio** en vez de cables. Cuando tu laptop o tu celular “conversan” con un router, lo hacen transformando paquetes de datos en señales de radio y viceversa. Wi-Fi es la implementación práctica de los estándares IEEE 802.11 (Wi-Fi 4/5/6/6E/7, etc.).

**Analogía:** imagina un pasillo donde la gente se pasa notas. Las notas son paquetes de datos, el pasillo es el canal de radio y las reglas de cortesía (quién habla cuándo) son los protocolos Wi-Fi.

### 2) Capas clave — qué ocurre físicamente y en la MAC

**Capa física (Radio / RF)**

- Frecuencias comunes: **2.4 GHz** (mayor alcance, más interferencia), **5 GHz** (menos interferencia, mayor velocidad), y **6 GHz** (Wi-Fi 6E).
- Canales: el espectro se divide en “canales” (en 2.4 GHz, los canales no solapados usuales son 1, 6 y 11).
- Modulación y ancho: Wi-Fi usa modulaciones como OFDM; puedes agrupar canales (20 / 40 / 80 / 160 MHz) para más velocidad.

**Capa MAC (control de acceso al medio)**

- Wi-Fi usa **CSMA/CA** (Carrier Sense Multiple Access with Collision Avoidance): antes de transmitir un dispositivo “escucha” si el canal está libre y espera un periodo aleatorio para evitar colisiones.
- Opcionalmente se usan RTS/CTS (petición/confirmación) para mitigación de colisiones en enlaces problemáticos.
- Cada trama enviada necesita un **ACK** (confirmación) para saber si llegó bien.

### 3) Paso a paso: ¿qué ocurre cuando te conectas a una red Wi-Fi? (ejemplo práctico)

Escenario: conectas tu laptop al router de tu casa.

1. **Escaneo / Descubrimiento**

   - Laptop envía _probe requests_ (o escucha beacons).
   - El router envía _beacon frames_ periódicos con información: SSID (nombre de la red), canales, tasas soportadas.

2. **Elección de red y autenticación**

   - Escoges el SSID y el método de seguridad (por ejemplo WPA2-PSK o WPA3).
   - Si la red usa contraseña, empieza el proceso de autenticación.
   - En WPA2 se hace el **4-way handshake** (se acuerdan claves temporales para cifrar la comunicación). En WPA3 se usa **SAE** (más seguro contra ataques por diccionario).

3. **Asociación**

   - La estación pide asociarse; el AP responde aceptando y asignando recursos locales (ej.: entradas en su tabla de asociación).

4. **Obtención de IP (DHCP)**

   - Laptop solicita IP por DHCP y recibe una IP privada (p. ej. 192.168.1.12). Ahora puede comunicarse a nivel IP.

5. **Tráfico de datos**

   - Datos de aplicaciones se fragmentan en tramas 802.11, se cifran, se transmiten por radio. AP reenvía paquetes hacia/desde Internet.

6. **Mantenimiento**

   - El AP envía beacons periódicos; el cliente manda “keep-alive” y ACKs; cuando preguntas por desconexión, se realiza la desasociación.

### 4) Elementos técnicos importantes (con ejemplos)

- **SSID / BSSID**: SSID = nombre (p. ej. `CasaGuss`); BSSID = MAC del AP (único por radio).
- **Direcciones MAC**: cada NIC tiene MAC (identifica a nivel enlace).
- **Beacon frame**: periódico — anuncia la red.
- **Probe request/response**: cliente pregunta; AP responde.
- **ACKs**: cada trama debe confirmarse; si no llega, se reenvía.
- **NAT en el router**: tu IP local se “traduce” a la IP pública del ISP para salir a Internet.

**Ejemplo rápido:** en una videollamada la app envía paquetes UDP al servidor; estos se encapsulan en tramas 802.11, viajan al AP, salen por el módem y llegan al servicio de videollamada.

### 5) Tecnologías modernas que mejoran Wi-Fi (qué hacen y por qué importan)

- **MIMO / MU-MIMO**: múltiples antenas permiten enviar/recibir varios “streams” a la vez. Ejemplo: un router con 4×4 MIMO puede enviar datos simultáneos a varios dispositivos.
- **Beamforming**: el AP “enfoca” la señal hacia el cliente para mejorar la recepción, como apuntar una linterna en vez de iluminar todo el cuarto.
- **OFDMA** (Wi-Fi 6): divide un canal en sub-portadoras para atender pequeños flujos de muchos dispositivos eficientemente (ideal para IoT y entornos concurridos).
- **Channel bonding**: combinar canales (p. ej. 80 MHz) aumenta velocidad teórica, pero usa más espectro y sufre más interferencia.
- **Multi-Link Operation (Wi-Fi 7)**: usar múltiples bandas simultáneamente para menor latencia y más throughput (avanzado).

### 6) Seguridad — riesgos y cómo mitigarlos (ejemplos)

- **WEP**: totalmente inseguro. No usar.
- **WPA2-PSK**: común; si la contraseña es débil, atacantes pueden obtenerla con diccionarios.
- **WPA3-SAE**: previene ataques offline y mejora seguridad en redes con contraseñas.
- **Red abierta (sin contraseña)**: riesgo alto — cualquiera puede interceptar tu tráfico (man-in-the-middle). En cafeterías, usa VPN.
- **Buenas prácticas**: usar WPA3 si es posible, contraseña larga y única, actualizar firmware, desactivar WPS, separar red de invitados.

**Ejemplo de riesgo real:** te conectas a "Wi-Fi gratuito" en un café sin HTTPS — un atacante en la misma red puede ver tráfico no cifrado. Solución: usar VPN o sitios con HTTPS.

### 7) Factores que afectan rendimiento y ejemplos prácticos

- **Distancia / obstáculos**: paredes de concreto/metal atenúan señal. Ejemplo: router en sótano → mala señal en el piso superior. Solución: mover router o usar APs/cable Ethernet o mesh.
- **Interferencia**: microondas, Bluetooth, redes vecinas. Ejemplo: señal baja al cocinar porque el microondas interfiere en 2.4 GHz. Solución: usar 5 GHz o cambiar canal.
- **Congestión de clientes**: muchos dispositivos conectados reducen ancho disponible. Ejemplo: casa con 20 dispositivos IoT → menor throughput para la videollamada. Solución: QoS o añadir AP.
- **Canal y ancho**: 20 MHz = más estable; 80/160 MHz = más velocidad si no hay interferencia. Ejemplo: consola de juegos a 80 MHz en 5 GHz para baja latencia.

### 8) Troubleshooting práctico (qué hacer paso a paso)

1. **Verifica si el problema es del ISP**: conecta un PC por cable al módem.
2. **Reinicia módem/router**.
3. **Acércate al router**: si mejora, es problema de cobertura.
4. **Cambia canal (2.4 GHz a 1/6/11)** o mueve a 5 GHz si hay mucha interferencia.
5. **Actualiza firmware** del router.
6. **Usa una app o comando** para ver señal: en Android “Wi-Fi Analyzer”, en Linux `iwconfig`/`iw`, en Windows `netsh wlan show interfaces`.
7. **Si hay zonas muertas**, añade un AP por cable o un sistema mesh.
8. **Si la red pública tiene problemas de seguridad**, usa VPN.

### 9) Casos de uso / ejemplos concretos

- **Casa pequeña (1 piso)**: un router dual-band moderno suele bastar. Conecta TV/Consola a 5 GHz, sensores a 2.4 GHz.
- **Casa grande / varios pisos**: usa APs cableados o mesh para evitar zonas muertas (un único router en un extremo no llegará al otro extremo).
- **Oficina concurrida**: APs con control central (controller) y Wi-Fi 6 para manejar muchos usuarios con OFDMA y MU-MIMO.
- **Cafetería (red pública)**: red de invitados, captive portal; vital usar HTTPS o VPN para seguridad.

### 10) Resumen rápido — “cheat-sheet” para optimizar tu Wi-Fi

- Coloca el router en un punto central y alto.
- Usa 5 GHz para dispositivos que necesitan velocidad/latencia.
- Para cobertura amplia, preferible AP cableado o mesh.
- En 2.4 GHz usa canales 1/6/11 (evita solapamiento).
- Prioriza WPA3 o al menos WPA2 con contraseña fuerte.
- Actualiza firmware y desactiva WPS.
- Si tu videollamada se traba: prueba acercarte al router, cambiar a 5 GHz, o priorizar la app con QoS.

---

[🔼](#índice)

---

## **25. Explicación de las redes inalámbricas**

### 1) ¿Qué es una red inalámbrica?

Una **red inalámbrica** permite que dispositivos (móvil, laptop, cámaras, sensores) intercambien datos sin usar cables, empleando **ondas de radio**. En Wi-Fi —la forma más común de red inalámbrica en hogares y oficinas— ese intercambio sigue las reglas del estándar IEEE 802.11.

**Analogía rápida:** es como una conversación en una sala donde las notas (paquetes) se pasan por el aire; hay reglas para evitar que varias personas hablen a la vez.

### 2) Componentes principales y su papel (con ejemplos)

- **Cliente / estación (STA):** tu teléfono, laptop, cámara. → _Ej.: tu celular se conecta al Wi-Fi del router._
- **Punto de acceso (AP) / Router inalámbrico:** centraliza conexiones inalámbricas y conecta a Internet. → _Ej.: el router de tu casa con SSID “CasaGustavo”._
- **Módem:** puente entre tu red y el ISP (Internet).
- **Controlador (controller) / RADIUS (en entornos empresariales):** gestiona muchos APs y autenticación (802.1X). → _Ej.: oficina con varios APs gestionados por un controlador._
- **Antenas, switches, repetidores, mesh nodes:** extienden o optimizan la cobertura. → _Ej.: nodo mesh en segundo piso para evitar zona muerta._

### 3) Qué pasa cuando tu dispositivo se conecta (paso a paso, ejemplo)

Escenario: conectas la laptop al Wi-Fi de casa.

1. **Escaneo / descubrimiento:** tu laptop ve los _beacons_ del AP y muestra SSIDs.
2. **Autenticación:** introduces la contraseña; se inicia el proceso de seguridad (WPA2/WPA3).
3. **Asociación:** el AP añade a la laptop a su tabla de asociados.
4. **Obtención de IP (DHCP):** la laptop solicita y recibe una IP privada (ej. 192.168.1.12).
5. **Tráfico:** aplicaciones mandan/reciben paquetes; la NIC los transforma en tramas 802.11 y los transmite por radio.
6. **Mantenimiento/desconexión:** el AP y el cliente envían señales periódicas; al salir, se desasocian.

### 4) Capas clave: física (RF) y MAC — qué hacen

- **Capa física (RF):** frecuencias (2.4 GHz, 5 GHz, 6 GHz), canales, ancho (20/40/80/160 MHz), modulaciones (OFDM). Más ancho = más throughput teórico.
- **Capa MAC:** controla quién transmite. Wi-Fi usa **CSMA/CA** (escucha antes de hablar) y requiere **ACKs** para confirmar recepción. Hay mecanismos RTS/CTS para enlaces con muchas colisiones.

**Ejemplo:** en 2.4 GHz con muchos vecinos, si todos intentan hablar sin coordinación la performance baja —por eso se usan 5 GHz o OFDMA en Wi-Fi 6 para organizar mejor el tráfico.

### 5) Bandas y canales (qué elegir y por qué)

- **2.4 GHz:** mayor alcance y mejor penetración en paredes, pero muy congestionada (IoT, microondas, Bluetooth). Canales no solapados típicos: **1, 6, 11**.
- **5 GHz:** más canales, menos interferencia, mejor para streaming/gaming; alcance menor. Algunas sub-bandas requieren **DFS** (detectar radar).
- **6 GHz (Wi-Fi 6E):** nuevos canales, muy poca congestión —ideal si tus dispositivos lo soportan.

**Consejo práctico:** usa 5 GHz para videollamadas/consolas; 2.4 GHz para sensores y dispositivos que necesitan más alcance.

### 6) Estándares y evolución (resumen útil)

- **802.11b/g** (antiguo), **802.11n** = _Wi-Fi 4_.
- **802.11ac** = _Wi-Fi 5_ (enfocado en 5 GHz, channel bonding).
- **802.11ax** = _Wi-Fi 6 / 6E_ (OFDMA, mejor eficiencia en entornos concurridos; 6E añade 6 GHz).
- **802.11be** = _Wi-Fi 7_ (Multi-Link Operation, canales aún más anchos).

**Ejemplo:** un router Wi-Fi 6 maneja mejor 30 dispositivos transmitiendo pequeñas cantidades de datos simultáneamente, gracias a OFDMA.

### 7) Seguridad (riesgos y recomendaciones)

- **WEP:** obsoleto y vulnerable. No usar.
- **WPA2-PSK:** todavía común; asegura, pero queda expuesto si la contraseña es débil.
- **WPA3:** mejora la resistencia a ataques (SAE) y protege mejor redes con contraseñas humanas.
- **802.1X / Enterprise:** autenticación con servidores RADIUS (ideal para empresas).
- **Buenas prácticas:** contraseña fuerte, WPA3 cuando sea posible, desactivar WPS, red de invitados para visitas, actualizar firmware.

**Ejemplo real:** en una cafetería pública usa VPN si entras a servicios que no usan HTTPS.

### 8) Topologías comunes y ejemplos de uso

- **Infraestructura:** AP <-> clientes. (Casa u oficina pequeña).
- **Ad-hoc / IBSS:** dispositivos hablan entre sí sin AP (poco usado hoy). Ej.: transferencia rápida entre dos laptops sin router.
- **Mesh (malla):** nodos distribuidos que se interconectan para cubrir áreas grandes. Ej.: casa de 2 pisos con router + 2 satélites.
- **Extendedor / repetidor:** repite señal (sencillo, pero puede halver throughput si usa el mismo canal).

### 9) Tecnologías que mejoran la performance (qué hacen y por qué importan)

- **MIMO / MU-MIMO:** múltiples antenas permiten transmitir varios flujos simultáneos. _Ej.: router 4×4 MIMO._
- **Beamforming:** enfoca la señal hacia el cliente, mejor recepción. _Ej.: mejor calidad de señal para tu laptop en la sala._
- **OFDMA (Wi-Fi 6):** divide el canal en subunidades para atender muchos clientes pequeños eficientemente. _Ej.: muchos sensores IoT transmitiendo datos pequeños sin pelear por el canal._
- **Channel bonding:** combina canales para más velocidad (pero mayor probabilidad de interferencia).

### 10) Problemas comunes, causas y soluciones rápidas (ejemplos)

- **Baja cobertura en ciertas habitaciones** → causa: distancia/obstáculos (concreto/metal). → solución: mover AP, usar mesh o cablear APs.
- **Interferencia (vecinos, microondas)** → causa: canal saturado. → solución: cambiar canal, usar 5 GHz.
- **Red lenta con muchos dispositivos** → causa: saturación. → solución: activar QoS, añadir APs, usar Wi-Fi 6.
- **Conexiones que se caen** → causa: firmware o incompatibilidad; probar actualización de firmware y ajustes de seguridad (WPA mode).

**Ejemplo práctico:** si tu Zoom tiene Eco y lag cuando la familia transmite video, prioriza la aplicación con QoS o conecta la laptop por cable si es posible.

### 11) Troubleshooting paso a paso (3–10 minutos)

1. Conecta un equipo por cable al módem para verificar si el ISP funciona.
2. Reinicia módem + router.
3. Acércate al router: si mejora, es señal/alcance.
4. Cambia canal 2.4 GHz a 1/6/11 o prueba 5 GHz.
5. Actualiza firmware del router.
6. Usa una app (Wi-Fi Analyzer en Android) o `netsh wlan show interfaces` (Windows) / `iw` (Linux) para medir RSSI/SNR.
7. Si persisten zonas muertas, agrega AP o mesh con backhaul por cable.

### 12) Casos prácticos con recomendaciones concretas

- **Casa pequeña (1 piso, 3 habitaciones):** router dual-band moderno en el centro. TV y consola en 5 GHz. Sensores en 2.4 GHz.
- **Casa de 2 pisos con sótano:** usar APs cableados o un sistema mesh con backhaul Ethernet; colocar nodo principal en el centro.
- **Oficina con 50+ personas:** APs profesionales, controlador central, 802.1X con RADIUS, Wi-Fi 6 para eficiencia, planificación de canales y capacidad.
- **Cafetería / hotspot público:** red de invitados aislada, portal cautivo, limitar ancho por usuario, usar HTTPS/VPN para tráfico sensible.

### 13) Resumen rápido / checklist para optimizar Wi-Fi en casa

- Coloca el router en un punto central y alto.
- Usa 5 GHz para dispositivos que requieren velocidad.
- Divide redes: invitados vs. dispositivos de confianza.
- Activa WPA3 si es posible; usa contraseña larga.
- Actualiza firmware regularmente.
- Mide canales y elige el menos congestionado.
- Añade APs cableados o mesh si hay zonas muertas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 8**              | **Siguiente 10**         |
| ------------------ | ------------------------ | ------------------------ |
| [🏠](../README.md) | [⏪](./1_8_Bluetooth.md) | [⏩](./1_10_Infrared.md) |
