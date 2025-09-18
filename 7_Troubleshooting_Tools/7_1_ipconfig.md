| **Inicio**         | **Siguiente 2**     |
| ------------------ | ------------------- |
| [🏠](../README.md) | [⏩](./7_2_Ping.md) |

---

## **Índice**

| Temario                                                |
| ------------------------------------------------------ |
| [160. Comando IPconfig](#160-comando-ipconfig)         |
| [161. Entendiendo IPconfig](#161-entendiendo-ipconfig) |

# **IPconfig**

## **160. Comando IPconfig**

![IPconfig](/img/7_Troubleshooting_Tools/IPconfig.png "IPconfig")

### 🔹 Configuración de IP

Una **dirección IP** identifica de forma única a un dispositivo en una red. Para que un equipo se comunique, necesita:

- **Dirección IP** (identificador del dispositivo).
- **Máscara de subred** (define la red y los hosts disponibles).
- **Puerta de enlace (gateway)** (salida hacia otras redes).
- **DNS** (traducción de nombres de dominio a IPs).

### 🔹 Sintaxis

La sintaxis varía según el sistema operativo.

#### 1. **En Windows (CMD)**

```bash
netsh interface ip set address "Nombre_Interfaz" static <IP> <Máscara> <Gateway>
netsh interface ip set dns "Nombre_Interfaz" static <DNS_Principal>
```

#### 2. **En Linux (con iproute2)**

```bash
ip addr add <IP>/<Prefijo> dev <Interfaz>
ip route add default via <Gateway>
```

#### 3. **En Linux (con ifconfig - obsoleto pero usado)**

```bash
ifconfig <Interfaz> <IP> netmask <Máscara> up
route add default gw <Gateway>
```

### 🔹 Parámetros

- **IP** → Dirección asignada al host (ej: `192.168.1.10`).
- **Máscara / Prefijo** → Define la red (ej: `255.255.255.0` o `/24`).
- **Gateway** → Dirección de salida hacia internet (ej: `192.168.1.1`).
- **DNS** → Servidores que traducen nombres (ej: `8.8.8.8`, `8.8.4.4`).
- **Interfaz** → Nombre de la tarjeta de red (ej: `Ethernet0`, `eth0`, `wlan0`).

### 🔹 Observaciones

1. La **IP debe ser única** dentro de la red local.
2. Si usas **DHCP**, el router asigna automáticamente la IP.
3. En configuraciones **estáticas**, debes ingresar todo manualmente.
4. La máscara y el gateway deben coincidir con la red del router.
5. Un error común es configurar dos hosts con la misma IP → genera **conflictos de red**.

### 🔹 Ejemplos

#### 🖥️ Ejemplo en Windows (IP fija en Ethernet)

```bash
netsh interface ip set address "Ethernet" static 192.168.1.50 255.255.255.0 192.168.1.1
netsh interface ip set dns "Ethernet" static 8.8.8.8
```

📌 Configura:

- IP → `192.168.1.50`
- Máscara → `255.255.255.0`
- Gateway → `192.168.1.1`
- DNS → `8.8.8.8`

#### 🐧 Ejemplo en Linux (IP fija en eth0)

```bash
ip addr add 192.168.1.50/24 dev eth0
ip route add default via 192.168.1.1
```

📌 Configura:

- IP → `192.168.1.50`
- Máscara → `/24` (equivalente a `255.255.255.0`)
- Gateway → `192.168.1.1`

#### 🌐 Ejemplo usando DHCP en Linux

```bash
dhclient eth0
```

📌 Pide al router que asigne IP automática a la interfaz **eth0**.

✅ **Resumen final:**

- La configuración IP depende del sistema operativo.
- Parámetros básicos: IP, máscara, gateway, DNS e interfaz.
- Puede ser estática (manual) o dinámica (DHCP).
- Una mala configuración genera pérdida de conectividad.

---

[🔼](#índice)

---

## **161. Entendiendo IPconfig**

### 🔹 ¿Qué es `ipconfig`?

`ipconfig` (Internet Protocol Configuration) es una **herramienta de línea de comandos en Windows** que permite ver y administrar la configuración de red del equipo.

👉 En Linux/macOS, el equivalente sería `ifconfig` o `ip`.

### 🔹 ¿Para qué se utiliza `ipconfig`?

Se usa para:

1. Ver **direcciones IP** de las interfaces de red.
2. Conocer la **máscara de subred**, **puerta de enlace predeterminada** y **DNS**.
3. **Renovar** o **liberar** direcciones IP cuando se usa DHCP.
4. **Vaciar la caché DNS** para solucionar problemas de resolución de nombres.
5. Diagnosticar **problemas de conectividad** en redes LAN, Wi-Fi o VPN.

### 🔹 Usar `ipconfig` para ver la información de la dirección IP

Ejemplo en CMD:

```bash
ipconfig
```

📌 Salida típica:

```
Adaptador de LAN inalámbrica Wi-Fi:

   Sufijo DNS específico para la conexión. . . : miempresa.local
   Dirección IPv4. . . . . . . . . . . . . . . : 192.168.1.50
   Máscara de subred . . . . . . . . . . . . . : 255.255.255.0
   Puerta de enlace predeterminada . . . . . . : 192.168.1.1
```

### 🔹 Uso de `ipconfig` para supervisar el rendimiento de la red

- Permite detectar si la **IP cambia constantemente** (problema de DHCP).
- Ver si el adaptador **recibe direcciones correctas**.
- Identificar cuando hay una IP **APIPA (169.254.x.x)** → significa que el DHCP falló.

### 🔹 Uso de `ipconfig` en scripts

Puedes automatizar diagnósticos. Ejemplo en un `.bat`:

```bat
@echo off
echo Información de red:
ipconfig /all
pause
```

📌 Útil en soporte técnico para recolectar datos de varios equipos.

### 🔹 Uso de `ipconfig` para diagnosticar problemas de VPN

- Si la VPN conecta pero no hay acceso a recursos:

  - Revisar si aparece un **adaptador virtual** con IP privada asignada.
  - Comprobar la **puerta de enlace** y servidores **DNS**.

Ejemplo:

```bash
ipconfig /all
```

📌 Podrás ver si la VPN realmente entregó una IP.

### 🔹 Formas de usar `ipconfig` para solucionar problemas de red

#### 🔸 Renovación de concesiones DHCP

```bash
ipconfig /release
ipconfig /renew
```

📌 Se usa cuando el equipo no obtiene IP del router.

#### 🔸 Vaciado de la caché DNS

```bash
ipconfig /flushdns
```

📌 Útil cuando un dominio cambió de servidor y aún apunta al viejo.

### 🔹 Comandos y modificadores `ipconfig` comunes

- `ipconfig` → Muestra IP, máscara y gateway.
- `ipconfig /all` → Muestra info completa (MAC, DHCP, DNS, etc.).
- `ipconfig /release` → Libera la IP obtenida por DHCP.
- `ipconfig /renew` → Solicita nueva IP al servidor DHCP.
- `ipconfig /flushdns` → Vacía la caché DNS.
- `ipconfig /displaydns` → Muestra la caché DNS.

### 🔹 Comandos `ipconfig` avanzados

- `ipconfig /registerdns` → Fuerza registro del host en el DNS.
- `ipconfig /showclassid` → Muestra los identificadores de clase DHCP.
- `ipconfig /setclassid` → Establece un ID de clase DHCP para configuraciones avanzadas.

### 🔹 Mensajes de error de `ipconfig`

- **“Medio desconectado”** → El adaptador no está conectado (Wi-Fi apagado o cable desconectado).
- **“No se puede renovar la dirección IP”** → El servidor DHCP no responde.
- **“Acceso denegado”** → Necesitas ejecutar CMD como **Administrador**.

### 🔹 Cómo acceder a `ipconfig` en Windows y Mac

- **Windows:** Abrir CMD (`Win + R` → `cmd` → Enter).
- **Mac/Linux:** El comando `ipconfig` no existe, se usa:

  - `ifconfig` (clásico).
  - `ip addr` (moderno en Linux).

Ejemplo en Mac:

```bash
ifconfig en0
```

### 🔹 Comparación de `ipconfig` con otras herramientas

- **Windows →** `ipconfig`.
- **Linux →** `ifconfig` / `ip`.
- **MacOS →** `ifconfig`.
- **Más avanzado en todos los SO →** `ping`, `tracert`/`traceroute`, `nslookup`, `netstat`.

✅ **Resumen final:**

`ipconfig` es una herramienta esencial en Windows para **diagnóstico de red**, ya sea en LAN, Wi-Fi o VPN. Permite ver configuraciones, renovar IPs, limpiar caché DNS y solucionar problemas comunes de conectividad.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**     |
| ------------------ | ------------------- |
| [🏠](../README.md) | [⏩](./7_2_Ping.md) |
