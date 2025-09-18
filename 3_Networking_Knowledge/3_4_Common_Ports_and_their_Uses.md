| **Inicio**         | **atrás 3**                                    | **Siguiente 5**           |
| ------------------ | ---------------------------------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./3_3_Common_Protocols_and_their_Uses.md) | [⏩](./3_5_SSL_vs_TLS.md) |

---

## **Índice**

| Temario                                                                                      |
| -------------------------------------------------------------------------------------------- |
| [67. Puertos de red comunes que debes conocer](#67-puertos-de-red-comunes-que-debes-conocer) |
| [68. Puertos de red comunes](#68-puertos-de-red-comunes)                                     |

---

# **Puertos comunes y sus usos**

## **67. Puertos de red comunes que debes conocer**

![Puertos de red comunes](/img/3_Networking_Knowledge/Puertos_comunes.webp "Puertos de red comunes")

> ⚠️ Nota: los servicios _pueden_ ejecutarse en otros puertos, pero los siguientes son los valores por defecto. Nunca escanees/ataques sistemas que no te pertenezcan: solo prueba en entornos controlados.

### 1 — Puerto **20** (TCP) — **FTP (data)**

- **Qué es:** puerto usado por FTP para la transferencia _de datos_ en el modo activo.
- **Ejemplo real:** transferir un fichero vía FTP en modo activo entre cliente y servidor.
- **Riesgos:** FTP transmite credenciales en texto claro. Exposición = robo de credenciales y archivos.
- **Mitigación:** usar **SFTP** o **FTPS**, desactivar FTP, usar firewall.

### 2 — Puerto **21** (TCP) — **FTP (control)**

- **Qué es:** canal de control de FTP (comandos).
- **Ejemplo:** comandos `USER`, `PASS`, `LIST`.
- **Riesgos & mitigación:** igual que en el puerto 20 — preferir SFTP/FTPS y cerrar si no se necesita.

### 3 — Puerto **22** (TCP) — **SSH (Secure Shell)**

- **Qué es:** acceso remoto seguro a shells y túneles (clave pública/privada).
- **Ejemplo:** `ssh usuario@servidor` para administrar servidores Linux.
- **Riesgos:** fuerza bruta si hay contraseñas débiles; exposición pública facilita intentos automatizados.
- **Mitigación:** usar autenticación por claves, deshabilitar login por contraseña, 2FA, Fail2Ban, limitar IPs, actualizaciones.

Comprobar servicio localmente:

```bash
ss -tlnp | grep :22
```

### 4 — Puerto **23** (TCP) — **Telnet**

- **Qué es:** acceso remoto sin cifrar (obsoleto para administración remota).
- **Ejemplo:** antiguas configuraciones de routers.
- **Riesgos:** transmite credenciales en texto plano → no usar sobre Internet.
- **Mitigación:** deshabilitar Telnet, usar SSH.

### 5 — Puerto **25** (TCP) — **SMTP (envío de correo)**

- **Qué es:** protocolo para _sendmail_ y transferencia de correo entre servidores.
- **Ejemplo:** servidor de correo recibiendo correos desde otros MTAs.
- **Riesgos:** si está mal configurado como _open relay_ se usa para spam; puede verse comprometido para phishing.
- **Mitigación:** autenticar envío (submission en 587), aplicar SPF/DKIM/DMARC, monitorizar relays.

### 6 — Puerto **53** (UDP/TCP) — **DNS (Domain Name System)**

- **Qué es:** resolución de nombres (UDP para queries normales, TCP para zone transfer y respuestas grandes).
- **Ejemplo:** `dig @8.8.8.8 ejemplo.com` usa 53.
- **Riesgos:** DNS amplification/reflection DDoS; DNS tunneling para exfiltración; cache poisoning.
- **Mitigación:** habilitar DNSSEC, rate-limit, limitar AXFR, usar servidores recursivos seguros y monitoreo.

### 7 — Puerto **67** (UDP) — **DHCP (server)**

- **Qué es:** servidor DHCP escucha en 67; asigna IP, gateway, DNS (DORA flow).
- **Ejemplo:** router doméstico actúa como servidor DHCP en 192.168.1.1:67.
- **Riesgos:** _rogue DHCP_ puede inyectar gateways/DNS maliciosos.
- **Mitigación:** DHCP snooping en switches, segmentación de red, detectar nuevas concesiones inusuales.

### 8 — Puerto **68** (UDP) — **DHCP (client)**

- **Qué es:** puerto usado por clientes DHCP para recibir respuesta (servidor→cliente).
- **Notas:** 67/68 siempre vienen en pareja en IPv4.

### 9 — Puerto **80** (TCP) — **HTTP (web no cifrada)**

- **Qué es:** transporte de páginas web sin cifrar.
- **Ejemplo:** `http://ejemplo.com` en el navegador.
- **Riesgos:** MITM (interceptación), robo de cookies/credenciales.
- **Mitigación:** migrar a HTTPS (443), HSTS, redirección forzada, WAF.

Comprobar conexión:

```bash
curl -I http://example.com
```

### 10 — Puerto **110** (TCP) — **POP3**

- **Qué es:** protocolo para descarga de correo desde servidor (POP3).
- **Ejemplo:** cliente descarga emails y (opcionalmente) los borra en servidor.
- **Riesgos:** credenciales en claro si no usan TLS (POP3S 995 es la versión segura).
- **Mitigación:** usar POP3S/IMAPS o migrar a IMAP con TLS.

### 11 — Puerto **143** (TCP) — **IMAP**

- **Qué es:** acceso a correo en servidor (permanece en servidor; más flexible que POP3).
- **Ejemplo:** revisar carpetas, buscar correo desde múltiples dispositivos. IMAPS seguro usa 993.
- **Riesgos & mitigación:** habilitar TLS/SSL, autenticación fuerte.

### 12 — Puerto **443** (TCP) — **HTTPS (HTTP over TLS)**

- **Qué es:** HTTP cifrado con TLS — navegación segura.
- **Ejemplo:** `https://bank.example.com`.
- **Riesgos:** mala configuración TLS (cipher suites débiles), certificados expirados.
- **Mitigación:** usar TLS moderno, renovar certificados, HSTS, PFS (Perfect Forward Secrecy), escaneo de vulns TLS.

Comprobar handshake TLS:

```bash
openssl s_client -connect example.com:443
```

### 13 — Puerto **3306** (TCP) — **MySQL / MariaDB**

- **Qué es:** puerto por defecto para bases de datos MySQL.
- **Ejemplo:** `mysql -h db.server -P 3306 -u user -p`.
- **Riesgos:** exposición pública permite robo de datos o acceso remoto a DB.
- **Mitigación:** bind a `localhost` o VPC interna, firewall, conexiones TLS, usuarios con privilegios mínimos, autenticación fuerte.

### 14 — Puerto **3389** (TCP/UDP) — **RDP (Remote Desktop Protocol)**

- **Qué es:** escritorio remoto de Windows.
- **Ejemplo:** `mstsc` desde Windows o `rdesktop`/`xfreerdp` desde Linux.
- **Riesgos:** vectores de fuerza bruta, exploits históricos (p. ej. BlueKeep).
- **Mitigación:** no exponer RDP a Internet; usar VPN o RD Gateway, habilitar NLA, aplicar MFA, parches y restringir por IP.

### Tabla resumen rápida

| Puerto | Protocolo   | Capa                    | Uso típico                          |
| ------ | ----------- | ----------------------- | ----------------------------------- |
| 20     | FTP-data    | Aplicación / Transporte | Transferencia de datos FTP (activo) |
| 21     | FTP-control | Aplicación              | Control FTP                         |
| 22     | SSH         | Aplicación/Transporte   | Acceso remoto seguro                |
| 23     | Telnet      | Aplicación              | Acceso remoto (inseguro)            |
| 25     | SMTP        | Aplicación              | Envío de correo                     |
| 53     | DNS         | Aplicación/Transporte   | Resolución de nombres               |
| 67     | DHCP (srv)  | Aplicación              | DHCP servidor                       |
| 68     | DHCP (cli)  | Aplicación              | DHCP cliente                        |
| 80     | HTTP        | Aplicación              | Web no cifrada                      |
| 110    | POP3        | Aplicación              | Recepción de correo (descarga)      |
| 143    | IMAP        | Aplicación              | Acceso a correo (sincrónico)        |
| 443    | HTTPS       | Aplicación              | Web cifrada (TLS)                   |
| 3306   | MySQL       | Aplicación              | Base de datos MySQL                 |
| 3389   | RDP         | Aplicación              | Escritorio remoto Windows           |

### Cómo comprobar puertos abiertos (local / remoto — **solo en tus sistemas**)

- **Local (Linux):**

```bash
ss -tuln           # muestra puertos TCP/UDP en escucha
sudo lsof -i -P -n # procesos y puertos
```

- **Remoto (auditoría autorizada):**

```bash
nmap -sT -p 22,80,443 ejemplo.com
```

_(usa `nmap` únicamente en hosts/entornos que controlas o con permiso explícito)._

### Buenas prácticas de seguridad respecto a puertos

- Cerrar/filtrar puertos innecesarios en el firewall.
- Aplicar **principio de menor privilegio**: servicios solo accesibles desde donde se necesiten.
- Usar **TLS/SSH**, evitar protocolos en texto plano (FTP, Telnet, POP3 sin TLS).
- Segmentar redes y usar VPN para acceso remoto.
- Monitoreo (SIEM/IDS/IPS) y alertas por tráfico anómalo.
- Parcheo oportuno y endurecimiento de servicios (deshabilitar servicios no usados).
- Evitar _security by obscurity_: cambiar puertos no sustituye a controles reales.

---

[🔼](#índice)

---

## **68. Puertos de red comunes**

### ¿Qué es un puerto en redes?

Un **puerto** es un número (0–65535) que identifica **un servicio o aplicación** dentro de un host en una red.

- Se combina con una dirección IP para formar un **socket** (ej.: `192.168.1.10:22`).
- Hay dos protocolos de transporte principales: **TCP** (orientado a conexión, fiable) y **UDP** (sin conexión, más ligero).
- Rango de puertos:

  - **0–1023**: well-known / servicios estándar (requieren normalmente privilegios root).
  - **1024–49151**: registered (servicios registrados).
  - **49152–65535**: ephemeral/dinamicos (usados por clientes).

### Cómo interpretarlos en la práctica

- Servicio: `ssh` → puerto `22/tcp`.
- Servicio: `dns` → puerto `53/udp` (consultas) y `53/tcp` (transferencias, respuestas grandes).
- Para conectarte: la app usa IP + puerto. Ej.: `ssh user@1.2.3.4` implica `1.2.3.4:22`.

### Comprobaciones y comandos útiles (solo en tus sistemas o con permiso)

- Ver puertos en escucha (Linux):

```bash
ss -tuln        # -t TCP, -u UDP, -l listening, -n numeric
sudo lsof -i -P -n
```

- Comprobar puertos desde local con curl / openssl:

```bash
curl -I http://example.com:80
openssl s_client -connect example.com:443
```

- Comprobar UDP/TCP remoto (solo en hosts autorizados):

```bash
# nmap (auditoría autorizada)
nmap -sT -p 22,80,443 ejemplo.com    # TCP connect
nmap -sU -p 53 ejemplo.com           # UDP scan
```

- Escuchar localmente con netcat (ejemplo educativo):

```bash
# TCP
nc -l 12345
# UDP
nc -u -l 12345
```

> ⚠️ **Nunca** escanees o ataques sistemas que no sean tuyos o para los que no tengas permiso explícito.

### Lista de puertos comunes — descripción, protocolo, ejemplo, riesgos y mitigación

Voy a agruparlos por categoría para que sea fácil de memorizar.

#### Puertos de administración remota y acceso

|   Puerto | Protocolo | Servicio | Ejemplo de uso            | Riesgos                             | Mitigación                                  |
| -------: | :-------: | :------- | :------------------------ | :---------------------------------- | :------------------------------------------ |
|   **22** |    TCP    | SSH      | `ssh user@server`         | Fuerza bruta si contraseñas débiles | Claves, 2FA, Fail2Ban, limitar IPs          |
|   **23** |    TCP    | Telnet   | acceso legacy (inseguro)  | Credenciales en claro               | Deshabilitar, usar SSH                      |
| **3389** |  TCP/UDP  | RDP      | Escritorio remoto Windows | Brute-force, exploits               | VPN/RD Gateway, NLA, MFA, parchar           |
| **5900** |    TCP    | VNC      | Control remoto GUI        | Sin cifrado por defecto             | Tunelizar por SSH/VPN, autenticación fuerte |

#### Puertos web y APIs

|          Puerto | Proto | Servicio           | Uso               | Riesgos                 | Mitigación                             |
| --------------: | :---: | :----------------- | :---------------- | :---------------------- | :------------------------------------- |
|          **80** |  TCP  | HTTP               | `curl http://...` | MITM, robo de cookies   | Forzar HTTPS                           |
|         **443** |  TCP  | HTTPS (HTTP/TLS)   | `https://...`     | TLS mal configurado     | TLS moderno, PFS, renovar certificados |
| **8080 / 8000** |  TCP  | HTTP alterno / dev | apps, dashboards  | exponer apps no seguras | auth, firewall, entorno de staging     |
|        **8443** |  TCP  | HTTPS alt          | web admin         | mismo que 443           | aplicar TLS y firewall                 |

#### Correo electrónico

|  Puerto | Proto | Servicio        | Uso                              | Riesgos                           | Mitigación                            |
| ------: | :---: | :-------------- | :------------------------------- | :-------------------------------- | :------------------------------------ |
|  **25** |  TCP  | SMTP (MTA)      | entrega entre servidores         | Open-relay, spam                  | Restricciones, SPF/DKIM/DMARC         |
| **587** |  TCP  | SMTP Submission | envío autenticado (cliente->MTA) | credenciales expuestas si sin TLS | STARTTLS, autenticación obligatoria   |
| **465** |  TCP  | SMTPS (legacy)  | SMTP sobre TLS                   | confusión de puertos              | usar puertos modernos y autenticación |

#### Correo (recepción)

|  Puerto | Proto | Servicio                   |
| ------: | :---: | :------------------------- |
| **110** |  TCP  | POP3 (sin TLS por defecto) |
| **995** |  TCP  | POP3S (POP3 sobre TLS)     |
| **143** |  TCP  | IMAP (sin TLS por defecto) |
| **993** |  TCP  | IMAPS (IMAP sobre TLS)     |

Mitigación: **usar las versiones seguras (TLS)** y credenciales robustas.

#### DNS, DHCP y protocolos infraestructurales

|  Puerto |  Proto  | Servicio    | Notas                                                         |
| ------: | :-----: | :---------- | :------------------------------------------------------------ |
|  **53** | UDP/TCP | DNS         | UDP para consultas, TCP para zone-transfer/respuestas grandes |
|  **67** |   UDP   | DHCP server | 67 server, 68 client                                          |
|  **68** |   UDP   | DHCP client | Parte del flujo DORA                                          |
| **123** |   UDP   | NTP (time)  | sincronización horaria — restringir acceso                    |

Riesgos: DNS tunneling, rogue DHCP, NTP amplification. Mitigar con DNSSEC, DHCP snooping, rate-limits.

#### Bases de datos y almacenamiento

|    Puerto |  Proto  | Servicio        |         Ejemplo         | Riesgos                            | Mitigación                                       |
| --------: | :-----: | :-------------- | :---------------------: | :--------------------------------- | :----------------------------------------------- |
|  **3306** |   TCP   | MySQL / MariaDB | `mysql -h host -P 3306` | Exposición pública → robo de datos | Bind local/VPC, firewall, TLS y usuarios mínimos |
|  **1433** |   TCP   | MS SQL Server   |  `sqlcmd -S host,1433`  | RCE/Brute force históricamente     | Firewall, autenticación, parchear                |
|  **1521** |   TCP   | Oracle DB       |     cliente Oracle      | Exposición similar                 | firewalls, ACLs                                  |
|  **2049** | TCP/UDP | NFS             |      file sharing       | permisos mal configurados          | usar Kerberos o VPN, restringir clientes         |
| **27017** |   TCP   | MongoDB         |        DB NoSQL         | DBs expuestas sin auth             | habilitar auth, IP restrictions                  |

#### Compartición / Servicios Windows

|      Puerto |  Proto  | Servicio                             |
| ----------: | :-----: | :----------------------------------- |
|     **445** |   TCP   | SMB (file sharing, Active Directory) |
| **137-139** | UDP/TCP | NetBIOS / legacy SMB                 |

Riesgos: exploits (p. ej. EternalBlue), movimiento lateral. Mitigar: parcheo, bloquear desde Internet, segmentación y control de credenciales.

#### Monitorización, gestión y logs

|      Puerto |  Proto  | Servicio                                                                |
| ----------: | :-----: | :---------------------------------------------------------------------- |
| **161/162** |   UDP   | SNMP (161 agent, 162 trap) — usar SNMPv3                                |
|     **514** | UDP/TCP | Syslog (legacy) — centralizar logs con TLS si es posible                |
|   **10000** |   TCP   | Webmin (panel admin) — ejemplo de app web admin frecuentemente expuesta |

Asegurar: versiones seguras (SNMPv3), credenciales, limitar origen.

#### Enrutamiento y protocolos de infraestructura

|  Puerto | Proto | Servicio                                   |
| ------: | :---: | :----------------------------------------- |
| **179** |  TCP  | BGP (Border Gateway Protocol) — entre ASes |
| **520** |  UDP  | RIP (legacy)                               |

BGP y enrutamiento son críticos: validar rutas (RPKI) y políticas.

#### Ejemplos prácticos (comprobaciones seguras)

1. Ver puertos en escucha en tu servidor Linux:

```bash
ss -tuln
# o
sudo netstat -tulnp
```

2. Probar un servicio web (HTTP/HTTPS):

```bash
curl -I http://localhost:80
openssl s_client -connect localhost:443
```

3. Ver proceso que ocupa puerto (ejemplo SSH):

```bash
sudo lsof -iTCP:22 -sTCP:LISTEN -P -n
```

4. Bloquear un puerto con UFW (Linux):

```bash
sudo ufw allow 22/tcp        # permitir SSH
sudo ufw deny 23/tcp         # bloquear Telnet
```

Windows Firewall (ejemplo PowerShell):

```powershell
# Bloquear puerto 23
New-NetFirewallRule -DisplayName "Block Telnet" -Direction Inbound -LocalPort 23 -Protocol TCP -Action Block
```

### Buenas prácticas de seguridad respecto a puertos

- **Cerrar puertos no usados** desde el firewall (ingreso y egress).
- **Principio de menor privilegio**: servicios accesibles solo desde donde se necesitan (VPCs, ACLs).
- **No exponer servicios de administración a Internet**: usar VPN o jump hosts.
- **Usar cifrado** (TLS/SSH) en vez de protocolos en texto plano.
- **Rotación y almacenamiento seguro de credenciales/keys**.
- **Limitar intentos y detectar brute-force** (Fail2Ban, bloqueo por IP).
- **Monitorización y alertas** (SIEM, NetFlow, IDS/IPS) para detectar escaneos, amplificación y exfiltración.
- **Parcheo continuo**: servicios con vulnerabilidades expuestas suelen ser objetivo primero.
- **Registro y auditoría** para investigar incidentes (logs de conexión, autenticación).

### Resumen rápido (cheat-sheet)

- Admin remota: **22 SSH** (seguro), evita **23 Telnet**.
- Web: **80 HTTP**, **443 HTTPS** (usar siempre 443/TLS).
- Correo: **25 SMTP**, **587 submission**, **993 IMAPS**, **995 POP3S**.
- DNS: **53 UDP/TCP** (restringir/usar DNSSEC).
- DB: **3306 MySQL**, **1433 MSSQL**, **27017 MongoDB** (no exponer sin protección).
- Compartir Windows: **445 SMB** (bloquear desde Internet).
- Escritorio remoto: **3389 RDP** (no directo a Internet; usar VPN).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**                                    | **Siguiente 5**           |
| ------------------ | ---------------------------------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./3_3_Common_Protocols_and_their_Uses.md) | [⏩](./3_5_SSL_vs_TLS.md) |
