| **Inicio**         | **atrás 7**            | **Siguiente 9**         |
| ------------------ | ---------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./7_7_TcpDump.md) | [⏩](./7_9_Nslookup.md) |

---

## **Índice**

| Temario                                                                      |
| ---------------------------------------------------------------------------- |
| [173. Página del manual de Traceroute](#173-página-del-manual-de-traceroute) |
| [174. Tracert](#174-tracert)                                                 |
| [175. Traceroute (tracert) explicado](#175-traceroute-tracert-explicado)     |

# **Tracert**

## **173. Página del manual de Traceroute**

![Tracert](/img/7_Troubleshooting_Tools/Tracert.jpg "Tracert")

Voy a explicarte **qué es `traceroute`**, **cómo funciona**, **las opciones más útiles** que verás en su _man page_, **cómo interpretar la salida** y **ejemplos prácticos** (Linux / macOS / Windows). Incluiré notas sobre variantes (ICMP / UDP / TCP) y herramientas relacionadas.

### ¿Qué es `traceroute`?

`traceroute` es una utilidad de diagnóstico de red que muestra el **camino (hops)** que siguen los paquetes desde tu equipo hasta un host remoto, y el tiempo (latencia) hacia cada salto. Es muy usada para localizar problemas de enrutamiento, puntos con alta latencia o dónde se está perdiendo tráfico.

### Cómo funciona (resumen técnico)

- `traceroute` aprovecha el campo **TTL** (Time To Live) de la cabecera IP. Envía paquetes con TTL=1, TTL=2, TTL=3, …; cada router que reenvía el paquete decrementa TTL y cuando TTL llega a 0 **descarta** el paquete y **envía un ICMP Time Exceeded** de vuelta al emisor. Con esas respuestas `traceroute` va obteniendo la IP (y normalmente el nombre) de cada router intermedio. Cuando el paquete finalmente llega al destino, éste responde (por ejemplo con ICMP Echo Reply si se usó ICMP, o con ICMP Destination Unreachable si se usaron probes UDP a puertos altos) y la traza termina.

### Implementaciones y protocolos (qué envía por defecto)

- En Unix/Linux/macOS el comportamiento **por defecto** históricamente es enviar **probes UDP** a puertos altos (33434–33534) y leer las respuestas ICMP. Muchas implementaciones permiten cambiar el protocolo de los probes (ICMP Echo, TCP SYN, etc.).
- En **Windows** el comando equivalente es `tracert` y envía **ICMP Echo Request** por defecto. (Por eso a veces `traceroute` y `tracert` muestran comportamientos diferentes frente a firewalls).

### Sintaxis / “página del manual” — los elementos que verás en el `man traceroute`

En el _man page_ verás secciones típicas: `SYNOPSIS`, `DESCRIPTION`, `OPTIONS`, `EXAMPLES`, `BUGS`.

Ejemplo de **sinopsis** simplificada:

```
traceroute [opciones] destino [max_ttl]
```

Opciones comunes (pueden variar ligeramente entre implementaciones — revisa `man traceroute` en tu sistema):

- `-n` : no resolver nombres DNS (muestra sólo IPs) — acelera la salida.
- `-m max_ttl` : límite máximo de saltos (TTL), por defecto suele ser 30.
- `-q nqueries` : número de probes por salto (por defecto 3).
- `-w timeout` : tiempo de espera por respuesta (segundos) para cada probe.
- `-f first_ttl` : empezar la traza con este TTL en lugar de 1.
- `-p port` : puerto base para probes UDP (útil si el destino filtra puertos altos).
- `-I` : usar **ICMP Echo Request** (en vez de UDP).
- `-T` : usar **TCP SYN** (muy útil cuando UDP/ICMP están filtrados).
- `-6` o `traceroute6` : traceroute para IPv6.

> Nota: las opciones `-I` y `-T` o `-P` (protocol selector) existen en muchas implementaciones; la disponibilidad y la sintaxis exacta dependen de la versión de `traceroute` en tu distro. Siempre confirma con `man traceroute` o `traceroute --help`.

### Ejemplos prácticos (comandos listos)

#### 1) Uso básico (Linux/macOS — UDP por defecto)

```bash
traceroute ejemplo.com
```

#### 2) No resolver nombres (más rápido)

```bash
traceroute -n ejemplo.com
```

#### 3) Forzar ICMP (útil si UDP está bloqueado)

```bash
sudo traceroute -I ejemplo.com
```

#### 4) Usar TCP SYN al puerto 80 (más parecido a lo que hace un navegador)

```bash
sudo traceroute -T -p 80 ejemplo.com
```

_(`sudo` puede ser necesario para enviar paquetes “raw” en algunos sistemas.)_

#### 5) Cambiar número de probes por hop y tiempo de espera

```bash
traceroute -q 2 -w 2 -m 40 ejemplo.com
# 2 probes por salto, 2s timeout, hasta 40 hops
```

#### 6) Windows (`tracert`)

```bat
tracert -d -h 30 -w 1000 ejemplo.com
# -d: no resolver nombres; -h: max hops; -w: timeout en ms
```

La doc oficial de Microsoft muestra la sintaxis y opciones.

### Ejemplo de salida y cómo leerla

Salida típica (ejemplo simplificado):

```
traceroute to ejemplo.com (93.184.216.34), 30 hops max, 60 byte packets
 1  192.168.1.1 (192.168.1.1)  0.42 ms  0.39 ms  0.36 ms
 2  10.0.0.1 (10.0.0.1)        1.23 ms  1.20 ms  1.16 ms
 3  203.0.113.1 (203.0.113.1) 10.51 ms 10.48 ms 10.46 ms
 4  * * *
 5  93.184.216.34 (93.184.216.34) 30.12 ms 29.99 ms 29.87 ms
```

- La primera columna es el **número de salto** (hop).
- La siguiente es la **IP** (y si no usaste `-n`, el nombre DNS).
- Las tres cifras son los **tiempos de ida y vuelta (RTT)** para cada probe enviado a ese salto (normalmente 3 probes por hop).
- `*` indica **sin respuesta** dentro del timeout (puede deberse a firewall, rate-limiting, el router no genera ICMP, o simplemente pérdida temporal).

### Por qué aparecen `* * *` (asteriscos) — causas comunes

- El router intermedio **no envía** ICMP Time Exceeded (filtrado por firewall).
- El host responde demasiado lento o hay pérdida.
- El dispositivo prioriza reenvío sobre responder a las sondas (rate-limiting).
- Rutas asimétricas o load balancing hacen que los probes lleguen por caminos distintos y el router no responda a esos probes.

  Interpretar `*` requiere contexto: prueba cambiando protocolo (ICMP/TCP) o usar `-n` para ver sólo IPs y evitar problemas DNS.

### Variantes y herramientas relacionadas

- `tracert` en Windows (ICMP).
- `tracepath` (Linux): parecido a traceroute pero **no requiere privilegios**; útil si no puedes usar `sudo`.
- `tcptraceroute` / `traceroute -T` y **`mtr`** (My Traceroute): `mtr` combina traceroute y ping de forma continua y es muy útil para ver pérdida y latencia en el tiempo.

### Consejos prácticos / buenas prácticas

- Si ves muchos `*`, cambia de protocolo: prueba `-I` (ICMP) y `-T` (TCP) para saltar filtros.
- Usa `-n` para acelerar y evitar bloqueos por DNS.
- Aumenta `-w` o `-q` si la red es lenta o está saturada.
- Si necesitas un trazado persistente y continuo, usa `mtr`.
- Ten en cuenta que los proveedores (ISPs) a veces ocultan o limitan respuestas ICMP, así que no assumes automáticamente que un `*` indica un fallo de enrutamiento definitivo.

### Donde leer la página del manual en tu sistema

En una terminal Unix / Linux / macOS:

```bash
man traceroute
# o para IPv6
man traceroute6
# o ver ayuda rápida:
traceroute --help
```

En Windows (símbolo del sistema):

```bat
tracert /?
```

---

[🔼](#índice)

---

## **174. Tracert**

### 🔹 ¿Qué es?

`tracert` (Trace Route) es una utilidad de diagnóstico en **Windows** que permite rastrear la ruta que siguen los paquetes IP hasta un host destino, mostrando los **saltos intermedios (routers)** y el **tiempo de respuesta** en cada uno.

👉 En Windows, `tracert` utiliza **ICMP Echo Request** (igual que `ping`) para obtener la información.

### 🔹 Sintaxis

```bat
tracert [-d] [-h saltos_máx] [-j lista] [-w tiempo_esp] [-R] [-S origen] [-4] [-6] destino
```

### 🔹 Parámetros principales

| Parámetro   | Descripción                                                                          |
| ----------- | ------------------------------------------------------------------------------------ |
| `destino`   | Dirección IP o nombre de host que deseas rastrear.                                   |
| `-d`        | No resuelve nombres DNS, solo muestra las direcciones IP (más rápido).               |
| `-h`        | Especifica el número máximo de saltos a seguir (por defecto: 30).                    |
| `-j lista`  | Ruta suelta: permite especificar una lista de hosts intermedios (máx. 9).            |
| `-w tiempo` | Tiempo de espera (en milisegundos) para cada respuesta (por defecto: 4000 ms = 4 s). |
| `-R`        | Traza la ruta de ida y vuelta (IPv6 solamente).                                      |
| `-S origen` | Especifica la dirección de origen (IPv6).                                            |
| `-4`        | Fuerza el uso de IPv4.                                                               |
| `-6`        | Fuerza el uso de IPv6.                                                               |

### 🔹 Ejemplos prácticos

#### 1. Ruta básica a un sitio web

```bat
tracert google.com
```

➡ Muestra todos los saltos (routers) hasta llegar a `google.com`.

#### 2. Mostrar solo direcciones IP (más rápido)

```bat
tracert -d google.com
```

➡ Evita la resolución DNS de cada hop, mostrando únicamente IPs.

#### 3. Limitar número máximo de saltos

```bat
tracert -h 10 google.com
```

➡ Solo rastrea hasta 10 saltos.

#### 4. Aumentar tiempo de espera (10 segundos)

```bat
tracert -w 10000 google.com
```

➡ Espera más tiempo antes de marcar `* * *` por falta de respuesta.

#### 5. Forzar IPv6

```bat
tracert -6 google.com
```

➡ Traza la ruta usando IPv6.

#### 6. Forzar IPv4

```bat
tracert -4 google.com
```

➡ Traza la ruta usando IPv4.

✅ **Resumen**:

- `tracert` funciona igual que `traceroute`, pero en Windows usa ICMP.
- Sintaxis simple: `tracert [opciones] destino`.
- Los parámetros más usados son: `-d`, `-h`, `-w`, `-4`, `-6`.
- Útil para diagnosticar problemas de conectividad o latencia en la red.

---

[🔼](#índice)

---

## **175. Traceroute (tracert) explicado**

### 1) ¿Qué hace `traceroute` / `tracert`?

Ambos comandos te muestran los **saltos (routers)** entre tu equipo y un host destino y el **tiempo** que tardan los paquetes en llegar a cada salto. Sirve para localizar:

- saltos con alta latencia,
- puntos donde se pierde tráfico,
- rutas asimétricas o bloqueos por firewalls.

**Diferencia clave:**

- `traceroute` (Linux/macOS) tradicionalmente envía **probes UDP** a puertos altos y espera mensajes ICMP _Time Exceeded_. Puede usar ICMP o TCP si lo pides.
- `tracert` (Windows) usa **ICMP Echo Request** por defecto.

### 2) ¿Cómo funciona (básico técnico)?

1. Envía paquetes con TTL=1; el primer router lo decrementa a 0 y responde con **ICMP Time Exceeded** → así obtiene la IP del primer hop y su RTT.
2. Envía paquetes con TTL=2 → responde el segundo hop, y así sucesivamente hasta llegar al destino (o hasta `max_ttl`).
3. Mide el RTT (round-trip time) de cada probe; por defecto se envían 3 probes por hop (puede cambiarse).

### 3) Comandos / sintaxis (ejemplos listos)

#### Linux / macOS (`traceroute`)

```bash
# Uso básico (UDP por defecto)
traceroute ejemplo.com

# No resolver DNS (más rápido)
traceroute -n ejemplo.com

# Forzar ICMP
sudo traceroute -I ejemplo.com

# Usar TCP SYN al puerto 80 (útil si UDP/ICMP están bloqueados)
sudo traceroute -T -p 80 ejemplo.com

# Cambiar max hops y probes por hop
traceroute -m 40 -q 2 -w 3 ejemplo.com   # hasta 40 hops, 2 probes, timeout 3s
```

#### Windows (`tracert`)

```bat
tracert google.com
tracert -d google.com           # no resolver nombres
tracert -h 20 google.com        # max 20 hops
tracert -w 5000 google.com      # timeout 5000 ms por intento
tracert -4 ejemplo.com          # forzar IPv4
tracert -6 ejemplo.com          # forzar IPv6
```

#### IPv6

```bash
# Linux: traceroute -6 destino
traceroute -6 ipv6.google.com
# Windows:
tracert -6 ipv6.google.com
```

### 4) Opciones importantes explicadas

- `-n` : no resolver nombres DNS (muestra sólo IPs) → mucho más rápido.
- `-m <max_ttl>` : máximo número de saltos (por defecto \~30).
- `-q <nqueries>` : cuántos probes enviar por salto (por defecto 3).
- `-w <timeout>` : segundos de espera por respuesta a cada probe.
- `-I` : usar ICMP Echo Request (Linux/macOS).
- `-T` / `-p <port>` : usar TCP SYN a un puerto específico (útil para caminos donde ICMP/UDP están filtrados). `sudo` puede ser necesario.
- `-6` : usar IPv6.

### 5) Interpretación de la salida — ejemplo y lectura

Salida típica (Linux `traceroute -n ejemplo`):

```
traceroute to ejemplo.com (93.184.216.34), 30 hops max
 1  192.168.1.1  0.42 ms  0.39 ms  0.36 ms
 2  10.0.0.1     1.23 ms  1.20 ms  1.16 ms
 3  203.0.113.1 10.51 ms 10.48 ms 10.46 ms
 4  * * *
 5  93.184.216.34 30.12 ms 29.99 ms 29.87 ms
```

- Columna 1 = número de salto (hop).
- IP (o nombre si no usas `-n`).
- Luego los tiempos (típicamente 3 valores = 3 probes).
- `*` = sin respuesta (timeout) — puede ser firewall, rate limiting, o router que no envía ICMP Time Exceeded.

#### Significados frecuentes de patrones

- **Tiempos crecientes suavemente**: latencia acumulada normal (cada salto añade su propio retardo).
- **Un hop con RTT alto y los siguientes bajos**: ese dispositivo responde lento o prioriza tráfico sobre responder a sondas; no siempre implica problema con la ruta final.
- **`* * *` repetido**: el dispositivo intermedio no responde ICMP o hay bloqueo; si el destino final responde más adelante, el tráfico está pasando aunque el router no reporte.
- **Último salto faltante** (todo `*`): destino bloquea o no hay conectividad.

### 6) Códigos y mensajes especiales

Algunas implementaciones muestran códigos cuando reciben ICMP unreachable (depende de la versión):

- `!N` network unreachable, `!H` host unreachable, `!P` protocol unreachable, `!F` fragmentation needed, `!X` administratively prohibited, etc.

  (La disponibilidad y significado exacto dependen de la versión de `traceroute`; usa `man traceroute` para ver la lista en tu sistema.)

### 7) Casos reales y cómo diagnosticar (pasos prácticos)

#### Caso A — “Mi web va lenta desde mi casa”

1. `traceroute -n midominio.com` → localiza el salto donde aumenta la latencia.
2. Si alta latencia a partir del hop 5 → problema en ISP intermedio; contacta soporte ISP con número de hop y timestamps.
3. Si hay `* * *` desde un hop y nunca llega al destino → posible bloqueo por firewall del target o del ISP.

#### Caso B — “No puedo alcanzar un host”

1. `ping host` → si no responde, `traceroute -n host` → ver si hay path.
2. Si la traza muere en un hop cercano al origen → verifica tu gateway/firewall local.
3. Si muere lejos → solicitar a administrador de la red remota o ISP.

#### Caso C — Firewalls y filtros

- Si `traceroute` tradicional (UDP) muestra `*` pero `traceroute -I` (ICMP) o `-T -p 443` (TCP) sí muestran ruta, entonces UDP probes están filtrados; usa TCP si quieres emular tráfico de una aplicación.

### 8) Herramientas relacionadas y cuando usarlas

- **`tracert`** → Windows (ICMP).
- **`tracepath`** → Linux: similar a traceroute pero **no requiere privilegios**.
- **`tcptraceroute`** / `traceroute -T` → cuando ICMP/UDP están bloqueados, prueba con TCP SYN al puerto de servicio (ej. 80 o 443).
- **`mtr`** → combina traceroute + ping en tiempo real (muestra pérdida/RTT por hop continuamente). Muy útil para ver fluctuaciones.
- **Paris-traceroute** → para medir rutas en redes con balanceo multipath (reduce artefactos por ECMP).

### 9) Ejemplos concretos listos para copiar/pegar

#### Linux — traza básica y rápida

```bash
# Rápida (sin resolución DNS)
traceroute -n google.com

# Usando ICMP (a veces salta firewalls)
sudo traceroute -I google.com

# Usando TCP al puerto 443 (muy útil para servidores web)
sudo traceroute -T -p 443 ejemplo.com
```

#### Windows — tracert ejemplos

```bat
tracert -d google.com          # sin resolución DNS
tracert -h 20 -w 2000 google.com # max 20 saltos, timeout 2000 ms
tracert -4 ejemplo.com         # forzar IPv4
tracert -6 ipv6.google.com     # forzar IPv6
```

#### Diagnóstico continuo con mtr (Linux)

```bash
# Interactivo
mtr google.com

# Salida en formato report para guardar
mtr -r -c 100 google.com   # 100 pings en total y luego imprime reporte
```

### 10) Limitaciones y puntos a tener en cuenta

- **No mide exactamente la latencia de tráfico real de la aplicación** (las respuestas ICMP/UDP/TCP a probes no siempre se manejan como tráfico normal).
- **ICMP rate limiting** en routers puede mostrar latencias altas o `*` aunque el tráfico real fluya bien.
- **Rutas asimétricas**: ruta de ida ≠ ruta de vuelta; `traceroute` solo muestra el camino de ida de los probes o las IPs que responden, no garantiza que las respuestas sigan la misma ruta.
- **MPLS / túneles / NAT**: pueden ocultar saltos reales o mostrar IPs “internas” de proveedores.

### 11) Buenas prácticas al usar traceroute

- Usa `-n` para acelerar cuando no necesitas nombres DNS.
- Prueba distintos protocolos (UDP / ICMP / TCP) si ves muchos `*`.
- Para diagnóstico continuo usa `mtr`.
- Anota fechas/hours y guarda salidas (`traceroute host > trazado.txt`) para compartir con soporte ISP.
- Si reportas a soporte, incluye hop número y las latencias promedio, no solo “va lento”.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**            | **Siguiente 9**         |
| ------------------ | ---------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./7_7_TcpDump.md) | [⏩](./7_9_Nslookup.md) |
