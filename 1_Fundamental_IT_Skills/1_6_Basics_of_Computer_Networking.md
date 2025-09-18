| **Inicio**         | **atrás 5**                                        | **Siguiente 7**    |
| ------------------ | -------------------------------------------------- | ------------------ |
| [🏠](../README.md) | [⏪](./1_5_Understand_Basics_of_Popular_Suites.md) | [⏩](./1_7_NFC.md) |

---

## **Índice**

| Temario                                                                                                       |
| ------------------------------------------------------------------------------------------------------------- |
| [16. Conceptos básicos de redes: lo que necesita saber](#16-conceptos-básicos-de-redes-lo-que-necesita-saber) |
| [17. Computer Networking](#17-computer-networking)                                                            |

---

# **Conceptos básicos de redes informáticas**

## **16. Conceptos básicos de redes: lo que necesita saber**

![Modelo OSI](/img/1_Fundamental_IT_Skills/modelo_OSI.webp "Modelo OSI")

### 1) ¿Qué es una red?

Una **red** es un conjunto de dispositivos (PC, móviles, impresoras, servidores) conectados entre sí para **compartir datos y recursos** (archivos, internet, impresoras).

**Ejemplo real:** en casa el router conecta tu PC, tu celular y la Smart TV para que todos usen la misma conexión a Internet.

### 2) Modelos: OSI y TCP/IP (qué hace cada capa)

- **Modelo OSI (7 capas)**: Aplicación → Presentación → Sesión → Transporte → Red → Enlace → Física.
  Sirve para entender responsabilidades (por ejemplo: la capa Red usa IP; la capa Enlace usa MAC).
- **Modelo TCP/IP (4 capas)**: Aplicación → Transporte → Internet → Acceso a la red (equivalente a OSI físico+enlace).

**Ejemplo:** cuando abres una web:

- Capa Aplicación: HTTP pide la web.
- Capa Transporte: TCP garantiza entrega (handshake).
- Capa Internet: IP dirige los paquetes a la dirección correcta.
- Capa Enlace/Física: Ethernet + cable o Wi-Fi transmiten bits.

### 3) Direcciones: IP vs MAC

- **MAC address**: identificador físico de una interfaz de red (ej. `00:1A:2B:3C:4D:5E`). Se usa dentro de la misma red local (switch).
- **IP address**: identificador lógico que permite enrutar entre redes (ej. IPv4 `192.168.1.10` o IPv6 `2001:db8::1`).

**Ejemplo:** en tu LAN, el switch entrega tramas a una MAC; cuando quieres acceder a google.com se usa una IP para enviar paquetes fuera de tu red.

### 4) IPv4, IPv6 y máscaras/subredes (con cálculos)

- **IPv4**: direcciones de 32 bits (ej. `192.168.1.0/24`).
- **IPv6**: direcciones de 128 bits, diseñadas para resolver la escasez de IPv4.

**Subredes — ejemplo práctico /24 vs /30**

- `/24`: 32 − 24 = 8 bits para hosts → 2^8 = 256 direcciones totales.

  - Usables = 256 − 2 (network + broadcast) = **254 hosts**.
    (Cálculo paso a paso: 32−24=8 → 2^8=256 → 256−2=254.)

- `/30`: 32 − 30 = 2 bits → 2^2 = 4 direcciones totales → usables = 4 − 2 = **2 hosts** (útil para enlace punto a punto).

**Ejemplo:** una red doméstica típica usa `192.168.1.0/24` y puede tener hasta 254 dispositivos.

### 5) DHCP: asignación automática de IPs

- **DHCP** (Dynamic Host Configuration Protocol) asigna IP, puerta de enlace y DNS automáticamente.
- **Handshake**: Discover → Offer → Request → ACK (DORA).

**Ejemplo:** cuando conectas tu laptop al Wi-Fi del café, tu laptop envía un _Discover_, el router (servidor DHCP) responde con una IP disponible, tu laptop la acepta y ya tiene conexión.

### 6) DNS: resolución de nombres

- **DNS** convierte nombres (ej. `google.com`) en IPs (ej. `142.250.190.14`).
- Hay resolutores recursivos, cachés locales y servidores autoritativos.

**Ejemplo:** al teclear `example.com` el sistema pregunta al resolver DNS, que devuelve la IP para que el navegador se conecte.

### 7) NAT (Network Address Translation)

- Permite que muchos dispositivos con IPs privadas (192.168.x.x) salgan a Internet usando **una sola IP pública**.
- La variante común es **PAT** (masquerading): mapea conexiones por puerto.

**Ejemplo:** tu router en casa tiene IP pública `200.1.2.3`. Todos tus dispositivos usan NAT para navegar; desde Internet solo se ve `200.1.2.3`.

### 8) Switch vs Router vs Access Point

- **Switch:** opera en capa 2 (usa MAC) y conecta dispositivos dentro de la misma red local.
- **Router:** opera en capa 3 (usa IP) y conecta redes diferentes (LAN ↔ WAN).
- **Access Point (AP):** punto de acceso Wi-Fi que conecta dispositivos inalámbricos a la red cableada.

**Ejemplo:** en una oficina:
PCs → switch → router → Internet.
AP conecta notebooks al switch para acceso inalámbrico.

### 9) TCP vs UDP (transporte)

- **TCP:** conexión fiable, control de flujo, reintentos (ej. web, correo, SSH).
- **UDP:** sin conexión, rápido, baja latencia (ej. DNS, VoIP, video en tiempo real).

**Ejemplo:** ver un video por streaming suele usar UDP (o protocolos basados en UDP) para minimizar latencia; descargar un archivo usa TCP para asegurar integridad.

### 10) Puertos y sockets

- Los **puertos** identifican servicios en un host: HTTP 80, HTTPS 443, SSH 22, DNS 53, SMTP 25.
- Un **socket** = IP + puerto (ej. `192.168.1.10:22`).

**Ejemplo:** `ssh usuario@192.168.1.50` se conecta al puerto 22 del host destino.

### 11) VLANs (segmentación de red)

- **VLAN** = red local virtual que separa tráfico dentro de un mismo switch físico.
- Usadas para segmentar departamentos y mejorar seguridad/gestión.

**Ejemplo:** en una empresa pones Finanzas en VLAN 10 y RRHH en VLAN 20: aunque estén en el mismo switch, no se hablan a menos que un router lo permita.

### 12) Conceptos de rendimiento: ancho de banda, latencia y jitter

- **Ancho de banda** = capacidad (Mbps/Gbps).
- **Latencia (ping)** = tiempo que tarda un paquete en ir y volver (ms).
- **Jitter** = variación de la latencia (importante para voz/video).

**Ejemplo:** en un juego online necesitas baja latencia (pings <50 ms) más que mucho ancho de banda; en descargas importa ancho de banda.

### 13) Seguridad básica de red

- **Firewalls** filtran tráfico por puertos/IP.
- **ACLs** en switches/routers controlan acceso.
- **Segmentación** (VLANs) reduce alcance de ataques.
- **WPA2/WPA3** protegen Wi-Fi; usa contraseñas fuertes y desactiva WPS.
- **VPN** para acceso remoto seguro (túnel cifrado).
- **IDS/IPS** para detección/prevenir intrusiones.

**Ejemplo:** conecta la cámara IP en una VLAN aislada para que, si se compromete, no acceda a la red corporativa.

### 14) Medios físicos: Ethernet y Wi-Fi

- **Ethernet (cable):** más estable y rápido (Cat5e, Cat6, Cat6a, Cat8).

  - Cat5e ≈ 1 Gbps, Cat6 puede 10 Gbps en distancias cortas (≈55 m), Cat6a 10 Gbps a 100 m.

- **Wi-Fi:** inalámbrico, estándares modernos: 802.11n/ac/ax (Wi-Fi 4/5/6).

**Ejemplo:** para streaming 4K y gaming, cable Ethernet ofrece mejor experiencia; Wi-Fi es cómodo para móviles.

### 15) Herramientas & comandos básicos para troubleshooting

**Windows**

- `ipconfig /all` → ver IP, gateway, DNS.
- `ping 8.8.8.8` → comprobar conectividad IP.
- `tracert google.com` → rutas hasta destino.
- `nslookup ejemplo.com` → consulta DNS.
- `netstat -ano` → conexiones y puertos.

**Linux / macOS**

- `ip a` o `ifconfig` → ver interfaces.
- `ping -c 4 8.8.8.8` → ping.
- `traceroute google.com` (o `tracepath`) → traza ruta.
- `dig ejemplo.com` → DNS.
- `ss -tulpn` o `netstat -tulpn` → sockets en escucha.
- `tcpdump -i eth0` → captura de paquetes (útil si sabes filtrarlos).
- `iwconfig` / `nmcli` → info/gestión Wi-Fi.

**Ejemplo de uso:** si no tienes internet:

1. `ip a` para ver si tienes IP.
2. `ping 8.8.8.8` comprueba conectividad a Internet (evita DNS).
3. `ping google.com` comprueba que DNS funciona.
4. `traceroute` si hay pérdida de paquetes.

### 16) Packet capture y análisis (Wireshark)

- Capturar paquetes te permite ver **qué se está enviando/recibiendo**.
- Útil para errores de protocolo, retransmisiones TCP o problemas de DNS.

**Ejemplo:** si una web carga lento, captura tráfico y busca retransmisiones TCP o respuestas DNS lentas.

### 17) Buenas prácticas y diseño básico de red

- Usa **IP planificada** (no dejar IPs asignadas al azar).
- Reserva subredes por departamento.
- Mantén **backups** de configuración de routers/switches.
- Actualiza firmware y patches con control.
- Documenta topología y puntos de acceso.

**Ejemplo:** documento simple: 192.168.10.0/24 = ventas, 192.168.20.0/24 = ingeniería, 192.168.99.0/24 = servidores.

### 18) Mini-proyectos para aprender (prácticos)

1. **Mapa de tu red doméstica**: dibuja topología, identifica IPs y MACs.
2. **Montar un servidor DHCP/DNS en Raspberry Pi (Pi-hole)**: práctica real de servicios de red.
3. **Configurar VLANs en un switch gestionado** y probar aislamiento entre ellas.
4. **Capturar tráfico con Wireshark** mientras navegas y analizar DNS/TCP.
5. **Montar VPN (OpenVPN/WireGuard)** para acceso remoto a tu LAN.

### 19) Chuleta rápida (resumen utilísimo)

- IP = localización lógica; MAC = identificador físico.
- DHCP = asignación automática; DNS = nombres → IP.
- Router = conecta redes; switch = conecta dispositivos en la misma red.
- NAT = permite salir a Internet con una IP pública.
- TCP = fiable; UDP = rápido.
- Comandos: `ipconfig`/`ip a`, `ping`, `traceroute`, `nslookup`/`dig`, `tcpdump`/`wireshark`.

---

[🔼](#índice)

---

## **17. Computer Networking**

### 1) ¿Qué es una red de computadoras?

Una **red** es un conjunto de dispositivos (computadoras, móviles, impresoras, servidores, cámaras, etc.) conectados entre sí para **compartir información y recursos** (archivos, internet, impresoras, bases de datos).
**Analogía:** una red es como una ciudad: dispositivos = casas, cables/ondas = calles, routers = oficinas postales que enrutan correspondencia, switches = conmutadores que entregan cartas dentro de una misma calle.

**Ejemplo sencillo:** en tu casa el router conecta tu laptop, tu celular y la Smart TV para que todos accedan a Internet y compartan archivos en la impresora.

### 2) Componentes físicos y dispositivos importantes

- **NIC (Network Interface Card):** interfaz de red del dispositivo (tarjeta o chip Wi-Fi/Ethernet).
- **Cableado / Fibra / Radio:** medios físicos (Ethernet Cat5e/Cat6, fibra óptica) o inalámbricos (Wi-Fi, Bluetooth).
- **Switch:** conecta dispositivos dentro de la misma red local (LAN) y envía tramas a la MAC correcta.
- **Router:** conecta redes diferentes (p. ej. tu LAN con Internet) y enruta paquetes basándose en direcciones IP.
- **Access Point (AP):** crea la red Wi-Fi para que dispositivos inalámbricos se conecten.
- **Modem:** traduce la señal del proveedor de Internet (ISP) a algo que tu router pueda usar.
- **Firewall / IDS / IPS:** controles de seguridad que filtran o supervisan tráfico.

**Ejemplo práctico:** en una oficina típica: PCs → switch → router → modem → Internet.

### 3) Modelos conceptuales: OSI y TCP/IP

- **Modelo OSI (7 capas):**

  7 Aplicación | 6 Presentación | 5 Sesión | 4 Transporte | 3 Red | 2 Enlace | 1 Física.
  Sirve para entender responsabilidades (por ejemplo, IP en capa 3, Ethernet en capa 2).

- **Modelo TCP/IP (4 capas):** Aplicación | Transporte | Internet | Acceso a red (más práctico).

**Por qué importa:** ayuda a diagnosticar problemas; por ejemplo, si `ping` falla pero `arp` funciona, pensamos en capas 3 o superior.

### 4) Direcciones: MAC vs IP

- **MAC address:** dirección física única de una interfaz (ej.: `00:1A:2B:3C:4D:5E`) — usada dentro de la red local (capa 2).
- **IP address:** dirección lógica que permite enrutar entre redes (ej.: IPv4 `192.168.1.10` o IPv6 `2001:db8::1`) — usada en capa 3.

**Ejemplo:** cuando tu PC envía un paquete a otra en la misma LAN, el switch usa MAC. Para ir a Google, el router usa IP para encaminarlos fuera de tu red.

### 5) Protocolos fundamentales (qué hacen y ejemplos)

- **TCP (Transmission Control Protocol):** conexión fiable, reenvío de paquetes perdidos (ej.: HTTP/HTTPS, SSH, SMTP).
- **UDP (User Datagram Protocol):** transmisión sin conexión, rápida, sin reenvío (ej.: DNS, VoIP, streaming).
- **HTTP / HTTPS:** protocolo web (HTTPS = HTTP sobre TLS, cifrado).
- **DNS:** resuelve nombres a IPs (`google.com` → `142.250.190.14`).
- **DHCP:** asigna IP automáticamente a los clientes (DORA: Discover, Offer, Request, Ack).
- **ARP:** resuelve IP → MAC en la LAN.
- **ICMP:** mensajes de control (ping usa ICMP Echo).

**Ejemplo de uso conjunto:** cuando escribes `https://example.com` el navegador usa DNS (para obtener la IP), luego establece conexión TCP (handshake) y usa TLS para cifrar la sesión antes de pedir la página HTTP.

### 6) ¿Cómo viajan los datos? Encapsulación (resumen)

1. Tu aplicación crea un mensaje (ej.: petición HTTP).
2. Capa de transporte (TCP/UDP) encapsula en segmentos/datagramas (añade puerto origen/destino).
3. Capa de red (IP) añade cabecera IP (direcciones IP).
4. Capa de enlace (Ethernet/Wi-Fi) empaqueta en tramas (añade MAC origen/destino).
5. Capa física transmite bits por cable/onda.

**Analogía:** la carta (mensaje) se mete en un sobre (TCP), luego en una caja con la dirección IP (IP), y la empresa local le pone la etiqueta de la calle (MAC) para la entrega final.

### 7) Subnetting (subredes) — explicación con ejemplos y cálculo paso a paso

Una **subred** divide una red mayor en partes más pequeñas. Las máscaras expresan cuántos bits son de red (ej.: `/24`).

#### Ejemplo 1: `/24` (el caso más común)

- Direcciones IPv4 totales: 32 bits.
- Prefijo `/24` → bits de host = `32 − 24 = 8`. (lo calculamos dígito a dígito:)

  - 3 2 − 2 4 = 8

- Número de direcciones = `2^8`. Calcular 2^8 paso a paso:

  - 2^1 = 2
  - 2^2 = 4
  - 2^3 = 8
  - 2^4 = 16
  - 2^5 = 32
  - 2^6 = 64
  - 2^7 = 128
  - 2^8 = 256

- Direcciones totales = **256**.
- Direcciones utilizables = 256 − 2 (una para la red y otra para broadcast) = **254**.

Resultado: `/24` permite **254 hosts** por subred (por ejemplo `192.168.1.1`…`192.168.1.254`).

#### Ejemplo 2: `/30` (punto a punto)

- Bits host = `32 − 30 = 2`.
- 2^2 = 4 → totales = 4 → usables = 4 − 2 = **2 hosts** (ideal para enlaces router ↔ router).

#### Conversión máscara a decimal

- `/24` → 8 bits en la última octeta: `11111111.11111111.11111111.00000000` → decimal `255.255.255.0`.
- `/26` → `32 − 26 = 6` bits host → 2^6 = 64 direcciones totales → usables 62 → máscara `255.255.255.192` (porque `11000000` en la última octeta = 192).

### 8) NAT (Network Address Translation) — por qué existe y cómo funciona

**Problema:** direcciones IPv4 públicas escasas.

**Solución:** NAT permite que múltiples IPs privadas salgan a Internet usando una IP pública. El router traduce `IPprivada:puerto` → `IPpública:puerto` y mantiene una tabla para devolver respuestas al host correcto.

**Ejemplo:** PC1 `192.168.1.10:50000` → Internet ve `200.1.2.3:62000`. Cuando la respuesta llega a `200.1.2.3:62000`, el router sabe que debe enviarla a `192.168.1.10:50000`.

### 9) Switch vs Router — ¿qué hacen cada uno?

- **Switch:** opera en capa 2, entrega tramas dentro de la misma LAN usando tablas MAC. Ideal para conectar PCs dentro del mismo edificio.
- **Router:** opera en capa 3, enruta paquetes entre redes y mantiene tablas de enrutamiento (static/dynamic: OSPF, BGP en entornos grandes).

**Ejemplo:** si dos PCs están conectadas al mismo switch, la comunicación no pasa por el router. Si una PC quiere acceder a Internet, el paquete pasa por el router.

### 10) Topologías comunes

- **Star (estrella):** todos los nodos al switch/centro (muy común).
- **Bus:** un solo medio compartido (histórico).
- **Ring (anillo):** cada nodo al siguiente (uso en algunas WANs).
- **Mesh:** conexiones múltiples redundantes (datacenters, redes críticas).

**Ejemplo:** tu red doméstica es típicamente estrella: dispositivos → router/switch central.

### 11) WLAN / Wi-Fi — conceptos rápidos

- Estándares: 802.11n/ac/ax (Wi-Fi 4/5/6).
- Bandas: 2.4 GHz (más alcance, menos velocidad) vs 5 GHz (más velocidad, menos alcance).
- AP (Access Point) provee cobertura; redes en malla usan múltiples APs para cubrir espacios grandes.

**Ejemplo:** para gaming competitivo preferirás 5 GHz o conexión Ethernet por la menor latencia.

### 12) Rendimiento: ancho de banda, latencia, jitter, pérdida de paquetes

- **Ancho de banda:** capacidad (Mbps/Gbps).
- **Latencia:** tiempo de ida y vuelta (ms).
- **Jitter:** variación de la latencia (malo para voz/video).
- **Pérdida de paquetes:** porcentaje de paquetes que no llegan (afecta retransmisiones y calidad).

**Ejemplo:** videollamadas requieren baja latencia y bajo jitter; descarga de archivos depende más del ancho de banda.

### 13) Seguridad en redes (principios y controles)

- **Cifrado:** TLS/SSL para web (HTTPS), VPN para túneles seguros.
- **Wi-Fi:** WPA2 / WPA3 (usar contraseñas fuertes y desactivar WPS).
- **Firewalls:** reglas para permitir/denegar tráfico por puerto/IP.
- **Segmentación (VLANs):** separar redes para limitar alcance de incidentes.
- **IDS/IPS:** detección y prevención de intrusiones.
- **Autenticación:** 802.1X para control de acceso en redes empresariales.

**Ejemplo práctico:** poner cámaras IoT en una VLAN separada y bloquear su acceso a servidores internos reduce riesgo si se comprometen.

### 14) Servicios de red comunes

- **DNS:** resolución de nombres.
- **DHCP:** asignación dinámica de IP.
- **NTP:** sincronización de tiempo.
- **LDAP/Active Directory:** autenticación y directorios para usuarios.
- **Proxy / Load Balancer:** balancear tráfico entre servidores y cachear contenido.

### 15) Flujo práctico: qué pasa cuando visitas una web (paso a paso)

1. Escribes `example.com`. Tu dispositivo pregunta al **DNS** (¿qué IP tiene?).
2. Obtienes IP y el navegador inicia **handshake TCP** con la IP (SYN, SYN-ACK, ACK).
3. Si es HTTPS, se negocia **TLS** (certificado, claves).
4. Se envía la petición HTTP/GET.
5. El servidor responde con la página; paquetes vuelven y se ensamblan en tu navegador.
6. Tu navegador procesa HTML, solicita recursos (CSS, JS, imágenes) y muestra la página.

### 16) Herramientas de diagnóstico (básicas y útiles)

- `ping` — comprobar conectividad ICMP.
- `traceroute` / `tracert` — ver ruta y saltos hasta destino.
- `nslookup` / `dig` — consultas DNS.
- `ipconfig` / `ifconfig` / `ip a` — ver configuración IP.
- `netstat` / `ss` — conexiones y puertos.
- `tcpdump` / **Wireshark** — captura y análisis de paquetes.

**Ejemplo de troubleshooting:** si no navegas, `ping 8.8.8.8` (prueba IP pública). Si responde, hay conectividad IP; `ping example.com` y si falla es problema DNS.

### 17) Ejemplos de redes comunes (casos de uso)

- **Red doméstica:** router (NAT) + Wi-Fi + 1-2 switches pequeños.
- **Red de oficina pequeña:** switch gestionable, router con firewall, servidor local y APs.
- **Datacenter / Cloud:** redes virtuales, VLANs, balanceadores, alta disponibilidad y BGP.
- **Red campus/universidad:** múltiples subredes, autenticación central, políticas y MPLS/SD-WAN entre sedes.

### 18) Mini-proyectos para practicar

1. Monta un **mapa de red** de tu casa identificando IPs y MACs.
2. Configura un **servidor DHCP** y observa DORA.
3. Levanta una máquina virtual y captura tráfico con Wireshark mientras navegas.
4. Crea dos VLANs en un switch gestionable y prueba aislamiento entre ellas.
5. Monta un servidor web simple (NGINX) y accédelo desde otra máquina.

### 19) Chuleta rápida (resumen esencial)

- IP = localización lógica; MAC = identificador físico.
- DHCP = asigna IP automáticamente.
- DNS = nombres → IP.
- Switch = entrega en LAN; Router = enruta entre redes.
- TCP = fiable; UDP = rápido.
- Subnetting: `/24` → 256 direcciones totales → 254 usables (paso por paso mostrado arriba).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**                                        | **Siguiente 7**    |
| ------------------ | -------------------------------------------------- | ------------------ |
| [🏠](../README.md) | [⏪](./1_5_Understand_Basics_of_Popular_Suites.md) | [⏩](./1_7_NFC.md) |
