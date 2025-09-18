| **Inicio**         | **atrás 2**          | **Siguiente 4**     |
| ------------------ | -------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./13_2_NMAP.md) | [⏩](./13_4_ARP.md) |

---

## **Índice**

| Temario                                                    |
| ---------------------------------------------------------- |
| [416. ¿Qué es el ping?](#416-qué-es-el-ping)               |
| [417. Comando ping explicado](#417-comando-ping-explicado) |

# **Ping**

## **416. ¿Qué es el ping?**

![Ping](/img/13_Tools_for_Incident_Response_and_Discovery/Ping.png "Ping")

### ¿Qué es _ping_?

**Ping** es una utilidad de red (comando) usada para comprobar la **conectividad** entre tu equipo y otro host en la red (por ejemplo: un servidor, una IP, un router). Envía paquetes ICMP _Echo Request_ y espera _Echo Reply_ para verificar si el objetivo responde y medir tiempos de ida y vuelta (latencia).

### Definición corta

**Ping** = comando que usa mensajes ICMP Echo Request/Reply para:

1. comprobar si un host está accesible,
2. medir la latencia (round-trip time, RTT), y
3. detectar pérdida de paquetes.

### ¿Cómo funciona el ping? (paso a paso)

1. Tu equipo envía un paquete ICMP **Echo Request** al host destino.
2. Cada router que cruza decrementa el campo **TTL** del paquete.
3. Si el destino recibe el Echo Request y está configurado para responder, devuelve un ICMP **Echo Reply**.
4. El comando mide el tiempo entre envío y recepción (RTT).
5. Repite según la cantidad de paquetes solicitada y calcula estadísticas (min/avg/max/desviación).

Puntos importantes:

- Ping usa **ICMP**, no TCP/UDP, por lo que comprueba la capa de red/ICMP, no si un servicio TCP (por ejemplo HTTP) está escuchando en un puerto.
- Si un firewall bloquea ICMP, un ping puede fallar aunque el servicio (p. ej. web) esté disponible.

### Cómo utilizar el comando `ping` (ejemplos prácticos)

#### Linux / macOS (ejemplos)

- Pingar 4 paquetes y salir:

```bash
ping -c 4 example.com
```

- Resultado típico (Linux):

```
PING example.com (93.184.216.34) 56(84) bytes of data.
64 bytes from 93.184.216.34: icmp_seq=1 ttl=56 time=10.2 ms
64 bytes from 93.184.216.34: icmp_seq=2 ttl=56 time=10.4 ms
64 bytes from 93.184.216.34: icmp_seq=3 ttl=56 time=10.1 ms
64 bytes from 93.184.216.34: icmp_seq=4 ttl=56 time=10.3 ms

--- example.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 10.116/10.284/10.459/0.136 ms
```

Explicación de líneas clave:

- `64 bytes from ...`: paquete recibido (éxito) — incluye `icmp_seq` (número del paquete), `ttl` y `time` (RTT en ms).

- `4 packets transmitted, 4 received, 0% packet loss`: si hay pérdida, indica problemas de red.

- `rtt min/avg/max/mdev`: estadísticas de latencia (mínima, promedio, máxima y desviación).

- Pingar continuamente (útil para monitoreo manual; detén con `Ctrl+C`):

```bash
ping example.com
```

- Cambiar tamaño del paquete (útil para probar MTU/fragmentación):

```bash
ping -s 1200 example.com   # 1200 bytes de carga ICMP (más IP/ICMP overhead)
```

- Forzar IPv4 o IPv6:

```bash
ping -4 example.com   # forzar IPv4 (en sistemas que soportan)
ping -6 example.com   # usar IPv6 (a veces ping6 o ping -6)
```

- Esperar menos tiempo por respuesta (timeout por paquete):

```bash
ping -W 1 -c 3 example.com   # en Linux: espera máximo 1 segundo por respuesta
```

#### Windows (ejemplos)

- Pingar 4 veces (por defecto Windows no es infinito):

```powershell
ping -n 4 example.com
```

- Forzar tamaño del paquete:

```powershell
ping -l 1200 example.com
```

- Continuo en Windows (similar a Linux `ping` sin `-c`):

```powershell
ping example.com -t   # detener con Ctrl+C
```

### ¿Cuándo sería útil utilizar el comando `ping`?

- Comprobar si un host está **vivo** (respondiente) en la red local o Internet.
- Medir **latencia** entre tu máquina y otro host (útil para videojuegos, VOIP, videollamadas).
- Detectar **pérdida de paquetes** (problemas de red, congestión, enlaces inestables).
- Verificar **resolución DNS** (si `ping example.com` falla por nombre, prueba con la IP).
- Diagnóstico inicial antes de ejecutar herramientas más avanzadas (traceroute, nmap, mtr).
- Probar cambios de red (p. ej. después de configurar firewall o rutas).

### ¿Qué es una prueba de ping?

Una **prueba de ping** es ejecutar `ping` (uno o muchos paquetes) contra un host para obtener:

- si responde (sí/no),
- latencias por paquete (ms),
- porcentaje de pérdida de paquetes,
- a veces el `TTL` y el tamaño de paquete, y
- estadísticas agregadas (min/avg/max/mdev).

Las pruebas pueden ser:

- **Cortas** (p. ej. `-c 4`) para comprobaciones rápidas.
- **Prolongadas/continuas** para observar variaciones a lo largo del tiempo (útil para detectar intermitencias).

Herramientas relacionadas para pruebas más avanzadas:

- `mtr` — combina ping + traceroute en tiempo real (mide pérdida por salto).
- `pingplotter` / `smokeping` — para gráficas y monitoreo histórico.

### ¿Qué te dice la prueba de ping? — interpretación práctica

1. **Host no responde (100% lost / request timed out):**

   - Host apagado, IP incorrecta, firewall bloqueando ICMP o problemas de enrutamiento.
   - Prueba: `ping` la IP directa, `traceroute` para ver dónde cae el tráfico, comprobar reglas de firewall.

2. **Responde con latencias bajas y 0% pérdida:**

   - Buena conectividad (red local o link de calidad).

3. **Responde pero con alta latencia (p. ej. >100 ms si debería ser local):**

   - Congestión, enlace saturado, mala ruta, peering pobre entre ISPs, o distancia física.

4. **Hay pérdida de paquetes (ej. 10%, 50%):**

   - Red inestable, buffers saturados, problemas en un salto intermedio. Si la pérdida ocurre sólo en un salto (ver con `mtr`), ese salto es probable culpable.

5. **RTT con alta variabilidad (jitter):**

   - No sólo la media importa: mucha diferencia entre min y max (o gran `mdev`) indica jitter, crítico para voz y video.

6. **TTL bajo o decreciente (comparando respuestas):**

   - TTL indica cuántos saltos quedan. Conocer TTL por defecto de sistemas comunes (Windows/Unix) puede ayudar a estimar cuántos saltos ha pasado el paquete. Pero no es una medida exacta (cada SO tiene TTL inicial distinto).

7. **Respuestas fragmentadas o “message too long” al aumentar tamaño (`-s`):**

   - Indica problemas con MTU o enlace que no permite fragmentación — útil para depurar túneles VPN o enlaces con PMTUD roto.

### Limitaciones y cosas a tener en cuenta

- **ICMP puede estar bloqueado** por firewalls — un ping fallido no siempre significa que todo el host esté caído.
- Ping no prueba servicios específicos (por ejemplo, que un servidor web esté funcionando) — usa `curl`, `telnet host 80` o `nmap` para eso.
- Algunos dispositivos responden a ping con baja prioridad o lo limitan (rate-limit), dando una falsa impresión de pérdida.
- No confíes solo en un ping aislado: para diagnósticos usa múltiples pruebas, `mtr`, `traceroute` y revisa logs.

### Diagnóstico rápido con ping — checklist

1. `ping -c 4 example.com` → ¿responde? ¿IP resuelta correctamente?
2. Si no responde, `ping -c 4 93.184.216.34` (usar IP directa) → si responde, problema DNS.
3. Si no responde por IP, ejecutar `traceroute example.com` / `tracert` en Windows → ver dónde cae la ruta.
4. Ejecutar `mtr example.com` para ver pérdida por salto.
5. Revisar firewalls (local y remoto) y políticas de ICMP.

### Resumen rápido

- **Ping** es la primera herramienta para comprobar conectividad y medir latencia/pérdida usando ICMP Echo.
- Útil para diagnósticos rápidos, pero **no** es definitivo: complementa con `traceroute`, `mtr`, y pruebas de servicios.
- Interpreta resultados mirando **respuestas**, **porcentaje de pérdida** y **estadísticas RTT (min/avg/max/mdev)**.
- Recuerda las limitaciones: ICMP bloqueado, rate-limiting y diferencias entre probar conectividad vs probar un servicio.

---

[🔼](#índice)

---

## **417. Comando ping explicado**

### Comando `ping` — explicación bien detallada con ejemplos

`ping` es la herramienta básica para comprobar **conectividad** en redes. A continuación tienes desde la definición técnica hasta ejemplos prácticos, opciones comunes, interpretación de salidas, uso avanzado y consejos de diagnóstico.

### ¿Qué es `ping`?

`ping` es un programa de red que envía **mensajes ICMP Echo Request** a un host y espera **ICMP Echo Reply**. Sirve para:

- Ver si un host está accesible (vivo).
- Medir la **latencia** (RTT — round-trip time) entre tu equipo y el host.
- Detectar **pérdida de paquetes**.
  No prueba servicios TCP/UDP; solo la capacidad de que paquetes ICMP lleguen y vuelvan.

### ¿Cómo funciona (técnicamente, paso a paso)?

1. El comando crea un paquete ICMP Echo Request y lo envía a la IP destino.
2. Cada router por el que pasa decrementa el **TTL** (time-to-live).
3. Si el destino acepta ICMP, devuelve un ICMP Echo Reply.
4. `ping` mide el tiempo entre envío y recepción: ese es el RTT (ms).
5. Repite según el número de paquetes solicitados y calcula estadísticas (min/avg/max/mdev).

### Sintaxis básica

```
ping [opciones] destino
```

`destino` puede ser un nombre de dominio (`example.com`) o una IP (`8.8.8.8`).

### Ejemplos prácticos (Linux/macOS)

1. Ping simple (envía paquetes hasta que lo detengas):

```bash
ping example.com
# Detener con Ctrl+C
```

2. Ping con número fijo de paquetes (4 paquetes):

```bash
ping -c 4 example.com
```

Salida típica:

```
PING example.com (93.184.216.34): 56 data bytes
64 bytes from 93.184.216.34: icmp_seq=1 ttl=56 time=10.2 ms
64 bytes from 93.184.216.34: icmp_seq=2 ttl=56 time=10.4 ms
64 bytes from 93.184.216.34: icmp_seq=3 ttl=56 time=10.1 ms
64 bytes from 93.184.216.34: icmp_seq=4 ttl=56 time=10.3 ms

--- example.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3005ms
rtt min/avg/max/mdev = 10.116/10.284/10.459/0.136 ms
```

3. Cambiar tamaño del paquete (útil para probar MTU/fragmentación):

```bash
ping -c 3 -s 1200 example.com
```

4. Forzar IPv4 o IPv6:

```bash
ping -4 example.com   # IPv4
ping -6 example.com   # IPv6 (o usar ping6 en algunos sistemas)
```

5. Espera por paquete más corta (timeout por paquete):

```bash
ping -c 3 -W 1 example.com   # espera 1 segundo como máximo por respuesta
```

### Ejemplos (Windows)

- Ping 4 paquetes (por defecto Windows usa 4):

```powershell
ping example.com
```

- Forzar número de paquetes:

```powershell
ping -n 10 example.com   # 10 paquetes
```

- Cambiar tamaño del paquete:

```powershell
ping -l 1200 example.com
```

- Ping continuo (detener con Ctrl+C):

```powershell
ping example.com -t
```

### Opciones útiles (GNU/Linux)

- `-c <num>` — enviar `<num>` paquetes y salir.
- `-i <seg>` — intervalo entre paquetes (segundos).
- `-s <bytes>` — tamaño de la carga ICMP (sin contar cabeceras).
- `-W <seg>` — tiempo máximo en segundos a esperar por cada respuesta.
- `-t <ttl>` — establecer TTL inicial del paquete.
- `-4`/`-6` — forzar IPv4 o IPv6.
- `-q` — salida silenciosa (solo estadísticas finales).

### ¿Qué te dice la prueba de `ping`? — cómo interpretar

Salida y significado:

- `64 bytes from 1.2.3.4: icmp_seq=1 ttl=56 time=10.2 ms`
  → Host respondió; `time` = RTT; `ttl` puede dar indicios de cuántos saltos quedan (no exacto).

- `4 packets transmitted, 4 received, 0% packet loss`
  → No hubo pérdida. Si hay % > 0, indica paquetes perdidos (problema de red/congestión/firewall).

- `rtt min/avg/max/mdev = 10.116/10.284/10.459/0.136 ms`
  → Estadísticas: latencia mínima, promedio, máxima y desviación. Jitter → diferencia min/max y mdev.

Interpretaciones comunes:

- **0% pérdida y RTT bajo**: buena conectividad.
- **Alta latencia** (ej. >100 ms en LAN): congestión, malas rutas, enlace saturado o distancia física.
- **Pérdida de paquetes**: red inestable, problemas en un salto intermedio o filtros que descartan ICMP.
- **Ping falla por nombre pero funciona por IP**: problema DNS.
- **Ping falla por IP**: host caído, firewall bloqueando ICMP o problema de enrutamiento.

### Limitaciones de `ping`

- Usa **ICMP**: muchos firewalls bloquean o rate-limit ICMP → ping fallido no implica servicio caído.
- No prueba servicios (por ejemplo HTTP) — sólo la capa de red/ICMP.
- Algunos dispositivos responden con baja prioridad (pueden retrasar replies).
- No da información de la ruta (usar `traceroute` / `tracert`) ni pérdida por salto (usar `mtr`).

### Diagnóstico con `ping` — checklist rápido

1. `ping -c 4 destino` — ¿responde?
2. Si no responde, `ping -c 4 <IP>` — ¿funciona por IP? (prueba DNS).
3. Si tampoco por IP: `traceroute destino` / `tracert destino` para ver dónde cae la ruta.
4. Para pérdida intermitente: `mtr destino` (combina traceroute + ping en tiempo real).
5. Si aumentas tamaño `-s` y recibes "Message too long" o fragmentación: revisar MTU y PMTUD.

### Uso avanzado y ejemplos útiles

1. **Monitoreo simple (continuo) para ver fluctuaciones)**:

```bash
ping example.com
# o en Windows: ping example.com -t
```

Observa variaciones en `time=` y pérdida.

2. **Comprobar resolución DNS vs IP**:

```bash
ping -c 3 example.com && ping -c 3 93.184.216.34
```

Si el primero falla y el segundo no → problema DNS.

3. **Comprobar MTU path (fragmentación)**:
   Empieza con paquetes grandes y reduce hasta que pasen:

```bash
ping -c 3 -s 1472 example.com
# 1472 + 28 (IP+ICMP) = 1500 bytes => típico MTU Ethernet
```

4. **Script Bash para monitoreo básico**:

```bash
#!/bin/bash
host="example.com"
for i in {1..12}; do
  echo "Intento $i: $(date) - $(ping -c1 -W1 $host | grep 'time=' || echo 'NO RESPONDE')"
  sleep 5
done
```

### Buenas prácticas

- Usa `ping` como **primer paso** de diagnóstico, no como único.
- Complementa con `traceroute`, `mtr`, `netstat`, `ss`, `curl` o `nmap` según necesites.
- Si administras la red, considera habilitar ICMP selectivamente y documentar políticas para evitar bloqueos que dificulten debugging.
- Para pruebas de disponibilidad públicas, monitorea desde varios puntos (a veces un ISP bloquea ICMP).

### Resumen rápido

- `ping` comprueba conectividad y mide latencia/pérdida usando ICMP Echo.
- Sintaxis básica: `ping destino` o `ping -c 4 destino`.
- Interpreta: `time=` (RTT), `% packet loss`, `min/avg/max/mdev`.
- Limitaciones: ICMP bloqueado, no prueba servicios, puede haber rate-limiting.
- Herramientas complementarias: `traceroute`, `mtr`, `curl`, `nmap`.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**          | **Siguiente 4**     |
| ------------------ | -------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./13_2_NMAP.md) | [⏩](./13_4_ARP.md) |
