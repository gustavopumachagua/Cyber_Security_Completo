| **Inicio**         | **atrás 1**              | **Siguiente 3**     |
| ------------------ | ------------------------ | ------------------- |
| [🏠](../README.md) | [⏪](./4_1_localhost.md) | [⏩](./4_3_CIDR.md) |

---

## **Índice**

| Temario                                                                                                                                                                          |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [79. Comprensión de la dirección de bucle invertido y las interfaces de bucle invertido](#79-comprensión-de-la-dirección-de-bucle-invertido-y-las-interfaces-de-bucle-invertido) |

# **loopback**

## **79. Comprensión de la dirección de bucle invertido y las interfaces de bucle invertido**

### 1) ¿Qué es la interfaz loopback y qué hace?

La **interfaz loopback** (normalmente llamada `lo`) es una **interfaz de red virtual** integrada en el sistema operativo cuyo tráfico nunca sale por la tarjeta de red física: se procesa internamente en el kernel y “vuelve” al mismo host.

- Dirección IPv4 por convención: **`127.0.0.1`** (pero todo el bloque `127.0.0.0/8` es loopback).
- Dirección IPv6: **`::1`**.
- Uso: permite que un equipo se comunique consigo mismo usando la pila de red IP completa sin tocar la red física.

### 2) ¿Por qué existe y cómo funciona a bajo nivel?

- Cuando una aplicación envía un paquete a `127.0.0.1`, el kernel lo encamina por la interfaz `lo` en vez de enviarlo al controlador de la NIC.
- Esto permite probar redes y servicios locales, interprocesos que usan sockets TCP/UDP, y ejecutar servicios que solo deben ser accesibles en la máquina local.
- El sistema operativo siempre configura `lo` con `127.0.0.1/8` y `::1` al iniciar (interfaz presente en todos los sistemas modernos).

### 3) Beneficios de usar la dirección loopback (por qué es útil)

1. **Pruebas y desarrollo local**

   - Levantas servidores (web, DB, API) en `127.0.0.1` y pruebas sin exponerlos a la red.
   - Ej.: `curl http://127.0.0.1:3000` para probar tu app Node local.

2. **Seguridad / restricción de acceso**

   - Si un servicio escucha en `127.0.0.1` solo puede ser accedido desde el propio host → reduce superficie de ataque.
   - Buen patrón: frontends públicos en `0.0.0.0:443` (Nginx) y backends internos en `127.0.0.1:8080`.

3. **Aislamiento en contenedores**

   - Cada contenedor tiene su propio _loopback_; `localhost` dentro del contenedor no es el host. Útil para entornos aislados.

4. **Rendimiento**

   - El tráfico loopback no atraviesa la NIC, por lo tanto evita I/O físico y tiene muy baja latencia.

5. **Servicios intermedios / túneles**

   - SSH tunneling y proxies locales usan loopback para exponer remotamente servicios a tu máquina sin abrir puertos en la red.

6. **Direcciones alias y pruebas de red**

   - Puedes asignar varias IPs loopback (ej. `127.0.0.2`, `127.0.0.3`) para testear binding, virtual hosting, licencias, etc.

7. **Diagnóstico y debugging**

   - Uso de `ping 127.0.0.1`, `ss/netstat` y `curl` para verificar que la pila TCP/IP funciona sin cables ni switches.

### 4) Casos prácticos (ejemplos de uso)

- **Desarrollo web**: Nginx reverse proxy en `0.0.0.0:443` → backend Node en `127.0.0.1:3000`.
- **Base de datos local**: Postgres escuchando solo en `127.0.0.1` para evitar conexiones remotas directas.
- **SSH local tunnel**: `ssh -L 9000:127.0.0.1:80 user@remote` → acceder `http://localhost:9000` abre el sitio remoto a través del túnel.
- **Bloquear dominio**: mapear `malware.example.com` a `127.0.0.1` en `/etc/hosts` para que los procesos locales no conecten al dominio.

### 5) Comandos básicos para comprobar loopback

```bash
# Linux / macOS
ip addr show lo            # ver interfaz loopback
ping -c 3 127.0.0.1
ping6 -c 3 ::1

# Ver procesos/puertos escuchando en loopback
ss -tuln | grep 127.0.0.1
sudo lsof -iTCP -sTCP:LISTEN -P -n

# Conectar a un servicio local
curl http://127.0.0.1:8000
openssl s_client -connect 127.0.0.1:443
```

```powershell
# Windows PowerShell
Get-NetIPAddress -AddressFamily IPv4 | Where-Object InterfaceAlias -eq 'Loopback Pseudo-Interface 1'
ping 127.0.0.1
```

### 6) Configuración temporal (añadir direcciones loopback en ejecución)

#### Linux (añadir alias / dirección adicional a `lo`)

```bash
# Añadir dirección IPv4 adicional al loopback
sudo ip addr add 127.0.0.2/8 dev lo

# Verificar
ip addr show lo
```

> Uso típico: pruebas de binding/virtual-hosts o simular múltiples hosts locales.

#### macOS (alias de loopback)

```bash
# Añadir alias temporal
sudo ifconfig lo0 alias 127.0.0.2
# Verificar
ifconfig lo0
```

#### Windows (crear adaptador loopback y asignar IP)

Windows no permite añadir direcciones a `127.0.0.1` directamente; la práctica es **instalar un adaptador loopback virtual**:

1. Abrir **Device Manager** → **Action** → **Add legacy hardware** → Next → "Install the hardware that I manually select" → Network adapters → Microsoft → **Microsoft KM-TEST Loopback Adapter**.
2. En _Network Connections_ configura la IP (ej. `127.0.0.2/8` o una privada si la quieres usar internamente).
3. Alternativa PowerShell para IPs en interfaz existente:

```powershell
# Obtener índice de la interfaz (ejemplo)
Get-NetAdapter
# Añadir IP a interfaz con índice 13
New-NetIPAddress -InterfaceIndex 13 -IPAddress 127.0.0.2 -PrefixLength 8
```

> Nota: manipular 127.0.0.x en Windows puede comportarse distinto; crear una interfaz loopback virtual suele ser más claro si necesitas direcciones internas adicionales.

### 7) Configuración persistente (para que sobreviva reinicios)

#### Debian / Ubuntu clásicas ( `/etc/network/interfaces` )

```text
auto lo
iface lo inet loopback
    up ip addr add 127.0.0.2/8 dev lo   # alias persistente al iniciar
```

(O bien, añade script en `/etc/network/if-up.d/` que ejecute `ip addr add`.)

#### Ubuntu moderno con Netplan (networkd backend)

Netplan normalmente gestiona interfaces físicas; loopback ya está configurado, pero puedes crear un archivo de systemd-networkd para dirección adicional o un servicio systemd que ejecute `ip addr add` en el arranque.

Ejemplo sencillo con systemd unit (persistente):

```ini
# /etc/systemd/system/loopback-alias.service
[Unit]
Description=Add loopback alias
After=network.target

[Service]
Type=oneshot
ExecStart=/sbin/ip addr add 127.0.0.2/8 dev lo
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Luego:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now loopback-alias.service
```

#### macOS persistente

macOS no tiene método directo fácil para persistir `ifconfig lo0 alias` tras reboot; puedes crear un LaunchDaemon que ejecute el comando al inicio.

### 8) Diferencia entre alias `lo:0` y `ip addr add`

- Histórico: `lo:0` era un alias mostrado por ifconfig. Hoy **se recomienda** usar `ip addr add` porque `lo:0` es solo sintaxis de ifconfig y no un dispositivo real.
- Ejemplo recomendado:

```bash
sudo ip addr add 127.0.0.2/8 dev lo   # ✅ moderno y correcto
```

### 9) Usos avanzados y patrones (con ejemplos)

- **Virtual hosting en desarrollo**

  - `/etc/hosts`:

    ```
    127.0.0.1   app1.local
    127.0.0.2   app2.local
    ```

  - Levantas 2 servidores y los pruebas por dominio local.

- **Clustering / IP flotante (testing)**

  - En producción podrías asignar IPs virtuales a interfaces de tipo dummy o usar VRRP. Para tests locales puedes asignar IPs `/32` a loopback para simular nodos.

- **Dummy interface vs loopback**

  - `ip link add dummy0 type dummy` + `ip addr add 10.0.0.1/32 dev dummy0` → útil si quieres una IP que se comporte como una interfaz independiente (routing, ARP) y que no sea del bloque 127/8.

### 10) Precauciones y “gotchas”

- **No intentes usar 127.0.0.x desde otra máquina** — 127.0.0.1 siempre indica “esta máquina”.
- **Cuidado con `/etc/hosts`**: si mapeas `localhost` a otra cosa o quitas `127.0.0.1 localhost`, algunas aplicaciones se rompen.
- **IPv6**: si `localhost` resuelve por defecto a `::1`, `ping localhost` puede usar IPv6; para forzar IPv4 usa `ping -4 localhost` o `ping 127.0.0.1`.
- **Contenedores**: `localhost` dentro del contenedor no es el host; para llegar al host usa `host.docker.internal` (Docker Desktop) o la IP del hostbridge.

### 11) Mini-tutorial: levantar un servicio escuchando solo en loopback (ejemplo práctico)

**Node.js** (archivo `server.js`):

```js
const http = require("http");
http
  .createServer((req, res) => {
    res.end("Solo local\n");
  })
  .listen(3000, "127.0.0.1", () => console.log("Listo en 127.0.0.1:3000"));
```

- Ejecuta: `node server.js`
- Prueba local: `curl http://127.0.0.1:3000` → responde.
- Desde otra máquina no accesible (si intentas `curl http://host-ip:3000`) → no responde porque el servicio no está cifrado en la IP externa.

### 12) Resumen rápido — lo esencial que debes recordar

- **Loopback = interfaz virtual** que permite que la máquina hable consigo misma sin salir por hardware.
- **127.0.0.1** y `::1` son las direcciones loopback más comunes.
- **Beneficios**: pruebas locales, seguridad (acceso local-only), rendimiento (sin NIC), aislamiento en contenedores.
- **Configurar** direcciones adicionales: usar `ip addr add 127.0.0.2/8 dev lo` (Linux), `ifconfig lo0 alias` (macOS), o crear adaptador loopback en Windows.
- Para persistencia usa la configuración del sistema (network scripts, systemd unit, Netplan, LaunchDaemon o configuración de adaptador).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**              | **Siguiente 3**     |
| ------------------ | ------------------------ | ------------------- |
| [🏠](../README.md) | [⏪](./4_1_localhost.md) | [⏩](./4_3_CIDR.md) |
