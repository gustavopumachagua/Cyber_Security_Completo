| **Inicio**         | **Siguiente 2**                          |
| ------------------ | ---------------------------------------- |
| [🏠](../README.md) | [⏩](./10_2_Man_in_the_middle_attack.md) |

---

## **Índice**

| Temario                                                                                              |
| ---------------------------------------------------------------------------------------------------- |
| [298. DoS vs DDoS](#298-dos-vs-ddos)                                                                 |
| [299. ¿Qué es un ataque de denegación de servicio?](#299-qué-es-un-ataque-de-denegación-de-servicio) |
| [300. ¿Qué es un ataque DDoS?](#300-qué-es-un-ataque-ddos)                                           |

# **Denial of Service (DoS) vs Distributed Denial of Service (DDoS)**

## **298. DoS vs DDoS**

![DDoS](/img/10_Common_Attacks/DDoS.png "DDoS")

- **DoS (Denial of Service / Denegación de Servicio)**

  Es un ataque en el que un único sistema atacante intenta hacer que un servicio o recurso deje de estar disponible para los usuarios legítimos, sobrecargándolo con tráfico malicioso o explotando vulnerabilidades.

- **DDoS (Distributed Denial of Service / Denegación de Servicio Distribuida)**

  Es una versión más avanzada de un DoS. Involucra **múltiples sistemas comprometidos** (generalmente parte de una botnet) que atacan al mismo objetivo de manera simultánea, haciendo que sea más difícil de detectar, filtrar y mitigar.

👉 En pocas palabras:

- **DoS** = un atacante, un origen.
- **DDoS** = múltiples atacantes distribuidos, un objetivo común.

### Tipos de ataques DoS y DDoS

#### 1. **Ataque de lágrima (Teardrop Attack)**

- Manipula los fragmentos de paquetes IP con encabezados incorrectos o solapados.
- Hace que el sistema víctima no pueda reensamblar correctamente los paquetes, provocando fallos o reinicios.

#### 2. **Ataque de inundación (Flood Attack)**

- El atacante envía una gran cantidad de tráfico para saturar el ancho de banda o los recursos.
- Ejemplos:

  - **ICMP Flood (Ping Flood)**
  - **UDP Flood**
  - **SYN Flood**

### 3. **Ataque de fragmentación de IP**

- Similar al de lágrima, pero se centra en enviar **paquetes IP fragmentados** de manera que consuman recursos excesivos al intentar reensamblarlos.

### 4. **Ataque volumétrico**

- Busca saturar el ancho de banda de la red.
- Mide la magnitud en **Gbps** o **Mpps (millones de paquetes por segundo)**.
- Ejemplo: Amplificación DNS o NTP.

### 5. **Ataque de protocolo**

- Se aprovecha de vulnerabilidades en protocolos de red.
- Busca agotar recursos de infraestructura como firewalls, balanceadores o routers.
- Ejemplo: **SYN Flood, Smurf Attack**.

### 6. **Ataque basado en aplicaciones**

- Se centra en las capas más altas del modelo OSI (capa 7).
- Envía solicitudes legítimas en apariencia, pero en gran volumen o con intención de consumir recursos.
- Ejemplo: HTTP Flood, Slowloris.

### Preguntas frecuentes sobre ataques DoS y DDoS

#### ❓ ¿Qué es un ataque DDoS?

Es un intento malicioso de interrumpir el tráfico normal de un servidor, servicio o red mediante una **inundación masiva de tráfico desde múltiples fuentes**.

#### ❓ ¿Qué es un ataque DoS?

Es un intento de hacer que un servicio quede inutilizable enviando **grandes cantidades de tráfico o explotando vulnerabilidades** desde una sola fuente.

#### ❓ ¿Cuáles son los tipos de ataques DoS?

- Ataques de lágrima
- Inundación (Flood)
- Fragmentación de IP
- Volumétricos
- De protocolo
- Basados en aplicaciones

#### ❓ ¿Cómo funciona un ataque DoS o DDoS?

1. El atacante genera tráfico falso o malicioso.
2. El servidor o red víctima intenta procesarlo.
3. Se saturan los recursos (CPU, RAM, ancho de banda, tablas de conexión).
4. Los usuarios legítimos no pueden acceder al servicio.

#### ❓ ¿Qué es la protección y mitigación de DDoS?

Son las medidas para **detectar, filtrar y reducir el impacto** de un ataque. Incluye:

- Firewalls y sistemas IDS/IPS.
- Servicios en la nube (Cloudflare, Akamai, AWS Shield).
- Balanceo de carga y redundancia.
- Filtrado de tráfico en ISPs.
- Limitación de solicitudes (rate limiting).

---

[🔼](#índice)

---

## **299. ¿Qué es un ataque de denegación de servicio?**

Un **ataque de denegación de servicio** es cualquier acción maliciosa cuyo objetivo es **hacer que un servicio (sitio web, API, servidor, red) deje de estar disponible para sus usuarios legítimos**. Lo consigue **agotando recursos** del objetivo (ancho de banda, CPU, memoria, tablas de conexión, hilos de servidor, etc.) o explotando una vulnerabilidad que provoca caídas o bloqueos.

- **DoS (Denial of Service)**: el ataque procede de **una sola fuente** (un único host).

- **DDoS (Distributed DoS)**: el ataque viene desde **múltiples fuentes** (botnets, miles de máquinas comprometidas), lo que lo hace mucho más potente y difícil de bloquear.

### ¿Cómo funciona, técnicamente? (explicación por capas)

1. **Capa de red / volumétrica**

   - Objetivo: **saturar el ancho de banda** o la capacidad de enrutamiento del enlace de red del objetivo.
   - Efecto: el tráfico legítimo no puede llegar porque el enlace está ocupado por tráfico malicioso.

   - Ejemplo conceptual: llegar al límite de tu conexión con millones de megabits por segundo (Gbps).

2. **Capa de transporte / protocolos (TCP/UDP)**

   - Objetivo: agotar **tablas de conexión** o recursos de la pila TCP/IP en servidores o equipos intermedios.

   - Ejemplo típico: un volumen masivo de paquetes SYN (inicios de conexión TCP) que llenan la tabla de conexiones pendientes del servidor, impidiendo que nuevas conexiones legítimas se completen.

3. **Capa de aplicación (Layer 7)**

   - Objetivo: consumir CPU, memoria o accesos a base de datos por medio de **solicitudes aparentemente legítimas** (por ejemplo, muchas peticiones HTTP costosas).
   - Efecto: el servidor responde lentamente o deja de responder a usuarios reales.

4. **Amplificación / reflexión**

   - Técnica indirecta: el atacante envía peticiones pequeñas a servidores públicos (p. ej. resolvers DNS abiertos) **falsificando la IP de origen** con la IP de la víctima; esos servidores responden con mensajes mucho más grandes hacia la víctima, amplificando el volumen de tráfico (alto factor de amplificación).

> Nota: la explicación anterior es conceptual — no contiene instrucciones para llevar a cabo ataques.

### Tipos comunes (con ejemplos descriptivos)

- **SYN Flood (transporte)**

  El servidor recibe una avalancha de solicitudes para iniciar conexiones TCP (SYN). Si muchos inicios no se completan, la tabla de conexiones se llena y nuevas conexiones legítimas son rechazadas o retrasadas.

- **ICMP/UDP Flood (volumétricos)**

  Envío masivo de paquetes ICMP (ping) o UDP para saturar ancho de banda o recursos de proceso en la víctima.

- **Amplificación (p. ej. DNS, NTP, Memcached)**

  Un atacante envía consultas pequeñas a servidores que responden con paquetes mucho mayores; como la IP de origen está falsificada, la respuesta se dirige a la víctima, multiplicando el tráfico. (Ej.: consultas a servidores DNS abiertos que devuelven respuestas grandes).

- **Slowloris (aplicación / capa 7)**

  Mantener abiertas muchas conexiones HTTP enviando encabezados incompletos muy despacio; el servidor reserva recursos esperando completar la petición y se queda sin hilos/conexiones para usuarios legítimos.

- **HTTP Flood (aplicación)**

  Millones de peticiones HTTP GET/POST legítimas (simulando navegadores reales) dirigidas a páginas que consumen CPU o realizan consultas de base de datos; aunque cada petición sea válida, el volumen hace caer el servicio.

- **Fragmentación / Teardrop (explotación de reensamblado de IP)**

  Envío de fragmentos IP malformados o solapados que producen errores al reensamblar paquetes en la pila de red de la víctima (esto ha sido más común en sistemas antiguos).

### Ejemplo narrativo (escenario típico, sin instrucciones técnicas)

1. Un atacante controla una red de dispositivos comprometidos (una botnet).
2. Decide derribar la web de una tienda en línea con un DDoS a horas pico.
3. Ordena a los bots que envíen millones de peticiones HTTP hacia la página de pago; muchas de esas peticiones hacen consultas a la base de datos.
4. El servidor empieza a consumir toda la CPU y las conexiones disponibles; la cola de peticiones legítimas crece; los usuarios obtienen timeouts y no pueden comprar.
5. La empresa detecta tráfico inusual, contacta al proveedor de red y activa mitigación (filtrado, red de distribución — CDN —, scrubbing), y el servicio se restaura parcialmente.

### Señales de que podrías estar sufriendo un ataque DoS/DDoS

- Aumento repentino y sostenido del tráfico (Gbps o pps) sin causa legítima.
- Muchas solicitudes incompletas (p. ej. muchos SYN sin ACK posteriores).
- Alta latencia y timeouts frecuentes en servicios que antes iban rápido.
- Registros del servidor con errores 503 (servicio no disponible), colas saturadas, o procesos que consumen 100% CPU.
- Usuarios distribuidos reportando inaccesibilidad, no solo desde una zona geográfica.

### Impacto típico

- **Económico:** pérdida de ventas, coste de mitigación, penalizaciones (SLAs).
- **Reputacional:** clientes pierden confianza.
- **Operativo:** equipos de TI consumidos en contención; servicios críticos pueden verse afectados.
- **Colateral:** medidas de mitigación agresivas (bloquear rangos IP, “blackholing”) pueden afectar a usuarios legítimos.

### Cómo se detecta y mitiga (resumen, defensivo)

**Detección**

- Monitorización continua de tráfico y alertas sobre picos inusuales.
- Análisis de patrones (ej.: muchos SYNs sin ACKs, picos de peticiones desde muchas IPs).
- Uso de Netflow/telemetría y herramientas de observabilidad.

**Mitigación (técnicas defensivas, alto nivel)**

- **Rate limiting**: limitar peticiones por IP o por sesión.
- **Filtrado en la frontera (ISP)**: bloqueo o filtrado de tráfico malicioso en los puntos de tránsito.
- **SYN cookies**: mitigar SYN flood sin consumir memoria en la tabla de conexiones.
- **Redes de entrega (CDN) y Anycast**: distribuir y absorber tráfico en muchos puntos.
- **Servicios de "scrubbing" o mitigación en nube**: desviar tráfico a centros especializados que filtran el tráfico malicioso.
- **WAF (firewall de aplicaciones web)**: bloquear patrones de capa 7 sospechosos.
- **Redundancia y escalado**: diseñar infraestructura escalable y con failover.
- **Cerrar y asegurar servicios amplificadores**: configurar correctamente servidores DNS/NTP para evitar su uso en amplificación.

> Importante: la mitigación efectiva suele requerir coordinación con el proveedor de conectividad (ISP) y, en muchos casos, con un proveedor de mitigación profesional.

### Aspecto legal y ético

- Lanzar un DoS o DDoS contra sistemas sin permiso es **ilegal** en la mayoría de jurisdicciones y puede conllevar sanciones penales y civiles.

---

[🔼](#índice)

---

## **300. ¿Qué es un ataque DDoS?**

**Definición breve:** Un **DDoS (Distributed Denial of Service)** es un ataque cuyo objetivo es **hacer indisponible un servicio, servidor o red** saturando sus recursos (ancho de banda, conexiones, CPU, memoria, etc.) mediante **tráfico masivo o solicitudes maliciosas** originadas desde **múltiples fuentes** simultáneas. En la práctica los atacantes usan máquinas comprometidas (botnets) o servicios mal configurados para generar ese tráfico distribuido.

### Componentes básicos — cómo funciona a alto nivel (sin instrucciones para atacar)

1. **Fuentes distribuidas**: en un DDoS las solicitudes provienen de muchas direcciones IP distintas (botnets, servidores en la nube, dispositivos IoT comprometidos). Eso complica bloquear por IP.
2. **Vector(es) del ataque**: el atacante elige el _cómo_ (volumen de datos, tipo de paquete o solicitudes de aplicación). Pueden combinarse varios vectores a la vez (ataque _multi-vector_).
3. **Amplificación / reflexión**: técnica común donde el atacante envía peticiones pequeñas a servidores que responderán con respuestas mucho mayores **falsificando la IP de origen** (la víctima recibe la respuesta masiva). Esto aumenta el volumen sin necesitar tantos bots. Ejemplos famosos usan servicios UDP mal configurados (DNS, NTP, Memcached, etc.).
4. **Objetivo**: agotar uno o varios recursos (ancho de banda, tablas de conexión TCP, hilos de servidor, CPU/DB en la capa aplicación) para impedir que usuarios legítimos usen el servicio.

> Nota: **no** voy a dar instrucciones sobre cómo montar un ataque. Eso es ilegal y peligroso. Aquí el enfoque es técnico y defensivo.

### Tipos principales de DDoS (con ejemplos conceptuales)

#### 1) **Volumétricos (floods)**

- **Qué buscan:** saturar la capacidad de enlace (Mbps/Gbps/Tbps) o la capacidad de encaminamiento.
- **Ejemplos conceptuales:** ICMP/UDP floods; grandes ataques UDP/UDP reflection.
- **Mitigación típica:** absorbción por CDN/anycast, filtrar en los puntos de peering, colaboración con ISP/proveedor de mitigación.

#### 2) **De protocolo (state-exhaustion)**

- **Qué buscan:** agotar tablas de conexión o recursos en dispositivos de red (firewalls, load-balancers, servidores).
- **Ejemplo conceptual:** **SYN flood** — muchos inicios de handshake TCP que no se completan y ocupan slots en la tabla de conexiones.
- **Mitigación típica:** SYN cookies, ajuste de timeouts, protección TCP avanzada.

#### 3) **De aplicación (Layer 7)**

- **Qué buscan:** simular tráfico aparentemente legítimo a operaciones costosas (páginas dinámicas, búsquedas, endpoints de API) para agotar CPU, hilos o conexiones de aplicación.
- **Ejemplos conceptuales:** HTTP floods, peticiones que disparan consultas DB intensas, ataques tipo _Slowloris_ que mantienen conexiones abiertas.
- **Mitigación típica:** WAF, rate limiting por sesión/por ruta, challenge (CAPTCHA), caching y arquitecturas escalables.

#### 4) **Amplificación / reflexión**

- **Qué buscan:** multiplicar el volumen enviado a la víctima usando servidores intermedios (servidores DNS abiertos, NTP, memcached, etc.). No siempre requieren una gran botnet porque la “amplificación” hace el trabajo.
- **Ejemplo real:** el ataque que alcanzó \~1.35 Tbps dirigido a GitHub en 2018 usó amplificación memcached. GitHub describió ese incidente y cómo fue mitigado.

### Casos reales (resumen y lecciones)

- **Estonia (abril-mayo 2007):** una serie de DDoS coordinados afectaron sitios de gobierno, bancos y medios, y marcaron un antes-después en la concienciación sobre ciberdefensa para infraestructuras críticas.
- **Dyn / Mirai (octubre 2016):** la botnet _Mirai_ comprometió miles de dispositivos IoT (cámaras, DVRs con credenciales por defecto) y provocó interrupciones en sitios muy conocidos al atacar el proveedor DNS Dyn. Fue un ejemplo claro del riesgo que suponen IoT inseguros.
- **GitHub (febrero 2018):** GitHub sufrió un pico de \~1.35 Tbps usando amplificación memcached; fue breve pero mostró el potencial de servidores mal configurados para amplificar ataques.

### Señales y detección (qué observar como defensor)

- Picos repentinos e inusuales en tráfico (Gbps o pps), aumento de errores 5xx, latencias extremas.
- Muchas conexiones “a medio abrir” (ej.: muchas SYN sin ACK) o conexiones lentas que consumen hilos.
- Tráfico proveniente de una gran cantidad de ASNs/geografías sin patrón de cliente real.
- Cambios bruscos en NetFlow, picos en paquetes por segundo (pps) en routers de borde.

  Herramientas de telemetría, NetFlow/IPFIX y soluciones de detección en la nube ayudan a identificar estas señales.

### Mitigación (alto nivel — defensivo)

- **Prevención en diseño:** redundancia, escalado horizontal, CDNs/Anycast para distribuir carga y evitar puntos únicos de fallo.
- **Rate limiting & WAF:** limitar solicitudes por IP/por sesión y filtrar patrones maliciosos en capa 7. OWASP recomienda políticas de rate limiting y límites de recursos.
- **Protección en capa de red:** colaboración con ISP, filtros en peering, soluciones de “scrubbing” en la nube que absorben y limpian tráfico volumétrico.
- **Protecciones específicas:** SYN cookies / hardening TCP, cerrar servicios UDP innecesarios, configurar correctamente servidores para evitar que se usen como amplificadores.
- **Preparación + playbooks:** pruebas periódicas autorizadas, planes de comunicación, monitorización continua y ejercicios con el proveedor de mitigación.

### Para un estudiante de _hacking ético_ — cómo aprender de forma legal y segura

1. **No realices pruebas contra sistemas que no te pertenecen ni tengas permiso explícito.** Eso es ilegal.
2. **Montar un laboratorio controlado:** crea VMs/containers aislados en una red privada o usa entornos de laboratorio (local o en una cuenta cloud personal con recursos privados). En ese entorno puedes experimentar con carga y comprobar cómo reacciona un servidor.
3. **Simulación y pruebas autorizadas:** para aprender sobre mitigación y detección, pídele permiso al propietario del sistema o utiliza servicios de pruebas autorizadas (proveedores de mitigación suelen ofrecer laboratorios y pruebas controladas).
4. **Estudia post-mortems y documentación de defensores:** leer informes y análisis (ej.: GitHub incident report, Cloudflare learning center, OWASP cheat sheets, y los informes de Akamai/NETSCOUT) te da contexto técnico y defensivo sin caer en mal uso.

### Recursos recomendados (lectura)

- Cloudflare — “What is a DDoS attack” / Learning Center.
- GitHub — informe del incidente memcached (postmortem).
- OWASP — _Denial of Service Cheat Sheet_ (mitigación, rate limiting).
- Artículos técnicos y análisis: KrebsOnSecurity sobre Mirai (Dyn 2016) y reportes de proveedores (Akamai, NETSCOUT) para tendencias.

### Resumen — puntos clave

- Un **DDoS** usa **múltiples fuentes** para negar servicio a la víctima saturando recursos.
- Hay **vectores volumétricos, de protocolo y de aplicación**, y la **amplificación** permite generar enorme tráfico desde recursos ajenos.
- Los incidentes famosos (Estonia 2007, Dyn/Mirai 2016, GitHub 2018) muestran la evolución y el impacto real de la amenaza.
- Aprende **defensa** y **detección**, usa laboratorios controlados y nunca pruebes ataques contra sistemas sin autorización.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**                          |
| ------------------ | ---------------------------------------- |
| [🏠](../README.md) | [⏩](./10_2_Man_in_the_middle_attack.md) |
