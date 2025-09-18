| **Inicio**         | **atrás 2**             | **Siguiente 4**            |
| ------------------ | ----------------------- | -------------------------- |
| [🏠](../README.md) | [⏪](./4_2_loopback.md) | [⏩](./4_4_Subnet_mask.md) |

---

## **Índice**

| Temario                                                                    |
| -------------------------------------------------------------------------- |
| [80. ¿Qué es CIDR?](#80-qué-es-cidr)                                       |
| [81. ¿Qué es la notación CIDR de red?](#81-qué-es-la-notación-cidr-de-red) |

# **CIDR**

## **80. ¿Qué es CIDR?**

![CIDR](/img/4_IP_Terminology/CIDR.png "CIDR")

### 📌 1. ¿Qué es el CIDR?

**CIDR (Classless Inter-Domain Routing)** o **Enrutamiento entre Dominios Sin Clase** es un método para asignar y gestionar direcciones IP y subredes de manera más eficiente que el sistema tradicional basado en clases (A, B, C).

En lugar de dividir direcciones en clases fijas, **CIDR permite definir subredes con cualquier cantidad de bits para la parte de red**.
Esto se representa con la notación **slash (`/`)**.

👉 Ejemplo:

- Dirección: `192.168.1.0/24`

  - `/24` = los **24 primeros bits** son parte de la red.
  - Rango de hosts: `192.168.1.1 – 192.168.1.254`.

### 📌 2. ¿Cuáles son los diferentes formatos de direcciones IP?

#### 🔹 En IPv4 (32 bits → 4 octetos)

- Se escribe en decimal con puntos.
  Ejemplo:

```
192.168.1.1
10.0.0.5
172.16.20.30
```

#### 🔹 En IPv6 (128 bits → hexadecimal con dos puntos)

- Mucho más largo, escrito en hexadecimales.
  Ejemplo:

```
2001:0db8:85a3:0000:0000:8a2e:0370:7334
```

Se puede abreviar:

```
2001:db8:85a3::8a2e:370:7334
```

#### 🔹 Formato CIDR

- Une la dirección con el **número de bits de red**.
  Ejemplos:

```
192.168.1.0/24   → 256 direcciones (254 útiles).
10.0.0.0/16      → 65,536 direcciones (65,534 útiles).
2001:db8::/48    → enorme bloque IPv6.
```

### 📌 3. Limitaciones del direccionamiento IP por clases que supera el CIDR

Antes del CIDR, existían **clases rígidas**:

- Clase A → /8 (16 millones de hosts).
- Clase B → /16 (65 mil hosts).
- Clase C → /24 (254 hosts).

🔴 Problema:

- Mucho desperdicio.
  Ejemplo: si una empresa necesitaba 1,000 IPs:
- Una clase C (/24) es muy pequeña.
- Una clase B (/16) es demasiado grande.

✅ Solución con CIDR:

- Se puede dar un bloque ajustado, ej. `/22` = 1,024 direcciones.

### 📌 4. Ventajas del CIDR

- ✅ **Eficiencia en uso de direcciones IP** (no desperdicia como las clases).
- ✅ **Subredes más flexibles** (tamaño exacto según necesidad).
- ✅ **Mejor enrutamiento**: combina varias redes en una sola entrada de tabla (route aggregation o supernetting).
- ✅ **Funciona con IPv4 e IPv6**.

👉 Ejemplo de agregación:
En lugar de anunciar 4 redes:

```
192.168.0.0/24
192.168.1.0/24
192.168.2.0/24
192.168.3.0/24
```

Se puede anunciar una sola:

```
192.168.0.0/22
```

(que cubre de 192.168.0.0 a 192.168.3.255).

### 📌 5. ¿Cómo funciona el CIDR?

CIDR funciona al **definir cuántos bits son red y cuántos son hosts**.

📍 Ejemplo con `192.168.1.0/26`:

- `/26` → los **primeros 26 bits** son red.
- Quedan **6 bits para hosts**.
- Total de direcciones = 2⁶ = 64.
- Direcciones útiles = 64 – 2 = **62 hosts**.

👉 Rango:

```
192.168.1.0   → dirección de red
192.168.1.1   → primer host
192.168.1.62  → último host
192.168.1.63  → broadcast
```

### 📌 6. ¿Cómo se usa el CIDR en IPv6?

En IPv6, **CIDR es esencial** porque no existen clases.

- Siempre se usa notación CIDR para dividir bloques.
- Los prefijos suelen ser más grandes, porque IPv6 tiene direcciones de sobra.

👉 Ejemplos:

- `/128` → una sola dirección.
- `/64` → subred típica de LAN.
- `/48` → bloque grande para una organización.

Ejemplo real:

```
2001:db8:1234::/48
```

Cubre todas las subredes desde `2001:db8:1234:0000::` hasta `2001:db8:1234:ffff::`.

### 📌 7. ¿Cómo puede AWS Support satisfacer sus necesidades de CIDR?

En **AWS (Amazon Web Services)**, el CIDR es clave en redes **VPC (Virtual Private Cloud)**:

- Al crear una VPC, eliges un rango en **CIDR IPv4 o IPv6**.
  👉 Ejemplo: `10.0.0.0/16` → 65,536 direcciones privadas.
- Puedes dividirlo en **subredes más pequeñas**:
  👉 `10.0.1.0/24` para servidores web.
  👉 `10.0.2.0/24` para base de datos.

🔹 Beneficios en AWS con CIDR:

- Flexibilidad para diseñar redes escalables.
- Control de acceso con **ACLs y Security Groups** sobre rangos CIDR.
- Soporte de **CIDR IPv6** (ej. `/56`, `/64`).
- Posibilidad de **expandir rangos** añadiendo bloques CIDR a una VPC existente.

### ✅ Resumen rápido

- **CIDR** = direccionamiento sin clases, con notación `/n`.
- **Resuelve el desperdicio de direcciones** del modelo por clases.
- **Más flexible y eficiente** para subredes y enrutamiento.
- Se usa en **IPv4 e IPv6**.
- En **AWS**, CIDR define la red de tu VPC y subredes.

---

[🔼](#índice)

---

## **81. ¿Qué es la notación CIDR de red?**

La **notación CIDR** (Classless Inter-Domain Routing) es la forma estándar y concisa de expresar una red IP indicando **la dirección base** y **cuántos bits** de esa dirección corresponden a la **parte de red**. Se escribe así:

```
<dirección_de_red>/<prefijo>
```

Ejemplos: `192.168.1.0/24`, `10.0.5.128/26`, `2001:db8::/48`.

### Qué significa cada parte

- **Dirección_de_red**: la dirección IP que representa el inicio del bloque (normalmente la dirección de red).
- **/prefijo**: un número (0–32 en IPv4, 0–128 en IPv6) que indica cuántos bits, desde el comienzo (más significativos), forman la **parte de red**.

  - Los bits restantes son **bits de host** (identifican dispositivos dentro de esa red).

Ejemplo: `192.168.1.0/24` → los primeros **24 bits** son red; quedan **8 bits** para hosts → 2⁸ = 256 direcciones totales, 254 hosts útiles (red + broadcast ocupadas).

### ¿Cómo se interpreta en IPv4? (con ejemplo paso a paso)

Tomemos la dirección con prefijo: **`192.168.10.130/26`**

1. **Prefijo /26** → 26 bits de red, 6 bits de host.
2. **Máscara en decimal**: 26 bits → `255.255.255.192`

   - En binario la máscara: `11111111.11111111.11111111.11000000`

3. **Dirección IP en binario** (último octeto relevante):

   ```
   192.168.10.130  -> 11000000.10101000.00001010.10000010
   ```

4. **Calcular la red** = IP AND máscara (operación bit a bit):

   ```
   11000000.10101000.00001010.10000010  (IP)
   AND
   11111111.11111111.11111111.11000000  (mask)
   = 11000000.10101000.00001010.10000000  -> 192.168.10.128
   ```

   → **Red: 192.168.10.128/26**

5. **Broadcast** = último bit de host a 1 → `192.168.10.191`
6. **Hosts utilizables** = `192.168.10.129` … `192.168.10.190` (62 hosts: 2⁶ − 2)

Ese cálculo (IP AND máscara) es la forma exacta de obtener la red.

### Fórmulas y reglas prácticas

- Bits de host = `32 − prefijo` (IPv4).
- Direcciones totales = `2^(bits de host)`.
- Hosts útiles = `2^(bits de host) − 2` (se restan la dirección de red y la de broadcast).

  - **Excepciones**: `/31` (RFC3021) se usa para enlaces punto a punto y tiene 2 direcciones útiles; `/32` representa **una sola IP** (host-route).

- Máscara a prefijo: por ejemplo `255.255.255.128` → `/25` (porque `128 = 10000000` → 1 bit en ese octeto; 24 + 1 = 25).

### Tabla rápida de prefijos comunes (IPv4)

| CIDR |         Máscara | Direcciones totales | Hosts útiles |
| ---- | --------------: | ------------------: | -----------: |
| /8   |       255.0.0.0 |          16,777,216 |   16,777,214 |
| /16  |     255.255.0.0 |              65,536 |       65,534 |
| /24  |   255.255.255.0 |                 256 |          254 |
| /25  | 255.255.255.128 |                 128 |          126 |
| /26  | 255.255.255.192 |                  64 |           62 |
| /27  | 255.255.255.224 |                  32 |           30 |
| /28  | 255.255.255.240 |                  16 |           14 |
| /29  | 255.255.255.248 |                   8 |            6 |
| /30  | 255.255.255.252 |                   4 |            2 |
| /31  | 255.255.255.254 |                   2 |      2 (p2p) |
| /32  | 255.255.255.255 |                   1 |     1 (host) |

### CIDR en IPv6

- Igual idea: `2001:db8::/48`, `2001:db8:abcd::/64`.
- Prefijos típicos: **/64** para subred LAN (estándar), **/48** o **/56** para organizaciones.
- Ejemplo: `2001:db8:1234::/64` → la parte de red ocupa 64 bits; quedan 64 bits para hosts → direcciones prácticamente ilimitadas para la subred.

### Usos avanzados: supernetting (agregación de rutas)

CIDR permite **agregar** varias redes contiguas en una sola ruta (reduce tamaño de tablas de enrutamiento).
Ejemplo:

- Redes: `192.168.0.0/24`, `192.168.1.0/24` → se pueden anunciar como `192.168.0.0/23` (cubre .0.0 → .1.255).
  Condición: los bloques deben ser contiguos y el prefijo común mayor que la suma, y la dirección base debe estar alineada al nuevo prefijo.

### VLSM (Variable Length Subnet Mask)

CIDR permite usar máscaras de distinta longitud dentro del mismo espacio para ahorrar direcciones. Ejemplo práctico: dentro de `10.0.0.0/24` puedes crear `/25`, `/26`, `/27` de acuerdo a necesidades — eso es VLSM.

Ejemplo de asignación en `192.168.0.0/24`:

- `192.168.0.0/25` (128 direcciones) → departamento A
- `192.168.0.128/26` (64 direcciones) → departamento B
- `192.168.0.192/27` (32 direcciones) → servidor/test

### Cómo comprobar/usar CIDR en la práctica (herramientas)

- **Linux**: `ipcalc 192.168.10.130/26` (muestra red, broadcast, rangos)
- **Python**:

```bash
python - <<'PY'
import ipaddress
net = ipaddress.ip_network("192.168.10.130/26", strict=False)
print("Network:", net.network_address)
print("Broadcast:", net.broadcast_address)
print("Hosts:", net.num_addresses, "-> usable:", net.num_addresses - (1 if net.prefixlen==32 else 2))
print("First host:", list(net.hosts())[0])
PY
```

- **Web / GUI**: IP calculators (ipcalc, subnet-calculator) — útiles para practicar.

### Buenas prácticas y recomendaciones

- Planifica tus subredes con **margen** para crecimiento (no aproximes justo al límite).
- Resume rutas cuando sea posible (agregación) para reducir tamaño de tablas de enrutamiento.
- En IPv6, asume `/64` para subredes LAN salvo casos muy concretos.
- Documenta bloques CIDR, gateways, VLANs y scopes DHCP.

### Resumen rápido

- La notación **CIDR** `A.B.C.D/N` indica **qué parte** de la dirección es red (N bits) y cuál es host.
- Permite flexibilidad y eficiencia (evita desperdicio de direcciones propio del classful).
- Se usa en IPv4 e IPv6 y facilita la agregación de rutas y el diseño de redes con VLSM.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**             | **Siguiente 4**            |
| ------------------ | ----------------------- | -------------------------- |
| [🏠](../README.md) | [⏪](./4_2_loopback.md) | [⏩](./4_4_Subnet_mask.md) |
