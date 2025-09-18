| **Inicio**         | **atrás 2**                             | **Siguiente 4**                            |
| ------------------ | --------------------------------------- | ------------------------------------------ |
| [🏠](../README.md) | [⏪](./3_2_Understand_the_OSI_model.md) | [⏩](./3_4_Common_Ports_and_their_Uses.md) |

---

## **Índice**

| Temario                                                                                                  |
| -------------------------------------------------------------------------------------------------------- |
| [65. 12 protocolos de red comunes](#65-12-protocolos-de-red-comunes)                                     |
| [66. ¡Redes para hackers! (Protocolos de red comunes)](#66-redes-para-hackers-protocolos-de-red-comunes) |

---

# **Protocolos comunes y sus usos**

## **65. 12 protocolos de red comunes**

![protocolos de red comunes](/img/3_Networking_Knowledge/protocolos_de_red_comunes.jpg "protocolos de red comunes")

### **Protocolos de la Capa de Aplicación**

La **capa de aplicación** (OSI capa 7 / TCP-IP capa 4) es donde los usuarios interactúan directamente con servicios y aplicaciones.

1. **Sistema de Nombres de Dominio (DNS)**

   - Traduce nombres de dominio legibles (ej. `google.com`) a direcciones IP (ej. `142.250.190.14`).
   - Funciona como la "agenda telefónica" de Internet.
   - Ejemplo: Cuando escribes `youtube.com`, tu PC consulta un servidor DNS para saber a qué IP conectarse.

2. **Protocolo de Configuración Dinámica de Host (DHCP)**

   - Asigna direcciones IP dinámicas a dispositivos dentro de una red.
   - Ahorra configurar IP manualmente.
   - Ejemplo: Cuando conectas tu laptop al WiFi del café, el router le da automáticamente una IP.

3. **Protocolo de Transferencia de Archivos (FTP)**

   - Permite transferir archivos entre computadoras en una red.
   - Usa puertos **20 y 21**.
   - Ejemplo: Subir páginas web a un servidor mediante FileZilla.

4. **Protocolo de Transferencia de Hipertexto (HTTP/HTTPS)**

   - Es el protocolo base de la **World Wide Web**.
   - HTTP (puerto 80) transmite páginas web.
   - HTTPS (puerto 443) hace lo mismo pero cifrado con TLS.
   - Ejemplo: Cada vez que abres una página web en tu navegador.

5. **Protocolo Simple de Transferencia de Correo (SMTP)**

   - Se usa para **enviar correos electrónicos**.
   - Normalmente usa el puerto **25** o **587**.
   - Ejemplo: Cuando Outlook envía tu email, usa SMTP para mandarlo al servidor de correo.

6. **Protocolo Simple de Administración de Red (SNMP)**

   - Permite a los administradores monitorear y gestionar dispositivos de red (routers, switches, servidores).
   - Ejemplo: Un administrador ve el uso de CPU y tráfico de un router desde su consola central.

7. **Secure Shell (SSH)**

   - Protocolo seguro para acceder de forma remota a otro dispositivo.
   - Cifra la comunicación.
   - Puerto **22**.
   - Ejemplo: Conectarte a un servidor Linux en AWS con `ssh user@ip-server`.

8. **Telnet**

   - Protocolo para acceder remotamente a otro equipo, pero **sin cifrado**.
   - Puerto **23**.
   - Fue reemplazado por SSH por motivos de seguridad.
   - Ejemplo: Administradores antes usaban Telnet para configurar routers.

### **Protocolos de la Capa de Transporte**

En el modelo OSI (capa 4), se encargan de la **comunicación extremo a extremo** entre aplicaciones.

1. **Protocolo de Control de Transmisión (TCP)**

   - Orientado a conexión.
   - Garantiza entrega **fiable** de datos, en orden y sin pérdidas.
   - Ejemplo: Descargas de archivos, emails, navegación web.

2. **Protocolo de Datagramas de Usuario (UDP)**

   - No orientado a conexión.
   - Entrega rápida pero sin garantizar fiabilidad.
   - Ejemplo: Streaming de video, juegos online, llamadas VoIP.

### **Protocolos de la Capa de Internet**

Equivalente a la capa 3 en OSI, se encargan del **direccionamiento y enrutamiento**.

1. **Protocolo de Resolución de Direcciones (ARP)**

   - Traduce direcciones IP a direcciones MAC (necesarias para la comunicación en una LAN).
   - Ejemplo: Tu PC quiere enviar un paquete a `192.168.1.10`, primero pregunta “¿qué MAC tiene esta IP?”.

2. **Protocolo de Mensajes de Control de Internet (ICMP)**

   - Se usa para enviar mensajes de error y diagnóstico.
   - Ejemplo: El comando `ping` usa ICMP para verificar si un host está disponible.

3. **Protocolo de Internet (IP)**

   - Se encarga de direccionar y enrutar paquetes entre dispositivos.
   - Versiones: IPv4 (32 bits), IPv6 (128 bits).
   - Ejemplo: Tu PC con IP `192.168.1.5` se comunica con un servidor IP público en `8.8.8.8`.

### **Otros Protocolos Especializados**

Algunos cumplen funciones específicas en redes más avanzadas:

1. **Protocolo de Acceso Fronterizo (BGP, Border Gateway Protocol)**

   - Usado entre **proveedores de Internet (ISPs)** para intercambiar rutas.
   - Permite que Internet funcione globalmente.
   - Ejemplo: Cuando tu ISP enruta tráfico hacia otra red internacional.

2. **Abra primero el camino más corto (OSPF, Open Shortest Path First)**

   - Es un protocolo de enrutamiento dinámico interno (IGP).
   - Usa el **algoritmo de Dijkstra** para calcular la ruta más corta entre routers.
   - Ejemplo: En una empresa grande, los routers OSPF eligen automáticamente la mejor ruta para enviar datos.

### 📌 Resumen Rápido en Tabla

| Capa (OSI)    | Protocolo  | Función Principal               | Ejemplo de uso                   |
| ------------- | ---------- | ------------------------------- | -------------------------------- |
| Aplicación    | DNS        | Resolver nombres a IP           | `google.com → 142.250.190.14`    |
| Aplicación    | DHCP       | Asignar IP automática           | Laptop conectada a WiFi          |
| Aplicación    | FTP        | Transferir archivos             | Subir web a un servidor          |
| Aplicación    | HTTP/HTTPS | Navegar web                     | Abrir `https://youtube.com`      |
| Aplicación    | SMTP       | Enviar emails                   | Outlook enviando correo          |
| Aplicación    | SNMP       | Monitoreo de red                | Ver tráfico de un router         |
| Aplicación    | SSH        | Acceso remoto seguro            | `ssh user@server`                |
| Aplicación    | Telnet     | Acceso remoto inseguro          | Configuración antigua de routers |
| Transporte    | TCP        | Comunicación fiable             | Descarga de archivos             |
| Transporte    | UDP        | Comunicación rápida             | Streaming, gaming                |
| Internet      | ARP        | IP ↔ MAC                        | PC encuentra MAC de otra IP      |
| Internet      | ICMP       | Diagnóstico/red                 | `ping 8.8.8.8`                   |
| Internet      | IP         | Direccionamiento y enrutamiento | Conexión a un servidor           |
| Especializado | BGP        | Enrutamiento global             | ISP intercambiando rutas         |
| Especializado | OSPF       | Ruta más corta en LAN/WAN       | Routers en empresa               |

---

[🔼](#índice)

---

## **66. ¡Redes para hackers! (Protocolos de red comunes)**

### 1) Conceptos rápidos que conviene tener claros

- **Paquetes → Tramas → Bits**: los datos se encapsulan en cada capa (aplicación → transporte → red → enlace → física).
- **Puertos**: identifican servicios en la capa de transporte (ej. 80 HTTP, 443 HTTPS, 22 SSH).
- **Direcciones**: IP (nivel red) y MAC (nivel enlace).
- **Estado vs. Stateless**: protocolos como TCP mantienen estado (handshake, sesión), UDP no.

### 2) Protocolos clave (qué son, ejemplo de uso, abuso típico y defensa)

> Para cada protocolo: **Qué es — Cómo funciona (breve) — Ejemplo legítimo — Abuso típico (alto nivel) — Medidas de defensa / detección**.

#### DNS (Domain Name System)

- **Qué es:** Servicio que traduce nombres (ej. `example.com`) a direcciones IP.
- **Cómo funciona:** consultas recursivas/iterativas a servidores DNS; cache en resolver.
- **Ejemplo legítimo:** tu navegador resuelve `google.com` antes de conectar.
- **Abuso (alto nivel):** tunneling/exfiltración de datos por canales DNS, envenenamiento de cache DNS (cache poisoning), uso de dominios maliciosos.
- **Defensa/detección:** DNSSEC, filtrado de dominios (proxy DNS), reputación de dominios, registros y alertas por alto volumen de consultas o subdominios con mucha entropía.

#### DHCP (Dynamic Host Configuration Protocol)

- **Qué es:** asigna IPs, gateway, DNS automáticamente en redes LAN.
- **Cómo funciona:** descubrimiento / oferta / solicitud / ack (DORA).
- **Ejemplo legítimo:** tu móvil obtiene IP al conectar al Wi-Fi.
- **Abuso:** rogue DHCP servers (un actor proporciona config maliciosa: gateway/ DNS manipulada).
- **Defensa/detección:** DHCP snooping en switches, segmentación de redes, monitoreo de nuevas concesiones DHCP.

#### ARP (Address Resolution Protocol)

- **Qué es:** traduce IP → MAC en una LAN.
- **Cómo funciona:** petición ARP broadcast, respuesta con MAC.
- **Ejemplo legítimo:** tu PC pregunta “¿quién tiene 192.168.1.10?” y recibe la MAC.
- **Abuso:** ARP spoofing/poisoning para interceptar tráfico (man-in-the-middle).
- **Defensa/detección:** Dynamic ARP Inspection (DAI) en switches, entradas ARP estáticas en segmentos críticos, monitoreo de cambios MAC↔IP.

#### IP (Internet Protocol)

- **Qué es:** direccionamiento y encaminamiento de paquetes (IPv4/IPv6).
- **Cómo funciona:** fragmentación, TTL, encabezados con direcciones origen/destino.
- **Ejemplo legítimo:** enviar un paquete a un servidor en otra red vía routers.
- **Abuso:** IP spoofing, routing misconfigurations que permiten interceptación.
- **Defensa/detección:** filtros anti-spoofing (BCP38), RPKI para BGP, monitorización de rutas.

#### ICMP (Internet Control Message Protocol)

- **Qué es:** mensajes de diagnóstico y control (ej. `ping`, mensajes de destino inalcanzable).
- **Ejemplo legítimo:** comprobar latencia con `ping`.
- **Abuso:** ICMP flood como vector de DDoS o para mapeo host/laddering.
- **Defensa/detección:** rate-limit ICMP, inspección en firewalls, IPS signatures.

#### TCP (Transmission Control Protocol)

- **Qué es:** transporte orientado a conexión, garantiza entrega en orden.
- **Cómo funciona:** three-way handshake (SYN, SYN-ACK, ACK), control de flujo, retransmisiones.
- **Ejemplo legítimo:** navegación web, transferencia de ficheros confiable.
- **Abuso:** SYN floods (negación de servicio) o manipulación de secuencia si vulnerabilidades en stack.
- **Defensa/detección:** SYN cookies, limitares de conexión, WAF/IPS, monitoreo de patrones anómalos de conexiones.

#### UDP (User Datagram Protocol)

- **Qué es:** transporte sin conexión, bajo overhead y latencia.
- **Ejemplo legítimo:** VoIP, streaming, DNS (consulta UDP).
- **Abuso:** UDP amplification/reflection DDoS (ej. NTP, memcached en el pasado).
- **Defensa/detección:** filtrar/limitar puertos UDP innecesarios, rate limiting, inspección de tráfico DNS/NTP.

#### HTTP / HTTPS

- **Qué es:** protocolo web (HTTP claro, HTTPS cifrado por TLS).
- **Ejemplo legítimo:** cargar páginas, APIs REST.
- **Abuso:** ataques a aplicaciones web (inyección, XSS, CSRF), interceptación en HTTP sin TLS.
- **Defensa/detección:** TLS obligatorio, HSTS, WAF (Web Application Firewall), análisis de logs, pruebas de seguridad de aplicaciones (SAST/DAST).

#### FTP / SFTP

- **Qué es:** FTP (no cifrado) y SFTP (SSH File Transfer, cifrado).
- **Ejemplo legítimo:** transferencia de archivos entre servidores (SFTP mucho preferible).
- **Abuso:** credenciales en texto plano en FTP, servidores mal configurados.
- **Defensa/detección:** usar SFTP/FTPS, deshabilitar FTP si no es necesario, monitorear accesos y transferencias.

#### SMTP / IMAP / POP3

- **Qué es:** protocolos para envío (SMTP) y acceso/recepción (IMAP/POP3) de correo.
- **Abuso:** phishing, spam, abuso de servidor abierto (open relay).
- **Defensa/detección:** SPF/DKIM/DMARC, antispam, autenticación, monitoreo de volúmenes y patrones.

#### SSH / Telnet / RDP / SMB

- **SSH (secure shell):** acceso remoto seguro (clave pública/privada).

  - **Telnet:** acceso remoto sin cifrado (evitar).
  - **RDP:** escritorio remoto de Windows (asegurar con MFA, limitación por IP).
  - **SMB:** compartición de archivos (vulnerable si no parcheado — recuerda WannaCry, EternalBlue).

- **Abuso:** credenciales débiles, servicios expuestos a Internet, explotación de vulnerabilidades.
- **Defensa/detección:** autenticación fuerte (claves, MFA), firewalling, segmentación, parcheo rápido, monitor de autenticaciones fallidas.

#### SNMP (Simple Network Management Protocol)

- **Qué es:** telemetría y gestión de dispositivos de red.
- **Abuso:** community strings por defecto (public/private), extracción de información topológica.
- **Defensa/detección:** SNMPv3 (autenticación + cifrado), cambiar strings por defecto, limitar origen de consultas.

#### BGP / OSPF (protocolos de enrutamiento)

- **BGP:** intercambio de rutas entre AS (proveedores de Internet). Riesgo: secuestro de rutas BGP si no se validan.

  - **Mitigación:** RPKI, políticas de filtrado.

- **OSPF:** enrutamiento interno usando el algoritmo de camino más corto (Dijkstra).

  - **Mitigación:** autenticación entre routers, monitoreo de LSAs (Link State Advertisements).

#### NTP (Network Time Protocol)

- **Qué es:** sincronización horaria.
- **Abuso:** servidores NTP abiertos usados para amplificación DDoS o manipulación de timestamp en logs.
- **Defensa:** limitar clientes permitidos, usar servidores de confianza.

#### LDAP / Active Directory

- **Qué es:** directorios y autenticación en entornos Windows.
- **Abuso:** exfiltración de hashes, abuso de consultas anónimas mal configuradas.
- **Defensa:** segmentación, endurecimiento de DCs, monitoreo de consultas inusuales, aplicar MS best practices.

### 3) Cómo aprenden los “hackers” éticos (y cómo deberías aprender tú)

Si tu objetivo es **aprender seguridad de redes**, hazlo en entornos legales y controlados:

- **Laboratorios y plataformas legales:** TryHackMe, Hack The Box, OWASP Juice Shop, VulnHub, RangeForce.
- **Máquinas virtuales:** crea un laboratorio con VirtualBox/VMware (red interna) y VMs vulnerables locales (DVWA, Metasploitable) — todo dentro de tu equipo.
- **Captura de paquetes para análisis:** usa Wireshark en redes que controlas para estudiar comportamiento de DNS/TCP/HTTP.
- **CTFs y retos:** ideales para practicar técnicas, metodología y mitigaciones sin infringir la ley.
- **Cursos y certificaciones éticas:** e.g., OSCP, eJPT, CompTIA Security+, cursos de redes y de seguridad.

### 4) Detección y defensa: checklist práctico (alto nivel)

- **Inventario de servicios y exposición:** conocer qué servicios escuchan en qué puertos.
- **Segmentación de red y VLANs:** limitar alcance de un compromiso.
- **Firewalls y ACLs:** cerrar puertos no necesarios.
- **Autenticación fuerte y MFA:** para accesos remotos (SSH, RDP, paneles).
- **Patching:** mantener sistemas y stacks actualizados (SMB, RDP, routers).
- **Monitorización y SIEM:** alertas por patrones anómalos (picos DNS, conexiones SSH fuera de horario).
- **Honeypots y deception:** para identificar intentos de intrusión sin arriesgar sistemas reales.
- **Backups y DR:** suponer que una intrusión puede ocurrir y estar preparado.

### 5) Ejemplos conceptuales (seguros y educativos)

- **Detectar exfiltración por DNS:** observar subdominios con alta entropía o picos inusuales de consultas a dominios externos.
- **Identificar brute-force SSH:** muchos intentos fallidos desde IPs distintas → alertar y bloquear geográficamente/por IP.
- **Ver actividad lateral SMB:** subida masiva de ficheros a shares desde cuentas no habituales → investigar.

(Estos ejemplos son para **detección** — no te explico cómo explotar SMB/SSH.)

### 6) Recursos recomendados (seguros y legales)

- TryHackMe, Hack The Box (laboratorios controlados).
- OWASP (aplicaciones web y metodología).
- Wireshark University / documentación de RFCs (para profundizar en protocolos).
- Libros: _The Web Application Hacker’s Handbook_ (para entender vulnerabilidades en apps), _Network Security Essentials_.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**                             | **Siguiente 4**                            |
| ------------------ | --------------------------------------- | ------------------------------------------ |
| [🏠](../README.md) | [⏪](./3_2_Understand_the_OSI_model.md) | [⏩](./3_4_Common_Ports_and_their_Uses.md) |
