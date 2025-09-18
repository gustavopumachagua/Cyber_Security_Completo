| **Inicio**         | **atrás 7**        | **Siguiente 9**   |
| ------------------ | ------------------ | ----------------- |
| [🏠](../README.md) | [⏪](./4_7_DMZ.md) | [⏩](./4_9_VM.md) |

---

## **Índice**

| Temario                                                                                                        |
| -------------------------------------------------------------------------------------------------------------- |
| [89. ¿Qué es el Protocolo de Resolución de Direcciones?](#89-qué-es-el-protocolo-de-resolución-de-direcciones) |
| [90. ARP explicado](#90-arp-explicado)                                                                         |

# **ARP**

## **89. ¿Qué es el Protocolo de Resolución de Direcciones?**

![ARP](/img/4_IP_Terminology/ARP.png "ARP")

### 1) Significado del Protocolo de Resolución de Direcciones (ARP)

El **Protocolo de Resolución de Direcciones (ARP)** es un protocolo de red usado en IPv4 que **traduce direcciones IP (lógicas) en direcciones MAC (físicas)** dentro de una red local (LAN).

📌 En palabras simples:

- Una computadora conoce la **IP de destino** (ej. 192.168.1.10).
- Para enviar datos en la LAN, necesita la **dirección MAC** (ej. AA\:BB\:CC\:DD\:EE\:FF).
- ARP responde a la pregunta: _“¿Qué dirección MAC corresponde a esta dirección IP?”_

### 2) ¿Qué hace ARP y cómo funciona?

Cuando un dispositivo quiere comunicarse con otro en la LAN:

1. **Consulta su caché ARP** (tabla ARP) → donde guarda correspondencias IP ↔ MAC aprendidas recientemente.
2. Si no encuentra la IP:

   - Envía un **ARP Request** → broadcast a toda la red local preguntando: _“¿Quién tiene la IP 192.168.1.10?”_
   - El host con esa IP responde con un **ARP Reply** → _“Yo, 192.168.1.10, tengo la MAC AA\:BB\:CC\:DD\:EE\:FF”_.

3. El dispositivo guarda la respuesta en la caché ARP y envía los datos.

📌 Ejemplo:

- PC1 (192.168.1.2) quiere hablar con PC2 (192.168.1.10).
- PC1 envía un ARP Request: "¿Quién tiene 192.168.1.10?".
- PC2 responde con su MAC: "Yo tengo 192.168.1.10 y mi MAC es AA\:BB\:CC\:DD\:EE\:FF".
- Ahora PC1 puede enviar paquetes directamente a esa MAC.

### 3) Relación de ARP con DHCP y DNS

- **ARP**: Traduce **IP ↔ MAC** en la red local.
- **DHCP**: Asigna **IP ↔ MAC** dinámicamente dentro de una red (quién obtiene qué IP).
- **DNS**: Traduce **nombre ↔ IP** (ej. [www.google.com](http://www.google.com) → 142.250.72.206).

📌 Diferencias:

- **ARP = nivel de enlace (Capa 2/3)** → resuelve IP a MAC dentro de la LAN.
- **DHCP = configuración (Capa de red/gestión)** → asigna direcciones IP dinámicas a dispositivos.
- **DNS = nombres (Capa de aplicación)** → convierte nombres legibles en direcciones IP.

En conjunto:

- Un equipo obtiene IP con DHCP.
- Consulta DNS para convertir un dominio en IP.
- Finalmente usa ARP para averiguar qué MAC corresponde a esa IP en la LAN.

### 4) Tipos clave de ARP

#### 🔹 1. Proxy ARP

Un router responde en nombre de otro host.
Ejemplo:

- Host A y Host B están en redes distintas, pero A no tiene gateway configurado.
- El router responde al ARP de A como si fuera B, reenviando después el tráfico.

**Uso**: conectar redes sin cambiar configuraciones de los hosts.

#### 🔹 2. ARP Gratuito (Gratuitous ARP)

Un host envía un ARP request para su **propia IP** sin que nadie lo haya pedido.
Ejemplo:

- Al arrancar, un servidor manda ARP gratuito para anunciar su IP y detectar conflictos (si alguien responde, hay duplicidad).
- También se usa para actualizar cachés ARP en switches y otros hosts.

#### 🔹 3. ARP Inverso (RARP - Reverse ARP)

Traduce lo contrario: de **MAC → IP**.

- Usado por hosts antiguos o dispositivos sin disco que sabían su MAC pero no su IP.
- Hoy reemplazado por DHCP.

#### 🔹 4. ARP Inverso (IARP - Inverse ARP)

(Se usa en redes WAN como Frame Relay o ATM).

- Permite a un dispositivo conocer qué **IP corresponde a un identificador de capa 2 (DLCI, por ejemplo)**.
- Menos común en redes modernas, pero útil en enlaces punto a punto.

### 5) ¿Para qué es útil ARP en redes?

- Comunicación dentro de la LAN (fundamental para que funcione IPv4).
- Administración de tablas ARP para optimizar el tráfico.
- Resolución rápida de direcciones sin intervención humana.
- Soporte de protocolos de seguridad/redes (VPNs, routing dinámico).

### 6) Peligros de la suplantación de ARP: ataques y riesgos de seguridad

Como ARP no tiene autenticación, es **vulnerable a ataques de spoofing**.

#### 🔹 1. Ataques de intermediario (MITM)

Un atacante envía respuestas ARP falsas para asociar su MAC con la IP del gateway o de otra máquina.
➡ Resultado: todo el tráfico pasa por el atacante antes de llegar al destino.

#### 🔹 2. Ataques de denegación de servicio (DoS)

El atacante envía ARP falsos saturando la tabla ARP de los dispositivos, impidiendo la comunicación.

#### 🔹 3. Secuestro de sesión

Si un atacante logra interceptar tráfico (ej. ARP spoofing), puede robar cookies, credenciales o datos de sesiones activas.

### 7) Preguntas frecuentes sobre ARP

**🔹 ¿ARP existe en IPv6?**
No. IPv6 usa **NDP (Neighbor Discovery Protocol)** en lugar de ARP.

**🔹 ¿Cómo veo la tabla ARP en un dispositivo?**

- En Windows: `arp -a`
- En Linux: `ip neigh show`

**🔹 ¿Se puede proteger contra ARP Spoofing?**

Sí:

- Usar **ARP estático** en entornos críticos.
- Implementar **DHCP Snooping + Dynamic ARP Inspection (DAI)** en switches gestionables.
- Usar cifrado (HTTPS, VPNs) para que aunque haya MITM, los datos no estén en claro.

**🔹 ¿Cuánto tiempo vive una entrada ARP en caché?**
Depende del SO/dispositivo. En Windows típicamente 2-10 minutos, en routers puede ser configurable.

✅ **Conclusión**: ARP es un protocolo fundamental en redes IPv4 para mapear IPs a MACs. Sin él, los hosts no podrían comunicarse en una LAN. Sin embargo, debido a que no tiene autenticación, es blanco frecuente de ataques, por lo que es vital proteger la red con medidas de seguridad en switches, firewalls y cifrado.

---

[🔼](#índice)

---

## **90. ARP explicado**

### 1) ¿Qué es ARP?

**ARP** (Protocolo de Resolución de Direcciones) es el mecanismo usado en IPv4 para **traducir una dirección IP (nivel 3) en una dirección MAC (nivel 2)** dentro del mismo dominio de difusión (misma LAN/VLAN). Fue definido originalmente en RFC 826.

En una LAN Ethernet, cuando un host sabe la IP de destino pero no su MAC, usa ARP para averiguarla antes de enviar la trama Ethernet.

### 2) ¿Qué hace ARP y cómo funciona? (paso a paso + paquete)

**Flujo básico (ejemplo):**

- PC-A tiene IP `192.168.1.10`, MAC `aa:aa:aa:aa:aa:aa`.
- PC-B tiene IP `192.168.1.20`, MAC `bb:bb:bb:bb:bb:bb`.
- PC-A quiere enviar a `192.168.1.20`: mira su caché ARP → no encuentra entrada.
- PC-A envía un **ARP Request** en broadcast: `¿Quién tiene 192.168.1.20?` (dst MAC = `ff:ff:ff:ff:ff:ff`).
- PC-B responde con **ARP Reply** no broadcast: “yo `192.168.1.20` → MAC `bb:bb:bb:bb:bb:bb`”.
- PC-A guarda la entrada en su **tabla ARP** y envía la trama Ethernet a la MAC de PC-B.

**Campos clave del mensaje ARP** (simplificado):

- Hardware type (p. ej. Ethernet)
- Protocol type (p. ej. IPv4)
- Hardware size, protocol size
- Opcode: request (1) / reply (2)
- Sender MAC, sender IP, target MAC, target IP

**Trama Ethernet**:

- ARP Request → Ethernet dst `ff:ff:ff:ff:ff:ff` (broadcast) + Ethertype `0x0806` (ARP).
- ARP Reply → Ethernet dst = MAC origen del requester (no broadcast).

### 3) La tabla ARP (cache), envejecimiento y gestión

- Los sistemas mantienen una **caché ARP** (IP ↔ MAC) para evitar preguntar continuamente.
- Las entradas son **temporales** (aging): duran minutos y se renuevan cuando se usan.
- **Comandos útiles**:

  - Windows: `arp -a` (ver), `arp -s 192.168.1.20 00-11-22-33-44-55` (añadir estático), `arp -d *` (borrar).
  - Linux: `ip neigh show` o `arp -n`, borrar con `sudo ip neigh flush all`, añadir permanente con `sudo ip neigh add 192.168.1.20 lladdr 00:11:22:33:44:55 dev eth0 nud permanent`.
  - Cisco: `show ip arp` (ver), `clear ip arp` (borrar), `arp <ip> <mac> ARPA` (añadir estática).

### 4) Relación con DHCP y DNS — ¿en qué se diferencian?

- **DNS**: resuelve _nombre → IP_ (ej. `www.ejemplo.com` → `93.184.216.34`).
- **DHCP**: asigna _IP → host_ (gestiona la asignación dinámica de direcciones IP a clientes, típicamente enlazando una MAC con una IP durante el lease).
- **ARP**: resuelve _IP → MAC_ **dentro de la LAN**, para permitir la entrega a nivel de enlace.

Flujo conjunto típico: un equipo obtiene IP por DHCP → resuelve nombres vía DNS → para enviar a una IP dentro de la LAN consulta ARP para obtener la MAC.

### 5) 4 tipos clave / variantes de ARP (con ejemplos)

#### 1) **ARP normal (request/reply)**

El caso básico que ya vimos.

#### 2) **Proxy ARP**

Un router responde a ARP en nombre de otra máquina.

**Ejemplo**: dos subredes conectadas por un router; un host sin configuración de gateway hace un ARP y el router responde “yo tengo esa IP” y reenvía. Se usa para compatibilidad, pero puede complicar el enrutamiento y la seguridad.

#### 3) **Gratuitous ARP (ARP gratuito)**

Un host envía un ARP _anunciando su propia IP_ (sin que nadie lo solicitara).
Usos:

- Detectar conflictos de IP (si otro responde, hay duplicidad).
- Actualizar tablas ARP de otros switches/hosts tras un failover (p. ej. cuando un VM se mueve) para que los demás aprendan la nueva asociación IP→MAC.

#### 4) **RARP (Reverse ARP) e InARP (Inverse ARP)**

- **RARP**: traducía _MAC → IP_ para estaciones sin disco en los primeros días (hoy obsoleto; DHCP/BOOTP lo reemplazaron).
- **InARP** (o IARP): usado en algunos enlaces WAN (ej. Frame Relay) para mapear identificadores de capa 2 (DLCI) a IPs; menos común en redes modernas.

### 6) ¿Para qué es útil ARP en redes?

- Es **fundamental**: sin ARP no hay comunicación IPv4 en una LAN física basada en Ethernet.
- Permite descubrimiento dinámico de direcciones MAC.
- Facilita movilidad (VMs, failover) cuando se combina con gratuitous ARP.
- Es la base para funciones avanzadas de seguridad de switches (DAI, DHCP snooping, IP source guard).

### 7) Peligros: suplantación de ARP (ARP spoofing) — ataques y riesgos

> ARP **no incluye autenticación**; por eso es susceptible a suplantación (spoofing). Esto permite varios ataques.

**Ataques comunes (concepto, no instrucciones):**

- **Man-in-the-middle (MITM)**: un atacante envía ARP replies falsos para asociar _su_ MAC con la IP del gateway (o de otra víctima). El tráfico pasa por el atacante, que puede espiar/modificar/registrar paquetes. Esto es un vector clásico para MITM.
- **DoS por ARP**: inundar la red con ARP falsos o entradas inválidas para saturar tablas ARP o confundir encaminamiento local.
- **Secuestro de sesión / robo de credenciales**: con MITM un atacante puede capturar credenciales no encriptadas (HTTP, FTP), cookies, tokens de sesión, etc.

**Importante de seguridad**: describir el _concepto_ de estos ataques es legítimo para defensa, pero **no** proporcionaré instrucciones paso-a-pas ni comandos que habiliten el ataque en entornos reales. Si necesitas practicar defensa, hazlo sólo en laboratorios controlados y aislados.

### 8) Detección y mitigación (cómo defenderse)

**Principios**: minimizar confianza en ARP, validar bindings IP↔MAC, cifrar tráfico sensible.

#### Técnicas y controles (prácticos):

- **DAI (Dynamic ARP Inspection)** — valida ARP entrantes usando una base de bindings (normalmente la tabla de DHCP snooping). Si un paquete ARP no coincide con la asociación conocida lo descarta. Muy efectivo en switches gestionables.
- **DHCP Snooping** — crear una base de datos confiable IP↔MAC↔puerto; DAI usa esa base.
- **IP Source Guard** — evita que una interfaz envíe tráfico con IP distinta a la registrada (usa bindings de DHCP snooping).
- **Port security / 802.1X** — limita/autoriza qué MACs pueden aparecer en cada puerto.
- **Entradas ARP estáticas** en hosts críticos (solo en casos puntuales; poco escalable).
- **Cifrado de capa aplicación/transporte**: usar HTTPS, SSH, VPNs — ¡si hay MITM, los datos siguen cifrados!
- **Monitoreo y detección**: herramientas como `arpwatch`, sistemas IDS y revisar picos inusuales de ARP.
- **Segmentación**: separar redes (VLANs) y no permitir demasiada promiscuidad en la capa 2.

> Ejemplo (comandos defensivos en switches Cisco — esquema):
>
> - Habilitar DHCP snooping:
>
> ```text
> ip dhcp snooping
> ip dhcp snooping vlan 10
> interface GigabitEthernet1/0/1
>  ip dhcp snooping trust
> ```
>
> - Habilitar DAI para una VLAN:
>
> ```text
> ip arp inspection vlan 10
> ```
>
> _(la sintaxis exacta depende de la plataforma/IOS — documenta y prueba en tu equipo)._

### 9) Cómo detectar indicios de ARP spoofing (comprobaciones rápidas)

- Revisar caché ARP (`arp -a` / `ip neigh show`) y buscar **dos IPs con la misma MAC** o una IP con MAC que no coincide con inventario.
- Monitorizar ARP en la interfaz (ej.: `tcpdump -n -i eth0 arp` para observar solicitudes/respuestas frecuentes) — para detección, no para ataque.
- Alertas en IDS/SIEM por ARP inusuales o por muchos broadcasts ARP.
- `arpwatch` y otras herramientas para notificar cambios en bindings.

### 10) Buenas prácticas resumidas

- Habilita **DHCP snooping + DAI + IP Source Guard** en switches gestionables.
- Usa **802.1X** donde sea posible.
- Minimiza servicios no cifrados en la red.
- Bloquea puertos no usados y aplica **port security**.
- Documenta IP↔MAC importantes y considera entradas ARP estáticas donde sea necesario.
- Realiza pentesting y escaneos periódicos en entorno controlado para validar protecciones.

### 11) Mini-cheat-sheet: comandos útiles (ver / borrar / añadir)

**Windows**

- Ver: `arp -a`
- Añadir estático: `arp -s 192.168.1.20 00-11-22-33-44-55`
- Borrar: `arp -d 192.168.1.20` o `arp -d *`

**Linux**

- Ver: `ip neigh show` / `arp -n`
- Añadir permanente: `sudo ip neigh add 192.168.1.20 lladdr 00:11:22:33:44:55 dev eth0 nud permanent`
- Borrar: `sudo ip neigh flush all` (o `ip neigh del ...`)

**Cisco**

- Ver ARP: `show ip arp`
- Borrar: `clear ip arp`
- Habilitar DHCP snooping / DAI: ver sección anterior (sintaxis según plataforma).

### 12) FAQ rápido

- **¿ARP funciona en IPv6?** No — IPv6 usa **Neighbor Discovery Protocol (NDP)** (ICMPv6) para las funciones que ARP hacía en IPv4.
- **¿Puedo desactivar ARP?** No si quieres comunicación IPv4 en la LAN; puedes mitigar riesgos con controles de switch y cifrado.
- **¿RARP sigue usándose?** No, fue reemplazado por BOOTP/DHCP.
- **¿Cómo saber si alguien hace MITM por ARP?** Busca cambios bruscos en la tabla ARP, latencia inusual, duplicidad de MACs o alertas del IDS.

### 13) Conclusión breve

ARP es un protocolo **simple y esencial** para la comunicación IPv4 en LANs, pero su **ausencia de autenticación** lo hace un vector atractivo para ataques de suplantación. La defensa real combina **control a nivel de switch (DAI/DHCP snooping/IP Source Guard/port security)** y **cifrado de tráfico** (HTTPS, VPN) para minimizar impacto incluso si aparece un MITM.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**        | **Siguiente 9**   |
| ------------------ | ------------------ | ----------------- |
| [🏠](../README.md) | [⏪](./4_7_DMZ.md) | [⏩](./4_9_VM.md) |
