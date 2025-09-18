| **Inicio**         | **atrás 14**             | **Siguiente 16**      |
| ------------------ | ------------------------ | --------------------- |
| [🏠](../README.md) | [⏪](./13_14_autopsy.md) | [⏩](./13_16_curl.md) |

---

## **Índice**

| Temario                                                |
| ------------------------------------------------------ |
| [440. comando ipconfig](#440-comando-ipconfig)         |
| [441. Entendiendo ipconfig](#441-entendiendo-ipconfig) |

# **ipconfig**

## **440. comando ipconfig**

![ipconfig](/img/13_Tools_for_Incident_Response_and_Discovery/ipconfig.png "ipconfig")

# `ipconfig` — explicación bien detallada con ejemplos (Windows)

`ipconfig` es la utilidad de línea de comandos de Windows para **consultar y administrar** la configuración IP de los adaptadores de red del equipo. Es la primera herramienta que usarás para diagnosticar problemas de conectividad, ver direcciones IPv4/IPv6, la puerta de enlace por defecto, servidores DNS, si el adaptador usa DHCP, y para renovar o liberar leases DHCP o limpiar la caché DNS.

### 1) ¿Qué hace `ipconfig`?

- Muestra direcciones IP, máscara de subred, gateway, DNS y estado DHCP.
- Permite liberar/renovar leases DHCP (`/release`, `/renew`).
- Gestiona la caché DNS local (`/flushdns`, `/displaydns`).
- Registra/actualiza registros DNS dinámicos (`/registerdns`).
- Muestra información detallada de todos los adaptadores (`/all`).
- Opera solo en Windows (equivalentes en Linux: `ip addr`, `ifconfig`, `nmcli`, `ip route`).

### 2) Sintaxis básica y opciones más usadas

```
ipconfig [ /all | /renew [adapter] | /release [adapter] | /flushdns | /displaydns | /registerdns | /showclassid adapter | /setclassid adapter [classid] | /? ]
```

Opciones clave:

- `ipconfig` — muestra direcciones IP básicas (por adapter).
- `ipconfig /all` — información completa (MAC, DHCP, leases, servidores DNS, WINS, descripción).
- `ipconfig /release` — libera el lease DHCP (quita la IP asignada).
- `ipconfig /renew` — solicita una nueva IP al servidor DHCP.
- `ipconfig /release6` y `ipconfig /renew6` — mismo para IPv6.
- `ipconfig /flushdns` — borra caché DNS local.
- `ipconfig /displaydns` — muestra la caché DNS local.
- `ipconfig /registerdns` — fuerza una actualización dinámica de DNS (registros A/AAAA).
- `ipconfig /allcompartments` — muestra info en todos los compartimentos (avanzado).
- `ipconfig /?` — ayuda.

> Muchos comandos (`/release`, `/renew`, `/flushdns`, `/registerdns`) requieren **ejecutar la consola como Administrador**.

### 3) Salida típica explicada (ejemplo `ipconfig /all`)

Ejemplo simulado (fragmento):

```
Windows IP Configuration

   Host Name . . . . . . . . . . . . : PC-GUSTAVO
   Primary Dns Suffix  . . . . . . . : example.com
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . : corp.example.com
   Description . . . . . . . . . . . : Intel(R) Ethernet Connection
   Physical Address. . . . . . . . . : AA-BB-CC-11-22-33
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::d4a5:...%12(Preferred)
   IPv4 Address. . . . . . . . . . . : 192.168.1.45(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Lease Obtained. . . . . . . . . . : Monday, September 15, 2025 08:00:12 AM
   Lease Expires . . . . . . . . . . : Monday, September 16, 2025 08:00:12 AM
   Default Gateway . . . . . . . . . : 192.168.1.1
   DHCP Server . . . . . . . . . . . : 192.168.1.254
   DNS Servers . . . . . . . . . . . : 192.168.1.10
                                       8.8.8.8
   NetBIOS over Tcpip. . . . . . . . : Enabled
```

Qué significan las líneas importantes:

- **Physical Address** = MAC del adaptador.
- **DHCP Enabled** = si la IP se obtuvo por DHCP.
- **IPv4 Address / Subnet Mask** = dirección IP y máscara.
- **Default Gateway** = router por el que sale el tráfico fuera de la subred.
- **DNS Servers** = servidores DNS usados (ordenados por preferencia).
- **Lease Obtained / Expires** = ventanas del lease DHCP.
- **Link-local IPv6** = dirección IPv6 local (fe80::...).
- **NetBIOS over Tcpip** = si NetBIOS está habilitado (legacy).

### 4) Ejemplos prácticos y flujos (con salida simulada)

#### A) Ver la información rápida de IP

```powershell
C:\> ipconfig
```

Salida resumida (ejemplo):

```
Ethernet adapter Ethernet:
   IPv4 Address. . . . . . . . . . . : 192.168.1.45
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.1.1
```

#### B) Información completa (recomendado para diagnóstico)

```powershell
C:\> ipconfig /all
```

#### C) Liberar y renovar DHCP (cuando no hay conectividad)

1. Abrir CMD como **Administrador**.
2. Liberar:

```powershell
C:\> ipconfig /release
```

Salida: la IP queda en 0.0.0.0 y el adaptador queda sin dirección DHCP.

3. Renovar:

```powershell
C:\> ipconfig /renew
```

Salida: nueva dirección IPv4 obtenida del servidor DHCP (si hay).
Si falla, verás errores de tiempo de espera; entonces comprueba cable, switch, servidor DHCP, firewall.

> Nota: para un adaptador específico puedes usar `ipconfig /release "Ethernet"` (pero en CMD el nombre exacto del adaptador debe coincidir).

#### D) Limpiar la caché DNS (resolver problemas de nombres)

```powershell
C:\> ipconfig /flushdns
Successfully flushed the DNS Resolver Cache.
```

#### E) Ver entradas DNS en caché

```powershell
C:\> ipconfig /displaydns
```

Salida: lista de hostnames y registros A/AAAA/TXT cacheados con TTL.

#### F) Forzar registro dinámico en DNS (útil cuando cambias de IP y quieres actualizar el DNS)

```powershell
C:\> ipconfig /registerdns
```

Esto lanza un registro dinámico (SRV/A/AAAA) y refresca la caché DNS local.

#### G) Mostrar solo los adaptadores/filtrar con `findstr`

Puedes extraer solo líneas útiles:

```powershell
C:\> ipconfig /all | findstr /i "IPv4 DNS Default Gateway"
```

Esto devolverá líneas que contienen "IPv4", "DNS" o "Default Gateway" — muy útil en scripts.

#### H) Guardar salida en un archivo (para enviar al soporte)

```powershell
C:\> ipconfig /all > C:\temp\netinfo.txt
```

### 5) Troubleshooting típico con `ipconfig` (pasos y ejemplos)

**Problema:** No hay acceso a internet.

1. Ejecuta `ipconfig /all`. ¿El adaptador tiene IP en 169.254.x.x?

   - Sí → **APIPA**: no obtuvo DHCP. Comprueba cable, Wi-Fi, servidor DHCP.

2. ¿DHCP Enabled = No?

   - Entonces la IP es estática; verifica que gateway y DNS estén correctos.

3. `ipconfig /release` y `ipconfig /renew` (Administrador). Observa errores (timeout).
4. `ping 127.0.0.1` → verifica stack TCP/IP local.
5. `ping <your-gateway>` (ej. `ping 192.168.1.1`) → si no responde, problema local (cable/switch/router).
6. `nslookup example.com` o `ping 8.8.8.8` → comprobar si el problema es DNS o enrutamiento.

   - Si `ping 8.8.8.8` funciona pero `ping google.com` falla → problema DNS → `ipconfig /flushdns` y revisar `ipconfig /all` para servidores DNS.

7. `ipconfig /displaydns` para ver entradas almacenadas; `ipconfig /registerdns` para forzar actualización.

### 6) Comandos relacionados y alternativas (PowerShell y Linux)

- **PowerShell** (cmdlets más modernos):

  - `Get-NetIPConfiguration` — muestra IPv4/IPv6, gateway, DNS (equivalente a ipconfig /all).
  - `Get-NetIPAddress` — lista direcciones.
  - `Get-NetAdapter` — estado del adaptador.
  - `ipconfig /release` y `/renew` se mantienen, pero en PowerShell puedes usar `Remove-NetIPAddress` / `New-NetIPAddress` para manipular direcciones en detalle.

- **Linux/macOS equivalentes**:

  - `ip addr` (o `ifconfig`) — ver direcciones.
  - `ip route` — ver gateway/ruta.
  - `nmcli device show` — NetworkManager info.
  - `systemd-resolve --status` o `resolvectl status` — info DNS en systemd.
  - `sudo dhclient -r` y `sudo dhclient` — release/renew DHCP.

### 7) Ejemplos avanzados y notas útiles

- **Renovar solo IPv6**:

```powershell
C:\> ipconfig /renew6
```

- **Liberar solo IPv6**:

```powershell
C:\> ipconfig /release6
```

- **Mostrar todos los compartimentos (raro, avanzado)**:

```powershell
C:\> ipconfig /allcompartments /all
```

(Sirve en entornos con compartimentos de red).

- **Ver y modificar ClassID DHCP** (usado por algunos servidores DHCP para asignaciones):

```powershell
C:\> ipconfig /showclassid "Ethernet"
C:\> ipconfig /setclassid "Ethernet" MYCLASSID
```

### 8) Buenas prácticas al usar `ipconfig`

- Ejecuta `ipconfig /all` y **captura** la salida antes de hacer cambios (guarda en archivo).
- Para operaciones de liberación/renovación o cambios en DNS usa **símbolo del sistema como Administrador**.
- Cuando pidas soporte técnico, adjunta la salida de `ipconfig /all` (oculta datos sensibles si es necesario).
- Usa `ipconfig /flushdns` cuando resuelvas problemas de nombres tras cambios DNS (ej. migración de sitio).

### 9) Ejemplo de sesión completa — diagnóstico paso a paso

Problema: el navegador no carga páginas.

1. Abre CMD y ejecuta:

```powershell
ipconfig /all > C:\temp\ip_all.txt
```

2. Revisa `ip_all.txt`: ¿IPv4 es 169.254.x.x? → Sí → no hay DHCP.
3. Ejecuta:

```powershell
ipconfig /release
ipconfig /renew
```

- Si `renew` falla → comprobar cable/punto de acceso o probar otro puerto en switch.

4. Comprueba conectividad con:

```powershell
ping 192.168.1.1
ping 8.8.8.8
ping google.com
```

5. Si `8.8.8.8` responde pero `google.com` no → ejecuta:

```powershell
ipconfig /flushdns
ipconfig /displaydns
nslookup google.com 8.8.8.8
```

6. Si todo falla, revisa logs y contacta con el administrador de red, adjuntando `C:\temp\ip_all.txt`.

### 10) Resumen — comandos clave rápidos

- `ipconfig` — ver info básica.
- `ipconfig /all` — info completa.
- `ipconfig /release` — liberar lease DHCP.
- `ipconfig /renew` — renovar lease DHCP.
- `ipconfig /release6` / `/renew6` — IPv6.
- `ipconfig /flushdns` — limpiar caché DNS.
- `ipconfig /displaydns` — mostrar caché DNS.
- `ipconfig /registerdns` — forzar actualización DNS.
- `ipconfig /showclassid` / `/setclassid` — gestionar classid DHCP.

---

[🔼](#índice)

---

## **441. Entendiendo ipconfig**

### 🔹 ¿Qué es `ipconfig`?

`ipconfig` es una herramienta de línea de comandos en **Windows** que permite ver y administrar la configuración de red del equipo.
Su nombre significa **IP Configuration** (Configuración de IP).

- Se usa para mostrar direcciones IPv4 e IPv6, máscara de subred, puerta de enlace (gateway) y servidores DNS.
- También permite **renovar IPs asignadas por DHCP**, **limpiar caché DNS** y resolver problemas de red.

👉 En **Linux/macOS** se usan comandos equivalentes como `ifconfig`, `ip addr`, o `ifconfig` (aunque `ifconfig` ya es legacy en Linux).

### 🔹 ¿Para qué se utiliza `ipconfig`?

1. Ver la IP pública o privada de tu equipo.
2. Comprobar si el adaptador usa **DHCP o IP estática**.
3. Renovar la conexión con el servidor **DHCP**.
4. Solucionar problemas de **DNS** (caché).
5. Apoyar en el diagnóstico de fallos de red, Wi-Fi o VPN.
6. Guardar información para soporte técnico.

### 🔹 Usar `ipconfig` para ver la información de la dirección IP

Ejemplo:

```powershell
C:\> ipconfig
```

Salida típica:

```
Ethernet adapter Ethernet:
   IPv4 Address. . . . . . . . . . . : 192.168.1.45
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . : 192.168.1.1
```

👉 Con `/all` tienes más detalle:

```powershell
C:\> ipconfig /all
```

Incluye: nombre del host, MAC, DHCP server, DNS servers, lease times, IPv6, NetBIOS.

### 🔹 Uso de `ipconfig` para supervisar el rendimiento de la red

Aunque `ipconfig` no mide velocidad como `ping` o `tracert`, sí:

- Te dice si tienes una IP válida o una **APIPA (169.254.x.x)**.
- Permite ver si tu equipo perdió el **gateway** (sin salida a Internet).
- Ayuda a comprobar si el servidor **DNS está configurado** (si no, no resuelve dominios).

👉 Ejemplo:

Si `ping 8.8.8.8` funciona pero no puedes navegar por nombre → revisa `ipconfig /all` y valida DNS.

### 🔹 Uso de `ipconfig` en scripts

Puedes automatizar chequeos:

```batch
:: Guardar estado de red en archivo con fecha
ipconfig /all > C:\logs\net_%date:~-4%%date:~3,2%%date:~0,2%.txt
```

Ejemplo en PowerShell:

```powershell
Get-Date | Out-File C:\logs\netinfo.txt -Append
ipconfig /all | Out-File C:\logs\netinfo.txt -Append
```

Esto es útil para auditorías o troubleshooting programado.

### 🔹 Uso de `ipconfig` para diagnosticar problemas de VPN

- Una VPN mal configurada puede **cambiar tu gateway** o servidores DNS.
- Con `ipconfig /all` puedes ver:

  - Qué adaptador creó la VPN.
  - Qué IP y DNS está usando.
  - Si la ruta de salida (gateway) es la esperada.

👉 Ejemplo:

Después de conectarte a la VPN revisa:

```powershell
ipconfig /all | findstr /i "IPv4 DNS Default"
```

Para comprobar si el tráfico se enruta correctamente.

### 🔹 Formas de usar `ipconfig` para solucionar problemas de red

#### 1) Renovación de concesiones de DHCP

Si la IP es inválida (ej. 169.254.x.x), renueva:

```powershell
ipconfig /release
ipconfig /renew
```

#### 2) Vaciado de la caché DNS

Si no puedes acceder a sitios web por cambios DNS:

```powershell
ipconfig /flushdns
```

Resultado:

```
Successfully flushed the DNS Resolver Cache.
```

#### 3) Ver qué entradas están en caché

```powershell
ipconfig /displaydns
```

### 🔹 Comandos y modificadores ipconfig comunes

| Comando                          | Descripción                            |
| -------------------------------- | -------------------------------------- |
| `ipconfig`                       | Info básica IP, máscara y gateway.     |
| `ipconfig /all`                  | Info completa (MAC, DHCP, DNS, etc.).  |
| `ipconfig /release`              | Libera dirección IP obtenida por DHCP. |
| `ipconfig /renew`                | Solicita nueva IP al servidor DHCP.    |
| `ipconfig /flushdns`             | Limpia caché DNS local.                |
| `ipconfig /displaydns`           | Muestra caché DNS local.               |
| `ipconfig /registerdns`          | Fuerza actualización de registros DNS. |
| `ipconfig /release6` / `/renew6` | Para IPv6.                             |

### 🔹 Comandos ipconfig avanzados

- `/registerdns` → Registra dinámicamente la IP en DNS.
- `/showclassid` y `/setclassid` → Para entornos DHCP con identificadores de clase.
- `/allcompartments` → Muestra todos los compartimentos de red (raro, pero útil en debugging avanzado).

### 🔹 Mensajes de error de ipconfig

- `An error occurred while renewing interface Ethernet: Unable to contact your DHCP server.`
  → No se pudo comunicar con DHCP → verifica router o servidor.

- `The requested operation requires elevation`
  → Debes ejecutar la consola como Administrador.

- `Media disconnected`
  → El adaptador no está conectado (Wi-Fi apagado o cable suelto).

### 🔹 Cómo acceder a ipconfig en Windows y Mac

- **Windows**:

  - Abre **CMD** (`Win + R` → `cmd` → Enter).
  - O usa **PowerShell**.

- **Mac**:

  - `ipconfig` existe pero con funciones distintas (muestra configuración de DHCP).
  - El equivalente de `ipconfig /all` en Mac es:

    ```bash
    ifconfig
    networksetup -getdnsservers Wi-Fi
    ```

  - En Linux, el equivalente sería:

    ```bash
    ip addr show
    ```

### 🔹 Comparación de `ipconfig` con otras herramientas de red

| Herramienta          | SO                     | Función                      |
| -------------------- | ---------------------- | ---------------------------- |
| `ipconfig`           | Windows                | Configuración IP y DNS.      |
| `ifconfig`           | Linux/Unix (legacy)    | Configuración de interfaces. |
| `ip addr`            | Linux moderno          | Reemplazo de `ifconfig`.     |
| `nmcli`              | Linux (NetworkManager) | Gestión avanzada de red.     |
| `netstat`            | Win/Linux              | Conexiones de red activas.   |
| `ping`               | Win/Linux              | Verifica conectividad ICMP.  |
| `tracert/traceroute` | Win/Linux              | Muestra ruta hacia un host.  |

### 🔹 ¿Para qué uso el comando ipconfig?

- Ver tu IP.
- Solucionar problemas de conectividad.
- Comprobar si DHCP/DNS funcionan.
- Diagnóstico de VPN.
- Limpiar caché DNS en problemas de resolución.

### 🔹 ¿Cuáles son los 3 comandos principales en ipconfig?

1. `ipconfig /all` → Ver toda la info de red.
2. `ipconfig /release` + `ipconfig /renew` → Resetear conexión con DHCP.
3. `ipconfig /flushdns` → Solucionar problemas de resolución DNS.

### 🔹 ¿Cómo uso el comando ipconfig en Windows 10 o Mac?

- En **Windows 10/11**: abrir CMD o PowerShell y escribir:

  ```powershell
  ipconfig /all
  ```

- En **Mac**:

  ```bash
  ifconfig
  ```

  o bien para DHCP:

  ```bash
  ipconfig getifaddr en0
  ```

### 🔹 ¿Ipconfig obtendrá mi dirección IP?

✅ Sí.

Si ejecutas:

```powershell
ipconfig
```

Obtendrás tu **dirección IPv4 e IPv6 local** (privada).

> ⚠️ No muestra la **IP pública** en Internet. Para eso tendrías que usar páginas como `whatismyip.com` o ejecutar `curl ifconfig.me` en Linux/Mac.

📌 **En resumen**:

`ipconfig` es tu **navaja suiza de red en Windows**. Te permite **ver IPs, DNS, gateway, renovar DHCP, limpiar caché DNS y diagnosticar problemas de conectividad**. Sus tres usos más comunes son:

- Ver la configuración con `/all`.
- Resetear IPs con `/release` y `/renew`.
- Arreglar problemas de DNS con `/flushdns`.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 14**             | **Siguiente 16**      |
| ------------------ | ------------------------ | --------------------- |
| [🏠](../README.md) | [⏪](./13_14_autopsy.md) | [⏩](./13_16_curl.md) |
