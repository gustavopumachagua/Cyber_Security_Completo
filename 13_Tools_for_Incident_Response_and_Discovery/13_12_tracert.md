| **Inicio**         | **atrás 11**              | **Siguiente 13**        |
| ------------------ | ------------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./13_11_nslookup.md) | [⏩](./13_13_winhex.md) |

---

## **Índice**

| Temario                                                                  |
| ------------------------------------------------------------------------ |
| [434. Tracert](#434-tracert)                                             |
| [435. Traceroute (tracert) explicado](#435-traceroute-tracert-explicado) |

# **Tracert**

## **434. Tracert**

![Tracert](/img/13_Tools_for_Incident_Response_and_Discovery/Tracert.png "Tracert")

**Tracert** (en Windows) —y su equivalente en Unix/Linux **traceroute**— es una utilidad para **rastrear la ruta que siguen los paquetes** desde tu equipo hasta un host remoto. Te muestra cada salto (router) intermedio y el tiempo que tarda cada salto, lo cual ayuda a diagnosticar problemas de red (latencia, pérdidas, puntos de filtrado).

### 1) ¿Qué hace exactamente tracert/traceroute?

- Envía paquetes con **TTL** (Time To Live) incrementales.

  - El primer paquete tiene TTL=1 → el primer router lo descarta y responde con un **ICMP Time Exceeded**, revelando su dirección.
  - El siguiente paquete TTL=2 llega hasta el segundo router, etc.

- Para cada salto envía normalmente **3 probes** (varía por implementación) y mide el **round-trip time (RTT)** de cada probe.
- Cuando un paquete finalmente alcanza el destino, el destino responde (p. ej. con ICMP echo-reply o con puerto UDP/TCP cerrado, según modo), y la ruta se completa.

Resultado: una lista de hops numerados con **IP/hostname** y **tiempos** (ms).

### 2) Tracert vs traceroute (diferencias clave)

- **Windows**: comando `tracert` (usa ICMP Echo por defecto).

  ```powershell
  tracert example.com
  ```

- **Linux/Unix/macOS**: comando `traceroute` (por defecto usa UDP en muchas distribuciones, pero puede usar ICMP o TCP con opciones).

  ```bash
  traceroute example.com        # UDP por defecto en muchos sistemas
  traceroute -I example.com     # usar ICMP (similar a tracert)
  traceroute -T -p 80 example.com  # usar TCP SYN al puerto 80
  ```

- Opciones y sintaxis difieren; la idea general es la misma.

### 3) Ejemplos prácticos (comandos + salida de ejemplo y explicación)

#### Windows (tracert)

Comando:

```powershell
tracert google.com
```

Salida (simulada):

```
Traza a la dirección google.com [142.250.78.14]
sobre un máximo de 30 saltos:

  1    <1 ms    <1 ms    <1 ms  192.168.1.1
  2     8 ms     7 ms     8 ms  10.10.0.1
  3    12 ms    11 ms    12 ms  isp-gw.example.net [203.0.113.1]
  4    48 ms    50 ms    47 ms  core1.isp.net [198.51.100.10]
  5    *        *        *      Solicitud agotada (timeout).
  6    60 ms    61 ms    60 ms  142.250.78.14
```

Interpretación:

- Columnas: `hop_number  RTT1  RTT2  RTT3  host (IP)`.
- El `*` indica **ninguna respuesta** para ese probe (puede deberse a firewall, rate-limit o dispositivo que no responde a ICMP).
- Si un salto muestra tiempos altos pero el siguiente salto vuelve a tiempos bajos, ese router puede **priorizar** el tratamiento de mensajes ICMP y no realmente estar causando la latencia de paso.

#### Linux (traceroute con ICMP)

Comando:

```bash
sudo traceroute -I -q 3 -m 30 example.com
```

Salida (simulada):

```
traceroute to example.com (93.184.216.34), 30 hops max, 60 byte packets
 1  192.168.1.1  0.335 ms  0.290 ms  0.271 ms
 2  10.10.0.1    7.842 ms  7.612 ms  7.587 ms
 3  isp-gw.example.net (203.0.113.1) 12.103 ms 11.986 ms 12.045 ms
 4  core1.isp.net (198.51.100.10) 48.331 ms 49.710 ms 47.923 ms
 5  * * *
 6  93.184.216.34 60.124 ms 59.998 ms 60.213 ms
```

Mismas interpretaciones que arriba.

### 4) Qué significan las cosas (detalles de interpretación)

- **Hop number**: posición del router en la ruta.
- **RTT (ms)**: tiempo ida y vuelta del probe.
- **Hostname/IP**: identifica el router. Si ves un nombre que no se resuelve, tracert intentará hacer reverse-DNS; usa la opción para evitar DNS si quieres velocidad (ej. `-d` en Windows o `-n` en traceroute).
- **`*` (timeout)**: no llegó respuesta para ese probe. Puede ser:

  - ICMP bloqueado por firewall en ese hop
  - Rate-limiting en el router
  - Paquete perdido

- **Altos RTT en un hop pero normales en posteriores**: generalmente indica que el router prioriza el reenvío sobre responder a probes ICMP; no necesariamente significa que hay congestión real en el camino hacia el destino.
- **Aumento sostenido de RTT a partir de un hop**: indica congestión o enlace lento en ese tramo.
- **Rutas asimétricas**: la ruta de ida y vuelta puede ser distinta; no siempre puedes inferir el camino inverso.

### 5) Opciones útiles (Windows tracert)

- `tracert -d host` → no resuelve nombres (muestra solo IPs) — más rápido.
- `tracert -h max_hops host` → cambiar máximo de hops.
- `tracert -w timeout_ms host` → ajustar tiempo de espera por reply.

Ejemplo:

```powershell
tracert -d -h 20 -w 2000 example.com
```

### 6) Opciones útiles (Linux traceroute)

- `-n` → no resolver DNS (muestra solo IPs).
- `-I` → usar ICMP Echo (como tracert).
- `-T` → usar TCP SYN (útil para atravesar firewalls que bloquean UDP/ICMP).
- `-U` → usar UDP probes.
- `-m max_hops` → límite de saltos.
- `-q probes` → probes por hop (por defecto 3).
- `-w timeout` → tiempo de espera en segundos.
- `-p port` → puerto destino (con TCP/UDP).
- `-A` → mostrar número de AS (en algunas implementaciones).

Ejemplo:

```bash
traceroute -T -p 443 -n -q 2 example.com   # TCP a puerto 443, sin DNS, 2 probes/hop
```

### 7) Trucos y variantes avanzadas

- **Usar TCP traceroute** (`traceroute -T` o `tcptraceroute`) para saltar firewalls que bloquean UDP/ICMP pero permiten TCP a puertos de servicio (80/443).
- **tracepath** (Linux) descubre MTU Path sin privilegios: `tracepath example.com`.
- **mtr** (My Traceroute) combina traceroute + ping en tiempo real (muy útil para ver variación y pérdidas por salto):

  ```bash
  mtr --report example.com
  ```

- **pathping** en Windows combina ping y tracert para análisis de pérdida por salto (Windows).

### 8) Diagnóstico práctico: cómo usar tracert para encontrar problemas

1. **Alta latencia al destino**:

   - Ejecuta `tracert` desde tu máquina.
   - Si la latencia aumenta de forma gradual y se mantiene en routers posteriores → congestión en ese enlace.
   - Si un único hop muestra alta latencia pero posteriores rebotan a valores bajos → probablemente ese router no responde rápido a probes ICMP (no necesariamente fallo).

2. **Pérdida de paquetes / \* en muchos hops**:

   - Si los `*` aparecen desde un hop y persisten hasta el destino → tráfico filtrado o gran pérdida en ese tramo.
   - Usa `mtr` para observar pérdidas con el tiempo.

3. **Problema localizado a cierto ISP**:

   - Si el salto donde aparece el problema pertenece a un ISP intermedio (nombre de host o AS), ponte en contacto con soporte del ISP, proporcionando salida de `tracert` y tiempos.

4. **Comprobación con TCP**:

   - Si ICMP está bloqueado y `tracert` no ayuda, prueba `traceroute -T -p 443 example.com` para ver la ruta usando TCP SYN.

### 9) Limitaciones y puntos a tener en cuenta

- **Firewalls y dispositivos pueden bloquear o limitar ICMP** → muchas veces verás `*` sin que el servicio real esté fallando.
- **Respuestas ICMP no representan necesariamente el tiempo de reenvío de paquetes normales** (routers pueden priorizar tránsito sobre responder probes).
- **Ruta asimétrica**: la ruta de retorno puede ser distinta; traceroute muestra la ruta en la dirección objetivo, no la de vuelta.
- **No es prueba definitiva de que un servicio esté caído** — usa `ping`, `telnet`, `curl` o pruebas de servicio para comprobar la disponibilidad del servicio en el puerto.

### 10) Ejemplos concretos de uso (casos reales)

#### A) Comprobar problema de acceso web

```bash
traceroute -T -p 443 -n example.com
```

Si la ruta llega al datacenter pero la web no responde, el problema puede estar en el servidor web o firewall del destino.

#### B) Diagnóstico de rotación alta de RTT entre dos saltos

- Ejecuta `mtr example.com` durante varios minutos para ver la variabilidad y pérdida.

#### C) Ver la MTU del camino

```bash
tracepath example.com
```

Te dará el menor MTU encontrado en el camino.

### 11) Seguridad y etiqueta

- **No abuses**: ejecutar traceroutes a terceros repetidamente puede considerarse ruido/scan.
- **Proporciona contexto** al ISP o administrador: salida de traceroute + timestamps y una explicación del problema.
- **En pruebas de seguridad**, usa entornos controlados y pide autorización.

### 12) Resumen rápido / cheat-sheet

- Windows:

  - `tracert example.com` — traceroute ICMP por defecto.
  - `tracert -d example.com` — mostrar solo IPs.
  - `tracert -h 20 example.com` — límite hops.

- Linux:

  - `traceroute example.com` — traceroute (UDP por defecto en muchas distros).
  - `traceroute -I example.com` — usar ICMP.
  - `traceroute -T -p 443 example.com` — usar TCP SYN al puerto 443.
  - `traceroute -n example.com` — no resolver DNS.
  - `mtr example.com` — traceroute + ping en tiempo real.
  - `tracepath example.com` — descubrir MTU del camino (sin privilegios).

---

[🔼](#índice)

---

## **435. Traceroute (tracert) explicado**

**Traceroute** (en Linux/macOS `traceroute`; en Windows `tracert`) es una utilidad que revela la **ruta** (los _saltos_ / routers) que siguen los paquetes desde tu máquina hasta un host remoto y mide el **tiempo** a cada salto. Es una herramienta esencial para diagnosticar latencia, pérdida de paquetes y puntos donde el tráfico puede estar siendo filtrado o retrasado.

**Cómo funciona (concepto clave)**

Traceroute explota el campo **TTL** (Time To Live) de los paquetes IP:

1. Envía un paquete con `TTL=1` → el primer router lo decrementa a 0, lo descarta y responde con un **ICMP Time Exceeded**.
2. Envía otro con `TTL=2` → llega al segundo router, que responde igual.
3. Repite incrementando TTL hasta alcanzar el destino (que responde de forma distinta: ICMP Echo Reply o paquete de puerto cerrado según implementación).

   Para cada TTL se envían normalmente 3 probes (depende de la implementación) y se miden los RTT (round-trip time) de cada probe.

### Ejemplos (comandos + salida típica)

#### Windows — `tracert`

```powershell
tracert example.com
```

Salida simulada:

```
Traza a example.com [93.184.216.34] sobre un máximo de 30 saltos:

  1     <1 ms    <1 ms     1 ms  192.168.1.1
  2      7 ms     6 ms     7 ms  10.10.0.1
  3     12 ms    11 ms    12 ms  isp-gw.example.net [203.0.113.1]
  4     48 ms    50 ms    47 ms  core1.isp.net [198.51.100.10]
  5      *        *        *     Solicitud agotada.
  6     60 ms    59 ms    61 ms  93.184.216.34
```

- Cada línea = un _hop_ (router).
- Columnas: `#  RTT1  RTT2  RTT3  host (IP)`
- `*` = sin respuesta (timeout / ICMP bloqueado / rate-limit).

#### Linux — `traceroute` (ICMP)

```bash
sudo traceroute -I example.com
```

Salida simulada:

```
traceroute to example.com (93.184.216.34), 30 hops max, 60 byte packets
 1  192.168.1.1  0.35 ms  0.29 ms  0.27 ms
 2  10.10.0.1    7.84 ms  7.61 ms  7.59 ms
 3  isp-gw.example.net (203.0.113.1) 12.10 ms 11.99 ms 12.05 ms
 4  core1.isp.net (198.51.100.10) 48.33 ms 49.71 ms 47.92 ms
 5  * * *
 6  93.184.216.34 60.12 ms 59.99 ms 60.21 ms
```

### Opciones útiles y variantes

- `tracert -d host` (Windows): no resuelve nombres — muestra solo IPs (más rápido).
- `traceroute -n host` (Linux): no DNS reverse.
- `traceroute -I host` (Linux): usar ICMP Echo (similar a tracert).
- `traceroute -T -p 443 host` (Linux): usar **TCP SYN** al puerto 443 — útil si ICMP/UDP están bloqueados.
- `traceroute -m 40 host`: límite máximo de hops.
- `traceroute -q 1 host`: 1 probe por hop (por defecto 3).
- `tracepath host`: similar a traceroute pero intenta detectar MTU del camino (no necesita root).
- `mtr host`: combina traceroute + ping en tiempo real (muy útil para ver pérdida/variación por hop).
- `tcptraceroute host port`: traceroute sobre TCP si `traceroute -T` no está disponible.

> Nota: en muchas distros `traceroute` necesita privilegios para algunos modos; usa `sudo` según necesites.

### Cómo interpretar la salida — reglas prácticas

- **`* * *` desde un hop y después también** → es probable que ese salto o un filtro intermedio bloquee ICMP/UDP; puede haber pérdida.
- **Un hop con RTT alto pero siguientes bajos** → ese router puede limitar/pausar respuestas ICMP pero no necesariamente está causando la latencia real para el tráfico de usuario.
- **Aumento sostenido de RTT a partir de un hop** → indica congestión o enlace lento en ese tramo.
- **Saltos no resolubles (sin nombre)** → puede ser por reverse-DNS ausente; usa `-n` para velocidad.
- **Ruta asimétrica** → la ruta de ida y vuelta puede ser distinta; traceroute muestra solo la ruta hacia el destino (no la de retorno).

### Diagnóstico práctico: qué comandos usar según problema

1. **No llega al servicio, ¿el host está vivo?**

   ```bash
   ping -c 4 destino
   traceroute -n destino
   ```

2. **ICMP bloqueado (muchos `*`): prueba con TCP**

   ```bash
   sudo traceroute -T -p 443 destino
   # o tcptraceroute destino 443
   ```

3. **Latencia intermitente / pérdida por salto**

   ```bash
   mtr --report destino   # observar pérdida y jitter por salto durante tiempo
   ```

4. **Probar MTU (problemas de fragmentación)**

   ```bash
   tracepath destino
   ```

5. **Traza rápida sin DNS (más rápida)**

   ```bash
   traceroute -n destino
   tracert -d destino   # Windows
   ```

### Limitaciones y precauciones

- Muchos routers **filtran o rate-limitean** ICMP/TTL-expired → aparecen `*` pero el servicio puede funcionar bien.
- Los RTT de traceroute usan respuestas ICMP/TTL expired que no siempre reflejan el tiempo real que tardan los paquetes de aplicación.
- No es prueba definitiva de caída de servicio: complementa con conexiones directas (`telnet host puerto`, `curl`, `nc`).
- No abuses de traceroute en hosts ajenos; repetir traceroutes con alto ritmo puede generar ruido.

### Ejemplo de caso real (interpretación paso a paso)

Salida simplificada:

```
1  192.168.1.1   1 ms   1 ms   1 ms
2  10.10.0.1     8 ms   8 ms   8 ms
3  203.0.113.1  12 ms  12 ms  12 ms
4  198.51.100.10 120 ms 130 ms 118 ms
5  * * *
6  93.184.216.34 121 ms 119 ms 122 ms
```

- Hop 4 muestra salto con RTT \~120 ms → posible enlace internacional / congestión.
- Hop 5 no responde (probablemente router que no responde a ICMP), pero el destino responde con RTT similar → el problema de latencia está en/antes de hop 4 (no en hop 5).
- Informe al ISP del salto 4 aportando la traza y horarios.

### Comandos rápidos (cheat-sheet)

- Linux:

  - `traceroute example.com` (UDP por defecto en muchas distros)
  - `sudo traceroute -T -p 443 example.com` (TCP SYN puerto 443)
  - `traceroute -I example.com` (ICMP)
  - `traceroute -n example.com` (no DNS)
  - `mtr --report example.com` (traceroute+ping)

- Windows:

  - `tracert example.com`
  - `tracert -d example.com` (sin resolver DNS)
  - `tracert -h 20 example.com` (máx hops 20)

### Conclusión

**Traceroute / tracert** es la herramienta para visualizar la **topología lógica** entre tú y un destino: qué routers atraviesas y cuánto tarda cada tramo. No es perfecta (los routers pueden no responder), pero combinada con `ping`, `mtr`, `tracepath` y pruebas de servicio (`curl`, `telnet`, `nc`) forma un kit poderoso para diagnosticar problemas de red y comunicar evidencia al proveedor o al equipo de red.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 11**              | **Siguiente 13**        |
| ------------------ | ------------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./13_11_nslookup.md) | [⏩](./13_13_winhex.md) |
