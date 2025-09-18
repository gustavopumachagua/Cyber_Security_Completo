| **Inicio**         | **atrás 6**                          | **Siguiente 8**                               |
| ------------------ | ------------------------------------ | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./3_6_Basics_of_NAS_and_SAN.md) | [⏩](./3_8_Public_vs_Private_IP_Addresses.md) |

---

## **Índice**

| Temario                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------------------- |
| [74. Conceptos básicos de redes: ¿Qué son las subredes IPv4?](#74-conceptos-básicos-de-redes-qué-son-las-subredes-ipv4) |
| [75. Subredes](#75-subredes)                                                                                            |

---

# **Conceptos básicos de subredes**

## **74. Conceptos básicos de redes: ¿Qué son las subredes IPv4?**

![subredes IPv4](/img/3_Networking_Knowledge/subredes_IPv4.png "subredes IPv4")

### 📌 1. Direcciones IP y binarios

Una **dirección IP versión 4 (IPv4)** tiene **32 bits** (4 octetos).
Se escribe en **decimal con puntos**, pero en realidad es binaria.

👉 Ejemplo:

```
192.168.1.10 (decimal)
11000000.10101000.00000001.00001010 (binario)
```

- Cada octeto va de `0` a `255` (2^8 = 256 valores).
- Ejemplo de rangos:

  - `00000000` = 0
  - `11111111` = 255

### 📌 2. Parte de red y parte de host

Una IP se divide en dos partes:

- **Parte de red**: identifica la red.
- **Parte de host**: identifica los equipos dentro de la red.

👉 Esto se define con la **máscara de red** (subnet mask).

Ejemplo:

```
IP: 192.168.1.10
Máscara: 255.255.255.0
Binario: 11111111.11111111.11111111.00000000
```

- Los **1** = parte de red (primeros 24 bits).
- Los **0** = parte de host (últimos 8 bits).
- Resultado: red = `192.168.1.0`, host = `10`.

### 📌 3. Clases de direcciones IP (histórico en IPv4)

En el modelo **classful** (ya casi no se usa, pero importante para teoría):

| Clase | Primer octeto | Rango de IPs                | Máscara por defecto | Uso típico        |
| ----- | ------------- | --------------------------- | ------------------- | ----------------- |
| A     | 0 – 127       | 0.0.0.0 – 127.255.255.255   | 255.0.0.0 (/8)      | Redes muy grandes |
| B     | 128 – 191     | 128.0.0.0 – 191.255.255.255 | 255.255.0.0 (/16)   | Redes medianas    |
| C     | 192 – 223     | 192.0.0.0 – 223.255.255.255 | 255.255.255.0 (/24) | Redes pequeñas    |
| D     | 224 – 239     | Multicast                   | —                   | Multicast         |
| E     | 240 – 255     | Reservado/Experimental      | —                   | Investigación     |

### 📌 4. Máscaras de red y número de hosts

La fórmula para hosts disponibles:

$$
\text{Hosts} = 2^{\text{bits de host}} - 2
$$

(Quitamos 2: red y broadcast).

#### 🔹 Clase A

- Máscara por defecto: **255.0.0.0** → `/8`
- Bits de host = 24
- Hosts posibles = 2^24 - 2 = **16,777,214**

#### 🔹 Clase B

- Máscara: **255.255.0.0** → `/16`
- Bits de host = 16
- Hosts posibles = 2^16 - 2 = **65,534**

#### 🔹 Clase C

- Máscara: **255.255.255.0** → `/24`
- Bits de host = 8
- Hosts posibles = 2^8 - 2 = **254**

### 📌 5. Cómo crear subredes en IPv4

**Subnetting** = tomar bits de la parte de host y convertirlos en parte de red.
Esto crea **más redes más pequeñas**, pero con menos hosts cada una.

Ejemplo con **192.168.1.0/24 (Clase C)**:

- Por defecto: 1 red con 254 hosts.
- Si tomamos **1 bit extra** (nuevo /25):

  - Máscara = 255.255.255.128
  - Redes creadas = 2
  - Cada red con 126 hosts.

👉 Detalle:

```
192.168.1.0/25     → hosts: 192.168.1.1 - 192.168.1.126 (broadcast 192.168.1.127)
192.168.1.128/25   → hosts: 192.168.1.129 - 192.168.1.254 (broadcast 192.168.1.255)
```

Otro ejemplo:

De una red **10.0.0.0/8 (Clase A)** quieres /16:

- /8 → 16 millones de hosts.
- /16 → 65,534 hosts por subred.
- Redes posibles: 2^(16-8) = 256 redes.

### 📌 6. Resumen visual rápido

- **IP**: 32 bits (red + host).
- **Máscara**: define qué parte es red.
- **Clases**: A (/8), B (/16), C (/24).
- **Subnetting**: robar bits a hosts para crear más redes.
- **Hosts por red**: 2^(bits host) - 2.

---

[🔼](#índice)

---

## **75. Subredes**

### 1) ¿Qué es una subred?

Una **subred** (subnetwork) es una porción de una red IP mayor creada al “partir” la parte de _host_ de una dirección IP en varias redes más pequeñas. Sirve para:

- Segmentar redes (security / performance).
- Reducir broadcast domain.
- Asignar rangos de IPs por departamentos, racks, servicios, etc.

Cada subred está definida por:

- Una **dirección de red** (network address).
- Una **máscara de subred** (subnet mask) o **prefijo** en notación CIDR (`/n`).
- Una **dirección de broadcast**.
- Un rango de direcciones **host** utilizables (salvo casos especiales /31 y /32).

### 2) Notación: decimal punteado, binario y CIDR

- Una IP IPv4 = **32 bits** = 4 octetos (ej.: `192.168.10.130`).
- En binario: `11000000.10101000.00001010.10000010`.
- La **máscara** indica qué bits son red (`1`) y qué bits son host (`0`):

  - Ej.: `255.255.255.0` → `11111111.11111111.11111111.00000000` → `/24`.

**CIDR**: `192.168.10.0/24` significa que los primeros 24 bits son la parte de red.

### 3) Cómo calcular la dirección de red (bitwise AND)

La red se obtiene haciendo AND bit a bit entre la IP y la máscara.

Ejemplo: `IP = 192.168.10.130`, `mask = 255.255.255.192 (/26)`.

En binario:

```
IP:   11000000.10101000.00001010.10000010  (192.168.10.130)
Mask: 11111111.11111111.11111111.11000000  (255.255.255.192)
AND:  11000000.10101000.00001010.10000000  -> 192.168.10.128  (dirección de red)
```

- Broadcast = último host + 1 en la porción de host → `192.168.10.191`.
- Rango usable = `192.168.10.129` … `192.168.10.190`.

### 4) Fórmulas rápidas

- Bits host = `32 - prefijo`
- Direcciones totales = `2^(bits host)`
- Hosts utilizables = `2^(bits host) - 2` (restamos red y broadcast).

  - **Excepciones**: `/31` (RFC3021) se usa para enlaces punto a punto (2 direcciones, ambos hosts válidos) y `/32` representa una sola IP (host único).

- Número de subredes al subdividir: `2^(nuevo_pref - prefijo_original)`.

### 5) Tabla práctica (CIDR, máscara, hosts, bloque)

| Prefijo | Máscara dotted  | Wildcard      | Bits host | Hosts usuables | Tamaño bloque (incremento) |
| ------- | --------------- | ------------- | --------- | -------------- | :------------------------- |
| /8      | 255.0.0.0       | 0.255.255.255 | 24        | 16,777,214     | 256                        |
| /9      | 255.128.0.0     | 0.127.255.255 | 23        | 8,388,606      | 128                        |
| /10     | 255.192.0.0     | 0.63.255.255  | 22        | 4,194,302      | 64                         |
| /11     | 255.224.0.0     | 0.31.255.255  | 21        | 2,097,150      | 32                         |
| /12     | 255.240.0.0     | 0.15.255.255  | 20        | 1,048,574      | 16                         |
| /13     | 255.248.0.0     | 0.7.255.255   | 19        | 524,286        | 8                          |
| /14     | 255.252.0.0     | 0.3.255.255   | 18        | 262,142        | 4                          |
| /15     | 255.254.0.0     | 0.1.255.255   | 17        | 131,070        | 2                          |
| /16     | 255.255.0.0     | 0.0.255.255   | 16        | 65,534         | 256                        |
| /17     | 255.255.128.0   | 0.0.127.255   | 15        | 32,766         | 128                        |
| /18     | 255.255.192.0   | 0.0.63.255    | 14        | 16,382         | 64                         |
| /19     | 255.255.224.0   | 0.0.31.255    | 13        | 8,190          | 32                         |
| /20     | 255.255.240.0   | 0.0.15.255    | 12        | 4,094          | 16                         |
| /21     | 255.255.248.0   | 0.0.7.255     | 11        | 2,046          | 8                          |
| /22     | 255.255.252.0   | 0.0.3.255     | 10        | 1,022          | 4                          |
| /23     | 255.255.254.0   | 0.0.1.255     | 9         | 510            | 2                          |
| /24     | 255.255.255.0   | 0.0.0.255     | 8         | 254            | 256                        |
| /25     | 255.255.255.128 | 0.0.0.127     | 7         | 126            | 128                        |
| /26     | 255.255.255.192 | 0.0.0.63      | 6         | 62             | 64                         |
| /27     | 255.255.255.224 | 0.0.0.31      | 5         | 30             | 32                         |
| /28     | 255.255.255.240 | 0.0.0.15      | 4         | 14             | 16                         |
| /29     | 255.255.255.248 | 0.0.0.7       | 3         | 6              | 8                          |
| /30     | 255.255.255.252 | 0.0.0.3       | 2         | 2              | 4                          |

> **Bloque (incremento)**: es el tamaño de la red en el octeto donde cambia (ej.: /26 → incremento 64 → subredes comienzan en .0, .64, .128, .192).

### 6) Ejemplo práctico — dividir `192.168.1.0/24` en `/26`

`/24` → 254 hosts; si lo queremos en subredes `/26` (cada una con 62 hosts útiles):

Subredes resultantes:

1. `192.168.1.0/26` → net: `192.168.1.0`, broadcast `192.168.1.63`, hosts `192.168.1.1 - 192.168.1.62`
2. `192.168.1.64/26` → net: `192.168.1.64`, broadcast `192.168.1.127`, hosts `192.168.1.65 - 192.168.1.126`
3. `192.168.1.128/26` → net: `192.168.1.128`, broadcast `192.168.1.191`, hosts `192.168.1.129 - 192.168.1.190`
4. `192.168.1.192/26` → net: `192.168.1.192`, broadcast `192.168.1.255`, hosts `192.168.1.193 - 192.168.1.254`

Explicación del incremento: máscara `/26` = `255.255.255.192` → último octeto 192 → `256 - 192 = 64` → subredes cada 64 direcciones.

### 7) VLSM (Variable Length Subnet Mask) — ejemplos reales

**VLSM** permite asignar subredes de distinto tamaño según necesidad (ahorrar direcciones).

**Escenario**: tienes `192.168.0.0/24` y requisitos:

- Red A: 100 hosts
- Red B: 50 hosts
- Red C: 25 hosts
- Red D: 10 hosts
- Red E: 5 hosts

**Ordena de mayor a menor** y asigna la subred más pequeña que cumpla con cada requisito:

- 100 hosts → necesita al menos `/25` (128 direcciones → 126 hosts útiles). Asignar `192.168.0.0/25` → hosts `192.168.0.1 - 192.168.0.126`.
- 50 hosts → necesita `/26` (64 direcciones → 62 hosts útiles). Siguiente bloque libre: `192.168.0.128/26` → hosts `192.168.0.129 - 192.168.0.190`.
- 25 hosts → necesita `/27` (32 addresses → 30 hosts). Siguiente bloque: `192.168.0.192/27` → hosts `192.168.0.193 - 192.168.0.222`.
- 10 hosts → necesita `/28` (16 addresses → 14 hosts). Next: `192.168.0.224/28` → hosts `192.168.0.225 - 192.168.0.238`.
- 5 hosts → needs `/29` (8 addresses → 6 hosts). Next: `192.168.0.240/29` → hosts `192.168.0.241 - 192.168.0.246`.

Resultado: todo cabe dentro del `/24`. **Regla**: siempre planifica dejando margen para crecimiento.

### 8) ¿Cómo calcular rápido la subred/máscara adecuada para N hosts?

1. Calcula `host_needed = N + 2` (suma red + broadcast).
2. Encuentra el menor `h` tal que `2^h >= host_needed`.
3. Prefix = `32 - h`. Ejemplo: 50 hosts → host_needed = 52 → 2^6 = 64 ≥ 52 → h=6 → prefix = 26 (`/26`).

## 9) Casos especiales

- **/31**: útil para enlaces punto a punto (RFC3021). No hay broadcast; ambas direcciones pueden usarse por los dos extremos.
- **/32**: representa una sola IP (host route). Usado para políticas o rutas individuales.

### 10) Comandos y herramientas para verificar/subnetear (práctico)

- `ipcalc` (Linux) — ejemplo:

```bash
ipcalc 192.168.10.130/26
# Muestra: Network, Netmask, Broadcast, Hosts/Range
```

- `sipcalc` — alternativa con más opciones.
- `python` (rápido): `python -c "import ipaddress; print(ipaddress.ip_network('192.168.1.0/26').hosts())"`
- Linux: ver interfaces / IP:

```bash
ip addr show
ip route show
```

- Windows: PowerShell:

```powershell
Get-NetIPAddress
# Para comprobar un cálculo, usar módulos o herramientas externas como ipcalc
```

### 11) Resumen de atajos prácticos (cheat-sheet)

- **Para obtener la red**: IP AND Máscara.
- **Incremento**: `256 - valor_octeto_máscara` en el octeto que cambia. Ej.: máscara `255.255.255.192` → `256 - 192 = 64`.
- **Hosts**: `2^(32 - prefijo) - 2` (salvo /31, /32).
- **Subredes desde prefijo mayor**: `2^(nuevo_pref - prefijo_original)`.

### 12) Errores comunes y buenas prácticas

- **No asumir** que la primera/última dirección son usables (red y broadcast).
- **No usar** SMB/hosts críticos en subredes sin planificación de crecimiento.
- Planificar con **margen de crecimiento** (por ejemplo, asignar `/25` para 100 hosts).
- **Documentar** rangos, gateways, VLANs, DHCP scopes.
- Usar **VLSM** para eficiencia y **resumir rutas (supernet)** en el routing para reducir tabla de enrutamiento.

### 13) Ejercicios rápidos (práctica)

1. ¿Cuál es la red, broadcast y rango usable para `10.10.5.77/28`?

   - Solución rápida: `/28` → máscara `255.255.255.240`, bloque = 16 → subredes: .0,.16,.32,.48,.64,.80...
   - `10.10.5.77` está en bloque `10.10.5.64/28`. Network = `10.10.5.64`, broadcast = `10.10.5.79`, hosts = `10.10.5.65 - 10.10.5.78`.

2. Divide `172.16.0.0/22` en subredes `/24`. ¿Cuántas subredes y cuáles?

   - /22 → subredes /24 = `2^(24-22) = 4` subredes: `172.16.0.0/24`, `172.16.1.0/24`, `172.16.2.0/24`, `172.16.3.0/24`.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**                          | **Siguiente 8**                               |
| ------------------ | ------------------------------------ | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./3_6_Basics_of_NAS_and_SAN.md) | [⏩](./3_8_Public_vs_Private_IP_Addresses.md) |
