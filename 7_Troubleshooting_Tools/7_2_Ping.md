| **Inicio**         | **atrás 1**             | **Siguiente 3**    |
| ------------------ | ----------------------- | ------------------ |
| [🏠](../README.md) | [⏪](./7_1_ipconfig.md) | [⏩](./7_3_Dig.md) |

---

## **Índice**

| Temario                                                    |
| ---------------------------------------------------------- |
| [162. ¿Qué es el ping?](#162-qué-es-el-ping)               |
| [163. Comando ping explicado](#163-comando-ping-explicado) |

# **Ping**

## **162. ¿Qué es el ping?**

![Ping](/img/7_Troubleshooting_Tools/ping.PNG "Ping")

### 📌 Definición de ping

El comando **`ping`** es una herramienta de red que se usa para **probar la conectividad entre dos dispositivos** en una red, ya sea local (LAN) o en Internet.

📌 El nombre viene de los **sonares submarinos**, que envían un "ping" y esperan un "eco" de respuesta.

👉 Ejemplo:

Si ejecutas:

```bash
ping www.google.com
```

Tu computadora enviará mensajes a los servidores de Google y verificará si responden.

### 📌 ¿Cómo funciona el ping?

1. El comando **envía paquetes ICMP (Internet Control Message Protocol)** tipo _Echo Request_.
2. El host de destino responde con un paquete ICMP _Echo Reply_.
3. El ping mide:

   - ✅ Si hay **respuesta** (conectividad).
   - ⏱️ El **tiempo de ida y vuelta (latencia)** en milisegundos.
   - ❌ Si hay **pérdida de paquetes** (problemas de red).

👉 Ejemplo de salida:

```
Haciendo ping a 8.8.8.8 con 32 bytes de datos:
Respuesta desde 8.8.8.8: bytes=32 tiempo=15ms TTL=118
```

### 📌 Cómo utilizar los comandos ping

#### 🔸 En Windows, Linux y Mac

```bash
ping <IP o dominio>
```

#### 🔸 Ejemplos

- `ping 192.168.1.1` → prueba conectividad con el router local.
- `ping www.google.com` → prueba conexión a internet y resolución DNS.
- `ping -t 8.8.8.8` (Windows) → ping continuo hasta que lo detengas con `Ctrl + C`.
- `ping -c 4 8.8.8.8` (Linux/Mac) → hace 4 pings y se detiene.

### 📌 ¿Cuándo sería útil utilizar el comando ping?

1. **Verificar si un dispositivo está en línea** (router, servidor, impresora en red).
2. **Probar conexión a Internet**.
3. **Detectar pérdida de paquetes** (problemas de red).
4. **Medir latencia** (útil en videojuegos online o VoIP).
5. **Diagnosticar problemas de DNS**:

   - Si `ping 8.8.8.8` funciona pero `ping google.com` no → el problema está en DNS.

### 📌 ¿Qué es una prueba de ping?

Una **prueba de ping** es simplemente ejecutar el comando varias veces para analizar la **calidad de la conexión**.
La salida muestra:

- **Respuestas recibidas** (conectividad).
- **Tiempo de respuesta (latencia)**.
- **Porcentaje de pérdida de paquetes**.

### 📌 ¿Qué te dice la prueba de ping?

1. ✅ **El host responde** → existe conectividad.
2. ⏱️ **Latencia baja (ms)** → red rápida y saludable.

   - < 50 ms → excelente (LAN o fibra óptica).
   - 50-150 ms → aceptable (juegos online, videollamadas).
   - > 200 ms → retraso notable (satélite o mala conexión).

3. ❌ **Tiempo agotado o inaccesible** → el host no responde (apagado, firewall bloqueando ICMP, pérdida de conexión).
4. 📉 **Pérdida de paquetes** → problemas en la red (interferencia, saturación, mal cableado).

👉 Ejemplo de reporte (Windows):

```
Paquetes: enviados = 4, recibidos = 4, perdidos = 0 (0% perdidos),
Tiempos aproximados de ida y vuelta en milisegundos:
    Mínimo = 14ms, Máximo = 18ms, Media = 16ms
```

✅ **Resumen final**

- `ping` es una herramienta de diagnóstico de red basada en ICMP.
- Sirve para probar si un host está disponible, medir latencia y detectar pérdida de paquetes.
- Muy útil en la resolución de problemas de conectividad en redes locales, VPNs e Internet.

---

[🔼](#índice)

---

## **163. Comando ping explicado**

### 1) ¿Qué es `ping`?

`ping` es una herramienta de **diagnóstico de red** que comprueba si hay **conectividad IP** entre tu equipo y otro host (IP o dominio). Lo hace enviando mensajes **ICMP Echo Request** y midiendo si llega un **ICMP Echo Reply** y cuánto tarda.

### 2) ¿Cómo funciona?

1. Tu equipo envía un **ICMP Echo Request** al destino.
2. Si el destino está alcanzable y lo permite, responde con **ICMP Echo Reply**.
3. `ping` muestra:

   - **latencia** (tiempo ida y vuelta, RTT, en ms)
   - **pérdida de paquetes**
   - **TTL** (saltos restantes; útil para inferir distancia en la red)

> Nota: algunos firewalls **bloquean ICMP**, así que que no responda no siempre significa que “no hay Internet”.

### 3) Uso básico (Windows / Linux / macOS)

#### Windows (CMD o PowerShell)

```bat
ping 8.8.8.8          :: 4 pings por defecto
ping -t 8.8.8.8       :: continuo hasta Ctrl+C
ping www.google.com   :: prueba conectividad + DNS
```

#### Linux / macOS (Terminal)

```bash
ping 8.8.8.8          # continuo hasta Ctrl+C
ping -c 4 8.8.8.8     # solo 4 paquetes
ping www.google.com   # prueba conectividad + DNS
```

### 4) ¿Qué te dice la salida?

Ejemplo típico (Windows):

```
Haciendo ping a 8.8.8.8 con 32 bytes de datos:
Respuesta desde 8.8.8.8: bytes=32 tiempo=18ms TTL=117

Estadísticas de ping para 8.8.8.8:
    Paquetes: enviados = 4, recibidos = 4, perdidos = 0 (0% perdidos),
Tiempos aproximados de ida y vuelta en milisegundos:
    Mínimo = 16ms, Máximo = 21ms, Media = 18ms
```

- **tiempo=18ms** → latencia (más bajo = mejor)
- **0% perdidos** → conexión estable
- **TTL=117** → no te preocupes por el valor exacto; sirve para detectar comportamientos raros (por ejemplo, TTL muy bajo puede indicar muchos saltos).

Mensajes comunes:

- **“Tiempo de espera agotado”** → sin respuesta (host caído, firewall, ruta rota).
- **“Destino inaccesible”** → un enrutador intermedio informa que no sabe llegar (típico de problemas de gateway/route).
- **APIPA (169.254.x.x)** en tu IP al hacer `ipconfig`/`ip addr` → fallo de DHCP.

### 5) ¿Cuándo usar `ping`?

- Ver si **un host está en línea** (router, servidor, impresora).
- Medir **latencia** (juegos, VoIP, videollamadas).
- Detectar **pérdida de paquetes** (cable malo, Wi-Fi saturado).
- Separar problemas de **DNS** vs **conectividad**:

  - Si `ping 8.8.8.8` funciona pero `ping google.com` no → problema de **DNS**.

### 6) Recetas de solución de problemas (paso a paso)

1. **Bucle local (stack IP):**

```bash
ping 127.0.0.1
```

2. **Tu propia IP:**

```bash
# Windows
ping <tu-ip>

# Linux/macOS
ip a    # o ifconfig para ver tu IP
ping <tu-ip>
```

3. **Gateway (router):**

```bash
ping 192.168.1.1
```

4. **IP pública conocida (evita DNS):**

```bash
ping 8.8.8.8
```

5. **Nombre de dominio (prueba DNS):**

```bash
ping www.google.com
```

Si falla en (5) pero (4) va bien → revisa **DNS**. Si falla desde (3) → problema local/LAN.

### 7) Opciones útiles por sistema operativo

#### Windows

```bat
ping -t 8.8.8.8        :: continuo
ping -n 10 8.8.8.8     :: 10 intentos
ping -l 1000 8.8.8.8   :: tamaño del payload (bytes)
ping -4/-6 host        :: fuerza IPv4 o IPv6
ping -w 2000 host      :: timeout por eco (ms)
ping -f -l 1472 host   :: DF (no fragmentar) + MTU discovery (útil para VPNs)
ping -a 8.8.8.8        :: resuelve nombre (si es posible)
```

> **Tip MTU**: si `-f` da “necesita fragmentación”, baja `-l` hasta que pase (descubres MTU efectiva).

#### Linux / macOS

```bash
ping -c 10 host        # número de paquetes
ping -i 0.5 host       # intervalo entre pings (s)
ping -s 1000 host      # tamaño del payload (bytes)
ping -4/-6 host        # fuerza IPv4 o IPv6
ping -W 2 host         # timeout de respuesta (s)
ping -I eth0 host      # interfaz origen específica
ping -t 64 host        # TTL (Linux)
```

> Linux suele enviar “para siempre” hasta Ctrl+C; macOS envía por defecto un número finito en algunas versiones (usa `-c` para fijarlo).

### 8) `ping` e IPv6

- Forzar IPv6:

  - **Windows:** `ping -6 ipv6.google.com`
  - **Linux/macOS:** `ping -6 ipv6.google.com` (o `ping6` en sistemas antiguos)

### 9) Usos “pro” y cuidados

- **Monitoreo temporal**: `ping -t` (Win) o `ping` continuo (Linux) mientras ves si hay microcortes.
- **Pruebas de tamaño** (MTU): `-f -l` (Win) o `-M do -s` (Linux: `-M do` = “don’t fragment”).
- **No abuses de “flood”** (`ping -f` en Linux): puede saturar equipos/redes; úsalo solo en **laboratorio controlado**.

### 10) `ping` en scripts (Windows)

```bat
@echo off
set HOST=8.8.8.8
ping -n 1 %HOST% >nul
if errorlevel 1 (
  echo [%date% %time%] %HOST% caido >> ping.log
) else (
  echo [%date% %time%] %HOST% OK >> ping.log
)
```

Crea un log simple de disponibilidad.

### 11) Diferenciar problemas por la salida

- **Request timed out** (tiempo agotado): el paquete salió pero nadie respondió (host apagado/firewall).
- **Destination host unreachable**: un router/tu PC no tiene ruta.
- **Alta variación (jitter)**: picos de latencia irregulares → Wi-Fi saturado, congestión.
- **Pérdida intermitente**: interferencias, enlaces débiles, problemas de ISP.

### 12) Herramientas complementarias

- **tracert / traceroute**: muestra el **camino** (saltos) hasta el destino.
- **pathping (Windows)** o **mtr (Linux)**: combina traceroute + estadísticas por salto (muy útil para localizar dónde se pierde).

### Mini-chuleta (cheat-sheet)

**Windows**

```bat
ping host                 :: básico
ping -t host              :: continuo
ping -n 20 host           :: 20 paquetes
ping -f -l 1472 host      :: probar MTU (DF)
ping -4/-6 host           :: IPv4/IPv6
```

**Linux/macOS**

```bash
ping host                 # continuo
ping -c 20 host           # 20 paquetes
ping -s 1200 host         # tamaño
ping -6 host              # IPv6
ping -I eth0 host         # interfaz
```

### Resumen

- `ping` verifica **alcance**, **latencia** y **pérdidas** entre dos hosts.
- Es la **primera prueba** en casi cualquier diagnóstico de red.
- Úsalo con tamaños y modos adecuados para detectar **MTU**, **DNS**, **rutas** o **interferencias**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**             | **Siguiente 3**    |
| ------------------ | ----------------------- | ------------------ |
| [🏠](../README.md) | [⏪](./7_1_ipconfig.md) | [⏩](./7_3_Dig.md) |
