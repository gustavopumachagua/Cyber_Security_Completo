| **Inicio**         | **atrás 34**               | **Siguiente 36**              |
| ------------------ | -------------------------- | ----------------------------- |
| [🏠](../README.md) | [⏪](./13_34_NAC_based.md) | [⏩](./13_36_Group_Policy.md) |

---

## **Índice**

| Temario                                                                                                                                                            |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [479. ¿Qué es el bloqueo de puertos?](#479-qué-es-el-bloqueo-de-puertos)                                                                                           |
| [480. ¿Tu router está bloqueando tus puertos? Aquí te explicamos cómo averiguarlo](#480-tu-router-está-bloqueando-tus-puertos-aquí-te-explicamos-cómo-averiguarlo) |

# **Port Blocking**

## **479. ¿Qué es el bloqueo de puertos?**

![Port Blocking](/img/13_Tools_for_Incident_Response_and_Discovery/Port_Blocking.jpg "Port Blocking")

Un **puerto** es un número que identifica una aplicación/servicio en un host (p. ej. 80 → HTTP, 443 → HTTPS, 22 → SSH).
El **bloqueo de puertos** significa impedir el tráfico hacia o desde un puerto concreto en un host o en la red. Se puede hacer en varios puntos: firewall del equipo, firewall de red (router, appliance), políticas del ISP, reglas NAT, o por la propia aplicación.

**Efecto:** si el puerto está bloqueado, una aplicación que escucha en ese puerto no recibirá conexiones (o no podrá establecer salidas), y quien intente conectarse obtendrá timeout o rechazo.

### ¿Por qué se bloquean puertos? (motivos)

- **Seguridad:** reducir superficie de ataque (cerrar puertos innecesarios).
- **Política/operativa:** ISPs bloquean puertos usados para spam (p. ej. 25) o para administrar servicios.
- **Control de acceso / QoS:** restringir servicios a VLANs o redes de confianza.
- **Protección de endpoint:** antivirus/endpoint firewall bloquea puertos para impedir exploits o C2.
- **Errores de configuración** o NAT mal hecho también pueden “parecer” bloqueos.

### Redes seguras: papel del bloqueo de puertos en ciberseguridad

- **Regla mínima (least privilege):** sólo abrir los puertos necesarios.
- **Compensación:** cerrar puertos públicos evita escaneo/ataques automáticos (worms, exploits).
- **Segmentación:** diferentes zonas con diferentes puertos abiertos; así si un segmento cae, no compromete todo.
- **Capa en defensa en profundidad:** bloqueo de puertos + IDS/IPS + EDR + autenticación multi-factor.

### ¿Por qué los antivirus usan bloqueo de puertos?

Las suites de seguridad modernas incluyen **firewall de aplicaciones/host** que:

- Evitan que **procesos maliciosos** abran listeners (ej. backdoors).
- Bloquean conexiones salientes sospechosas (comando y control, exfiltración).
- Previenen exploits en servicios que abren puertos (por ejemplo, detener que un proceso descargue un payload por un puerto no autorizado).

En la práctica: el antivirus/hardening intercepta intentos de escucha o conexiones y los bloquea si no son de una app autorizada.

### ¿Puede el bloqueo de puertos afectar mi velocidad de Internet?

- **Directo:** No — bloquear un puerto concreto no reduce el ancho de banda de otros puertos/protocolos (p. ej. el bloqueo del puerto 25 no hace que HTTPS vaya más lento).
- **Indirecto:** sí puede aparentar impacto si:

  - Bloqueas los puertos usados por una aplicación (p. ej. un juego o CDN) y la app usa fallback por rutas ineficientes → menos rendimiento.
  - Un firewall/Proxy inspecciona todo el tráfico (deep packet inspection) y añade latencia.
  - NAT table saturada o CPU del firewall al filtrar mucho tráfico causa cuello de botella.

En resumen: el bloqueo _en sí_ no baja la velocidad, pero la configuración de seguridad y la inspección sí pueden afectar latencia/throughput.

### ¿Cómo comprobar si un puerto está bloqueado? — Métodos y comandos

> **Aviso**: realizar escaneos o pruebas fuera de tu red sin permiso puede ser ilegal. Usa estas pruebas en tus hosts/redes o con autorización.

#### 1) Comprobar localmente (¿hay servicio escuchando?)

**Windows (PowerShell)**

```powershell
# ver puertos escuchando
Get-NetTCPConnection -State Listen | Format-Table LocalAddress,LocalPort,OwningProcess

# o con netstat
netstat -ano | findstr LISTEN
```

**Linux/macOS**

```bash
# ss (moderno)
ss -tuln
# o lsof
sudo lsof -iTCP -sTCP:LISTEN -P -n
# netstat (legacy)
netstat -tuln
```

Si no aparece el puerto en `LISTEN`, el servicio no está activo; no es bloqueo de red sino falta de servicio.

#### 2) Probar conexión **saliente** desde tu equipo

**Telnet / nc / curl / PowerShell Test-NetConnection**

- **Linux / macOS**:

```bash
# prueba TCP al puerto 22 en host remoto
nc -vz example.com 22
# o
telnet example.com 22
```

- **Windows PowerShell**:

```powershell
Test-NetConnection -ComputerName example.com -Port 22
```

- **curl** para puertos HTTP/S:

```bash
curl -v http://example.com:8080/
```

**Resultados**:

- `succeeded` / `Connected` → puerto accesible (no bloqueado para salida).
- `connection timed out` → probablemente filtrado por firewall (drop).
- `connection refused` → el host rechazó la conexión (puerto cerrado en destino, no necesariamente bloqueado en la red).

#### 3) Probar **entrante** (desde Internet hacia tu host)

Si quieres saber si tu host es accesible desde Internet (p. ej. servidor en casa detrás de router/NAT):

- **Desde un servidor en la nube (o móvil en 4G)**:

```bash
nc -vz tu-ip-publica 3389
```

- **Herramientas online** (port scanners públicos) — ejemplos: “Open Port Check Tool”, “ShieldsUP” (usa con permiso).
- **nmap** (desde otro host):

```bash
nmap -p 22,80,443 tu-ip-publica
```

Si `filtered` → firewall está bloqueando. Si `closed` → puerto no está abierto por servicio/NAT.

#### 4) Ver reglas de firewall en tu equipo / router

**Windows Firewall (GUI)** — “Windows Defender Firewall with Advanced Security” → Inbound Rules / Outbound Rules.

**PowerShell**:

```powershell
Get-NetFirewallRule | Where-Object { $_.Enabled -eq "True" } | Format-Table DisplayName,Direction,Action
```

**Linux (iptables/nftables/ufw)**:

```bash
# iptables
sudo iptables -L -n -v
# ufw
sudo ufw status verbose
# nftables
sudo nft list ruleset
```

**Router**: entra a la interfaz web/CLI del router y revisa reglas de firewall / port forwarding / NAT.

#### 5) Diagnóstico con ICMP y traceroute

- `ping` y `traceroute` ayudan a saber si hay conectividad general.
- Si `ping` OK pero TCP al puerto falla → bloqueo específico de puerto.

#### 6) Logs y herramientas de diagnóstico

- Revisa logs del firewall local (`/var/log/ufw.log` / syslog) o del router para entradas de denegación.
- Revisa logs del antivirus/host firewall (por ej. Windows Security/Windows Defender).

### Puertos frecuentemente bloqueados (ejemplos reales)

- **25 (SMTP)**: muchos ISPs lo bloquean para evitar spam saliente.
- **135–139, 445 (NetBIOS/SMB)**: a menudo bloqueados desde Internet por seguridad.
- **23 (Telnet)**: bloqueado por inseguro.
- **3306 (MySQL), 5432 (Postgres)**: generalmente no se exponen a Internet.
- **Rango alto**: puertos dinámicos/ephemeral no suelen bloquearse, pero policies pueden afectar.

### Qué hacer si un puerto está bloqueado (remedios)

1. **Verifica servicio local**: asegúrate que el daemon/service esté `LISTEN`.
2. **Revisa firewall local** (Windows Firewall, ufw, iptables). Permite el puerto si procede.
3. **Revisa NAT/port forwarding** en tu router si esperas accesos desde Internet.
4. **Consulta al ISP**: algunos bloqueos (p.ej. 25) los impone el proveedor. Solicita desbloqueo si justificas caso de uso.
5. **Usa VPN o túnel** si debes permitir tráfico desde fuera (con precaución y seguridad).
6. **Audita antivirus/EDR**: puede bloquear por política; crea excepción segura para la app si es legítima.

### Buenas prácticas de seguridad con bloqueo de puertos

- Abrir sólo lo estrictamente necesario (principio de mínimo privilegio).
- Usar autenticación fuerte y cifrado en servicios expuestos (TLS, SSH con keys).
- Mantener servicios actualizados y parches aplicados.
- Segmentar redes (VLANs) y usar ACLs para limitar alcance.
- Registrar (logging) y monitorizar rechazos/intenções recurrentes (SIEM).
- No depender solo de puerto cerrado — complementar con IDS/IPS y controles en capa aplicación.

### Resumen práctico rápido (cheat-sheet)

- **¿Comprobar servicio local?** `ss -tuln` / `netstat -ano` / `Get-NetTCPConnection -State Listen`
- **¿Probar salida a puerto remoto?** `nc -vz host port` / `Test-NetConnection -Port` / `curl -v`
- **¿Probar desde Internet a mi IP?** `nmap -p port mi-ip-publica` (desde host externo) o usar herramienta en línea.
- **¿Ver reglas de firewall?** `iptables -L` / `sudo ufw status` / Windows Firewall GUI / `Get-NetFirewallRule`
- **¿Si está bloqueado?** revisar servicio local → firewall host → router/NAT → ISP → políticas de seguridad (EDR/AV).

---

[🔼](#índice)

---

## **480. ¿Tu router está bloqueando tus puertos? Aquí te explicamos cómo averiguarlo**

### 🔎 Comprensión de puertos y enrutadores

- Un **puerto** es un número lógico que identifica un servicio en un dispositivo (ej. 80 → HTTP, 443 → HTTPS, 22 → SSH).
- Un **router** conecta tu red interna con Internet y suele incluir un **firewall** que bloquea o filtra tráfico para protegerte.
- El router puede:

  - Bloquear puertos por defecto para evitar ataques.
  - Permitir abrir puertos manualmente con **Port Forwarding**.

### 🚫 ¿Por qué están bloqueados los puertos en los enrutadores?

1. **Seguridad:** Evitar accesos no autorizados a tu red local.
2. **Prevención de malware:** Proteger contra gusanos o bots que usan puertos inseguros (ej. 135–139 SMB, 23 Telnet).
3. **Restricciones del ISP:** Algunos proveedores bloquean puertos como el 25 (SMTP) o el 80 (HTTP) para evitar abuso.
4. **Configuración por defecto:** Muchos routers cierran todo excepto el tráfico saliente (puertos abiertos solo desde dentro hacia fuera).

### 🕵️ Cómo comprobar si un puerto está bloqueado en tu enrutador

#### ✅ Método 1: Usar la interfaz web del router

1. Conéctate al router (ej. [http://192.168.1.1](http://192.168.1.1) o [http://192.168.0.1](http://192.168.0.1)).
2. Inicia sesión con usuario y contraseña (por defecto: admin/admin o el que hayas configurado).
3. Busca la sección: **Firewall**, **Port Forwarding**, **NAT** o **Virtual Server**.
4. Revisa si el puerto aparece en la lista de reglas bloqueadas o si necesitas abrirlo manualmente.

**Ejemplo:** Quieres usar un servidor de juegos en el puerto 25565 (Minecraft).

En la interfaz del router agregas una regla de **Port Forwarding** para tu IP local (192.168.1.100 → 25565).

#### ✅ Método 2: Utilizar un escáner de puertos

Puedes usar herramientas locales o en línea para comprobar accesibilidad de puertos desde Internet.

- **nmap** (Linux/Windows con WSL):

```bash
nmap -p 80,443,25565 mi-ip-publica
```

Salida:

- `open` → puerto abierto.

- `closed` → servicio no escucha.

- `filtered` → probablemente firewall/router bloqueando.

- **Herramientas web**:

  - "Open Port Check Tool" (canyouseeme.org).
  - ShieldsUP de Gibson Research.

#### ✅ Método 3: Usar el símbolo del sistema

**Windows (PowerShell):**

```powershell
Test-NetConnection -ComputerName mi-ip-publica -Port 25565
```

**Linux/macOS:**

```bash
nc -vz mi-ip-publica 25565
```

Si devuelve **Timeout o Filtered**, el router/ISP está bloqueando.

Si devuelve **Connection Refused**, el router pasa el tráfico, pero no hay servicio escuchando.

### 🛠️ Solución de problemas de bloqueo de puertos

#### 🔹 Paso 1: Verifique la configuración del enrutador

- Entre en la interfaz web.
- Revise si hay reglas de **Port Forwarding** configuradas correctamente.
- Asegúrese de asignar la **IP local correcta** del dispositivo.

#### 🔹 Paso 2: Verifique las reglas del firewall

- Algunos routers tienen firewalls adicionales que bloquean puertos.
- En routers avanzados, puedes definir **ACLs** (listas de control de acceso).

#### 🔹 Paso 3: Verifique el bloqueo del ISP

- Pregunta a tu proveedor si bloquean puertos específicos.
- Ejemplo: muchos ISPs bloquean **puerto 25 (SMTP)** por spam.

#### 🔹 Paso 4: Reiniciar el enrutador

- A veces un reinicio aplica nuevas reglas de firewall o limpia sesiones bloqueadas.

### 📌 Preguntas frecuentes

#### 🔹 ¿Qué es el bloqueo de puertos y por qué ocurre?

Es la denegación de tráfico entrante/saliente en un puerto específico, normalmente por seguridad o políticas del ISP.

#### 🔹 ¿Cómo sé si mi enrutador está bloqueando puertos?

- Si desde dentro funciona (localhost o red interna) pero desde fuera no, lo más probable es que sea el router/firewall.
- Usa herramientas como `nmap`, `Test-NetConnection`, `canyouseeme.org`.

#### 🔹 ¿Cuáles son los puertos más comunes que están bloqueados?

- **25 (SMTP):** evitar spam.
- **135–139 y 445 (NetBIOS/SMB):** evitar gusanos.
- **23 (Telnet):** inseguro.
- **80 (HTTP) en algunos ISPs:** para que no hospedes servidores web en casa.

#### 🔹 ¿Cómo puedo verificar qué puertos están bloqueados?

- Revisando logs del router.
- Escaneando con `nmap`.
- Usando servicios en línea de port check.

#### 🔹 ¿Puedo desbloquear puertos en mi enrutador?

Sí, creando reglas de **Port Forwarding** o **DMZ** (menos recomendable).

Ejemplo:

Router TP-Link → Forwarding → Virtual Servers → Agregar → Servicio: 25565 → IP local 192.168.1.100.

#### 🔹 ¿Cuáles son los riesgos de desbloquear puertos?

- Dejas tu red expuesta a Internet.
- Un servicio mal configurado puede ser atacado (ej. RDP en puerto 3389).
- Riesgo de malware, fuerza bruta o explotación remota.

#### 🔹 ¿Cómo evitar problemas de bloqueo de puertos en el futuro?

1. Mantén actualizado el firmware del router.
2. Usa reglas claras de Port Forwarding en lugar de abrir todos los puertos.
3. Usa **VPN** para acceso remoto en vez de exponer servicios directos.
4. Documenta los puertos que abras.
5. Monitorea los logs de firewall y tráfico sospechoso.

### 🎯 Conclusión

El bloqueo de puertos en routers no es un error, es una **medida de seguridad por defecto**.

Para saber si el tuyo bloquea puertos:

- Usa la **interfaz web** (fácil y directo).
- Haz pruebas con **escáneres de puertos** o comandos (`nmap`, `Test-NetConnection`).
- Si confirmas que el puerto está bloqueado, revisa reglas del router y pregunta a tu ISP.

⚠️ Solo abre puertos que realmente necesites y protege siempre los servicios expuestos con contraseñas fuertes, TLS o VPN.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 34**               | **Siguiente 36**              |
| ------------------ | -------------------------- | ----------------------------- |
| [🏠](../README.md) | [⏪](./13_34_NAC_based.md) | [⏩](./13_36_Group_Policy.md) |
