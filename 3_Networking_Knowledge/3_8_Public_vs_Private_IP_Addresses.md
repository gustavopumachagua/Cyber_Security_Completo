| **Inicio**         | **atrás 7**                         |
| ------------------ | ----------------------------------- |
| [🏠](../README.md) | [⏪](./3_7_Basics_of_Subnetting.md) |

---

## **Índice**

| Temario                                                                                                        |
| -------------------------------------------------------------------------------------------------------------- |
| [76. Direcciones IP públicas y privadas](#76-direcciones-ip-públicas-y-privadas)                               |
| [77. ¿Cuál es la diferencia entre IP pública y privada?](#77-cuál-es-la-diferencia-entre-ip-pública-y-privada) |

---

# **Direcciones IP públicas y privadas**

## **76. Direcciones IP públicas y privadas**

![IP públicas y privadas](/img/3_Networking_Knowledge/Direcciones_IP_publicas_y_privadas.jpg "IP públicas y privadas")

### 📌 1. ¿Qué es una dirección IP pública?

Una **IP pública** es la dirección que **tu proveedor de Internet (ISP)** te asigna para que tu dispositivo o red sea **accesible desde Internet**.
Es única en todo el mundo: no puede repetirse porque sirve para identificarte en la red global.

👉 Ejemplo:

- Si entras a Google y buscas “mi IP”, puede salir algo como:

  ```
  179.7.250.123
  ```

  Esa es tu IP pública, la que ve Internet.

🔹 Características:

- Es **enrutada en Internet**.
- Permite la comunicación con servicios externos.
- Puede ser **fija (estática)** o **cambiada por el ISP (dinámica)**.

### 📌 2. ¿En qué se diferencia una IP pública de una IP externa?

En realidad, **IP pública y IP externa significan lo mismo**.
Ambos términos se refieren a la dirección visible desde Internet.
Se usa “externa” porque es la IP que ve el mundo exterior (no dentro de tu red local).

### 📌 3. ¿Se pueden rastrear las direcciones IP públicas?

Sí ✅.

Con una IP pública se puede rastrear:

- El **país y ciudad aproximada**.
- El **ISP** que te da servicio.
- A veces, la **zona geográfica** (barrio).

👉 Ejemplo: si tu IP pública es `179.7.250.123`, una búsqueda puede mostrar:

```
Ubicación aproximada: Lima, Perú
Proveedor: Movistar Perú
```

⚠️ Pero: **no revela directamente tu nombre o dirección exacta**, aunque con orden judicial, tu ISP sí puede dar esa información.

### 📌 4. ¿Qué es una dirección IP privada?

Una **IP privada** es la que usan tus dispositivos dentro de una **red local (LAN)**, como tu casa u oficina.
Estas IP **no son enrutadas en Internet** y sirven solo para identificar dispositivos dentro de la red interna.

👉 Ejemplo en tu casa:

```
Router: 192.168.1.1
Laptop: 192.168.1.10
Celular: 192.168.1.20
Impresora: 192.168.1.30
```

Todos comparten la **misma IP pública** en Internet, pero dentro de casa tienen IPs privadas distintas.

### 📌 5. Direcciones IP privadas, locales e internas

En la práctica, son lo mismo:

- **Privada** → definida por la norma (RFC 1918).
- **Local** → usada dentro de tu red casera.
- **Interna** → opuesta a la externa (Internet).

👉 Ejemplo:

`192.168.1.15` = IP privada/local/interna de tu laptop en casa.

### 📌 6. ¿Se pueden rastrear las direcciones IP privadas?

No 🚫.

Una IP privada **no viaja por Internet**.
Nadie desde fuera puede ver que tu laptop tiene `192.168.1.15`.
Lo que ven es tu IP pública del router.

### 📌 7. Principales diferencias entre IP públicas y privadas

| Característica      | IP Pública 🌍                   | IP Privada 🏠                |
| ------------------- | ------------------------------- | ---------------------------- |
| Visibilidad         | Visible en Internet             | Solo dentro de la red local  |
| Asignación          | ISP                             | Router/DHCP                  |
| Rango               | Cualquier IP que no sea privada | Rango reservado RFC 1918     |
| Ejemplo             | 179.7.250.123                   | 192.168.1.15                 |
| ¿Se puede rastrear? | Sí, ubicación aproximada        | No, solo el admin de red     |
| Seguridad           | Más vulnerable (expuesta)       | Más segura (oculta tras NAT) |

### 📌 8. Intervalos de direcciones IP privadas (según RFC 1918)

- **Clase A**: `10.0.0.0 – 10.255.255.255`
- **Clase B**: `172.16.0.0 – 172.31.255.255`
- **Clase C**: `192.168.0.0 – 192.168.255.255`

👉 Ejemplos reales:

- Universidades suelen usar `10.x.x.x`.
- Oficinas grandes usan `172.16.x.x`.
- Casas suelen usar `192.168.x.x`.

Todas las demás IPs son **públicas**, excepto las reservadas para usos especiales (ej. `127.0.0.1` loopback).

### 📌 9. ¿Cómo comprobar qué tipo de dirección IP utilizo?

- Para tu **IP privada (local)**:

  - En **Windows**:

    ```
    ipconfig
    ```

  - En **Linux/Mac**:

    ```
    ifconfig
    ```

  Ejemplo salida: `192.168.1.10`

- Para tu **IP pública (externa)**:

  - Abre Google y escribe: “mi IP”
  - O visita sitios como `whatismyip.com`

### 📌 10. Proteger tu IP (VPN como Avast SecureLine VPN)

Una **VPN (Red Privada Virtual)** como Avast SecureLine:

- Cifra tu conexión.
- Cambia tu IP pública real por la de un servidor seguro en otro país.
- Protege tu privacidad en Wi-Fi públicos.

👉 Ejemplo:

Sin VPN:

```
Tu IP: 179.7.250.123 (Lima, Perú)
```

Con VPN en EE.UU.:

```
Tu IP: 104.28.25.1 (Nueva York, USA)
```

✅ **En resumen**:

- La **IP pública** es la dirección que ve Internet, puede rastrearse y es única.
- La **IP privada** es solo para tu red local, no se rastrea desde fuera.
- Usar una **VPN** te ayuda a proteger tu IP pública y tu ubicación real.

---

[🔼](#índice)

---

## **77. ¿Cuál es la diferencia entre IP pública y privada?**

### ¿Qué es una IP pública?

- **Definición:** dirección IPv4/IPv6 que **es única en Internet** y está **enrutada globalmente**.
- **Asignación:** la da tu **ISP** (o tu proveedor cloud).
- **Visibilidad:** cualquier servidor/servicio en Internet puede ver esa IP (por ejemplo: sitios web, APIs).
- **Ejemplo:** `203.0.113.45` (dirección pública ficticia reservada para ejemplos).

**Uso típico:** la IP pública es la “cara” de tu red frente a Internet. Si tienes un servidor web accesible desde cualquier parte, necesitará una IP pública (o un servicio que la represente, como un balanceador/CloudFlare).

### ¿Qué es una IP privada?

- **Definición:** dirección usada **solo dentro de una red local (LAN)**. **No** está enrutada en Internet.
- **Asignación:** la da tu **router** (DHCP) o se configura manualmente.
- **Rangos (RFC 1918):**

  - `10.0.0.0 – 10.255.255.255` (/8)
  - `172.16.0.0 – 172.31.255.255` (/12)
  - `192.168.0.0 – 192.168.255.255` (/16)

- **Ejemplo:** `192.168.1.10`, `10.0.0.5`

**Uso típico:** PCs, móviles, impresoras y NAS en tu casa/empresa usan IPs privadas para comunicarse entre sí.

### Diferencia técnica clave (enrutamiento)

- **IP pública →** enrutada por Internet: los routers globales saben cómo entregarla.
- **IP privada →** NO se enruta por Internet; los routers públicos la ignoran. Para conectar dispositivos privados con Internet se usa **NAT** (Network Address Translation) en el router.

### ¿Cómo comparten todos los dispositivos una sola IP pública?

Mediante **NAT / PAT (Port Address Translation)**:

- Tu router traduce las IPs privadas/puertos internos a su **IP pública** y a puertos externos únicos.
- Ejemplo sencillo (traducción):

| Host interno | Socket interno     | Traducción (router)      | Socket externo     |
| ------------ | ------------------ | ------------------------ | ------------------ |
| PC1          | 192.168.1.10:52345 | 203.0.113.45:40001 (map) | 203.0.113.45:40001 |
| Teléfono     | 192.168.1.20:52345 | 203.0.113.45:40002 (map) | 203.0.113.45:40002 |

Así 100 dispositivos pueden “compartir” una sola IP pública, diferenciados por puertos.

### Ejemplo práctico — casa vs servidor público

**Casa (uso típico):**

- Router WAN (IP pública: `203.0.113.45`) ←→ ISP
- LAN:

  - Laptop `192.168.1.10`
  - Móvil `192.168.1.20`
  - NAS `192.168.1.50`

- Navegas a `https://example.com` → desde Internet solo ven `203.0.113.45`. El router NAT traduce y enruta la respuesta al dispositivo correcto.

**Servidor accesible desde Internet:**

- Opción A: Asignar **IP pública** directamente al servidor (en cloud/ISP).
- Opción B: Mantener el servidor en LAN con IP privada y usar **port forwarding** en el router: mapear `203.0.113.45:80` → `192.168.1.50:80`.
- Opción C: Usar **reverse proxy / CDN** (CloudFlare, CloudFront) que tenga IP pública y proteja el servidor interno.

### ¿Se pueden rastrear las IP públicas?

Sí, parcialmente:

- Se puede saber **ISP**, **país**, **ciudad aproximada** y qué rango de red usa tu ISP (geolocalización por IP).
- **No** da por sí sola el nombre o dirección exacta de una persona; para eso un juez puede pedir al ISP la asociación IP ↔ cliente (logs DHCP).
- Logs en servidores (web, SSH) registran la IP pública desde la que se conectó un cliente.

Las IP privadas **no** son rastreables desde Internet porque no salen en los paquetes hacia Internet.

### IP pública estática vs dinámica

- **Estática:** la misma IP se mantiene en el tiempo (útil para servidores).
- **Dinámica:** cambia con el tiempo (ISP reasigna), común en conexiones residenciales.
- Para un servidor casero sin IP estática puedes usar **DDNS** (dynamic DNS) o contratar IP fija.

### IPv6 — cómo cambia la historia

- En IPv6 las direcciones **globales** son públicas y enrutables (ej.: `2001:db8::1`).
- Existe el concepto de **Unique Local Addresses (ULA)** `fc00::/7` (similar a IPv4 privada) para redes internas, pero la filosofía IPv6 promueve direcciones globales por dispositivo; se maneja seguridad con firewall, no con NAT obligatoria.

### Seguridad: ¿proteger IP pública vs privada?

- **IP pública expuesta** → servicios visibles a Internet (más riesgo): proteger con firewall, limitar puertos, autenticación, TLS/SSH keys, actualizar.
- **IP privada** → más “oculta”, pero no invulnerable: si un dispositivo interno está comprometido, el atacante puede usar la IP privada para moverse lateralmente.
- **NAT no es una solución de seguridad completa**: sigue implementando firewalling y segmentación (VLANs) y aplicar principios de menor privilegio.

### Cómo comprobar qué tipo de IP estás usando (comandos)

**Ver tu IP privada (local):**

- Windows:

```powershell
ipconfig
```

- Linux/macOS:

```bash
ip addr show            # o: ifconfig (si instalado)
```

**Ver tu IP pública (lo que ve Internet):**

- Desde terminal:

```bash
curl ifconfig.me
# o
curl -s https://api.ipify.org
```

- Desde navegador: buscar “mi IP” o visitar `https://whatismyipaddress.com`.

### Resumen rápido (tabla)

| Atributo                      |                 IP pública | IP privada                                |
| ----------------------------- | -------------------------: | ----------------------------------------- |
| Enrutada en Internet          |                         Sí | No                                        |
| Visible desde Internet        |                         Sí | No                                        |
| Asignada por                  |                ISP / Cloud | Router / DHCP local                       |
| Rangos comunes                |       Cualquier IP pública | 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16 |
| Uso típico                    | Servidores, gateway de red | Dispositivos dentro de LAN                |
| ¿Se puede rastrear desde web? | Sí (aprox. ubicación, ISP) | No                                        |

### Consejos prácticos y buenas prácticas

- **No expongas servicios innecesarios** a Internet: cierra puertos en el router/firewall.
- Para acceso remoto seguro: usa **VPN**, jump hosts o bastion hosts en la DMZ, no abrir puertos RDP/SSH sin protección.
- **Usa autenticación fuerte** y actualiza servicios expuestos.
- Para servidores caseros sin IP estática: usar **DDNS** o usar un servicio cloud (proxy/reverse) que tenga IP pública y proteja el backend.
- Monitoriza logs para detectar accesos inusuales desde IPs públicas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**                         |
| ------------------ | ----------------------------------- |
| [🏠](../README.md) | [⏪](./3_7_Basics_of_Subnetting.md) |
