| **Inicio**         | **atrás 3**         | **Siguiente 5**                |
| ------------------ | ------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./4_3_CIDR.md) | [⏩](./4_5_Default_gateway.md) |

---

## **Índice**

| Temario                                                                |
| ---------------------------------------------------------------------- |
| [82. ¿Qué es una máscara de subred?](#82-qué-es-una-máscara-de-subred) |

# **Máscara de subred**

## **82. ¿Qué es una máscara de subred?**

![subred](/img/4_IP_Terminology/subred.webp "subred")

Una **máscara de subred** (Subnet Mask) es un número de 32 bits (en IPv4) que acompaña a una dirección IP y sirve para **separar la parte de red de la parte de host** en esa dirección.

👉 Se representa en **decimal con puntos** (ejemplo: `255.255.255.0`) o en **notación CIDR** (`/24`).
👉 La máscara **no es una dirección IP real**, sino una **guía matemática** que le dice al sistema qué porción de la IP identifica la **red** y qué porción identifica al **host**.

📌 Ejemplo:

```
Dirección IP: 192.168.1.25
Máscara:      255.255.255.0   (o /24)
```

En binario:

```
IP:     11000000.10101000.00000001.00011001
Máscara:11111111.11111111.11111111.00000000
```

➡ Los primeros 24 bits son **red** → `192.168.1`
➡ Los últimos 8 bits son **host** → `25`

Eso significa que la red es `192.168.1.0/24` y dentro de ella los hosts van de `192.168.1.1` a `192.168.1.254`.

### 🔹 ¿Cómo funciona la subred?

Una **subred** (sub-network) es una **división lógica de una red más grande**.
Funciona gracias a la máscara de subred, que determina:

- **La dirección de red** (el identificador común de todos los hosts de esa subred).
- **La dirección de broadcast** (usada para enviar mensajes a todos los hosts de la subred).
- **El rango de hosts válidos** (los dispositivos que pueden tener direcciones dentro de esa subred).

📌 Ejemplo práctico:

Supongamos que tenemos la red `192.168.0.0/24` (256 direcciones).
Queremos dividirla en **2 subredes más pequeñas**:

- Usamos `/25` → máscara `255.255.255.128`

  - Primera subred:

    - Red: `192.168.0.0`
    - Hosts: `192.168.0.1` → `192.168.0.126`
    - Broadcast: `192.168.0.127`

  - Segunda subred:

    - Red: `192.168.0.128`
    - Hosts: `192.168.0.129` → `192.168.0.254`
    - Broadcast: `192.168.0.255`

➡ Con la subredización, **una sola red grande** se partió en **dos redes más pequeñas** y administrables.

### 🔹 Beneficios de la subred

El uso de subredes aporta ventajas clave en diseño de redes:

1. **Mejor uso de direcciones IP**

   - Sin subredes, una red clase C (`/24`) solo sería un gran bloque de 254 hosts.
   - Con subredes, puedes dividirlo en bloques más pequeños para ajustarlo a tus necesidades.

2. **Seguridad y aislamiento**

   - Puedes separar departamentos en distintas subredes (Ej: Finanzas en `192.168.1.0/24` y Marketing en `192.168.2.0/24`) para que no se vean directamente entre sí, salvo a través de un router/firewall.

3. **Reducción del tráfico de broadcast**

   - Cada subred tiene su propio dominio de broadcast. Así evitas que todos los dispositivos de la organización reciban mensajes innecesarios.

4. **Escalabilidad y organización**

   - Facilita el crecimiento: puedes ir agregando subredes sin rediseñar toda la red.
   - Permite asignar IPs de forma lógica por área (ejemplo: `10.0.1.0/24` para servidores, `10.0.2.0/24` para usuarios, `10.0.3.0/24` para impresoras, etc.).

### 🔹 Resumen rápido

- La **máscara de subred** separa la IP en parte de red y parte de host.
- La **subredización** divide redes grandes en redes más pequeñas y manejables.
- Los **beneficios** son: ahorro de direcciones, seguridad, menor tráfico de broadcast, escalabilidad y organización.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**         | **Siguiente 5**                |
| ------------------ | ------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./4_3_CIDR.md) | [⏩](./4_5_Default_gateway.md) |
