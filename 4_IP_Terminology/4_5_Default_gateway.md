| **Inicio**         | **atrás 4**                | **Siguiente 6**     |
| ------------------ | -------------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_4_Subnet_mask.md) | [⏩](./4_6_VLAN.md) |

---

## **Índice**

| Temario                                                                                                    |
| ---------------------------------------------------------------------------------------------------------- |
| [83. ¿Qué es una puerta de enlace predeterminada?](#83-qué-es-una-puerta-de-enlace-predeterminada)         |
| [84. Enrutadores y puertas de enlace predeterminadas](#84-enrutadores-y-puertas-de-enlace-predeterminadas) |

# **Puerta de enlace predeterminada**

## **83. ¿Qué es una puerta de enlace predeterminada?**

![Puerta de enlace predeterminada](/img/4_IP_Terminology/default_gateway.png "Puerta de enlace predeterminada")

### ¿Qué es una red local?

Una **red local (LAN, Local Area Network)** es un conjunto de dispositivos (computadoras, celulares, impresoras, servidores, etc.) conectados entre sí dentro de un área geográfica pequeña, como una **casa, oficina o edificio**.

👉 Se caracteriza porque:

- Usa direcciones **IP privadas** (ej: `192.168.x.x`, `10.x.x.x`, `172.16.x.x`).
- Permite compartir recursos como archivos, impresoras o internet.
- Generalmente está gestionada por un **router o switch**.

📌 Ejemplo:

En tu casa tienes:

- PC → `192.168.1.10`
- Celular → `192.168.1.11`
- Impresora → `192.168.1.20`
  Todos ellos están en la misma **LAN (192.168.1.0/24)** y se comunican entre sí.

### ¿Qué es exactamente una puerta de enlace predeterminada?

La **puerta de enlace predeterminada (Default Gateway)** es el **dispositivo de red (normalmente el router)** que actúa como **intermediario entre tu red local y otras redes externas** (como internet).

👉 Si dos dispositivos están en la misma red local, se comunican directamente.

👉 Pero si un dispositivo quiere comunicarse con una red externa (fuera de su subred), necesita la puerta de enlace.

📌 Ejemplo:

- Tu PC (192.168.1.10) quiere acceder a `google.com` (142.250.184.14).
- Tu PC no sabe cómo llegar a esa red, así que envía el tráfico al **router (192.168.1.1)**, que tiene conexión a internet.
- El router reenvía la petición y devuelve la respuesta.

### ¿Qué es una dirección de puerta de enlace predeterminada?

Es la **dirección IP del router dentro de tu red local**.
Generalmente es la primera dirección de la subred:

- `192.168.0.1`
- `192.168.1.1`
- `10.0.0.1`

📌 Ejemplo práctico:

```
Red: 192.168.1.0/24
Router (Gateway): 192.168.1.1
PC de usuario: 192.168.1.25
```

Aquí, la dirección `192.168.1.1` es la **puerta de enlace predeterminada**.

### 🔹 Cómo encontrar su puerta de enlace predeterminada

#### ✅ En **Windows**

1. Abre el **Símbolo del sistema (CMD)**.
2. Escribe:

   ```
   ipconfig
   ```

3. Busca la línea **Puerta de enlace predeterminada**.

📌 Ejemplo de salida:

```
Adaptador de LAN inalámbrica Wi-Fi:
   Dirección IPv4. . . . . . . . . . . . : 192.168.1.25
   Máscara de subred . . . . . . . . . . : 255.255.255.0
   Puerta de enlace predeterminada . . . : 192.168.1.1
```

#### ✅ En **macOS**

1. Abre **Preferencias del sistema** → **Red**.
2. Selecciona tu red (WiFi o Ethernet).
3. Haz clic en **Avanzado** → pestaña **TCP/IP**.
4. Verás **Router** → esa es tu puerta de enlace predeterminada.

O bien en Terminal:

```
netstat -nr | grep default
```

#### ✅ En **Android**

1. Abre **Ajustes** → **WiFi**.
2. Mantén pulsada la red conectada → **Administrar red** → **Opciones avanzadas**.
3. Mira el campo **Puerta de enlace**.

#### ✅ En **iOS (iPhone/iPad)**

1. Abre **Ajustes** → **WiFi**.
2. Pulsa el icono **(i)** junto a tu red conectada.
3. En **Router** verás la puerta de enlace.

### Cómo solucionar problemas de conectividad de la puerta de enlace predeterminada

A veces aparece el error **“La puerta de enlace predeterminada no está disponible”**. Esto significa que tu PC no puede comunicarse con el router.

✅ Soluciones típicas:

1. Reiniciar el router y el dispositivo.
2. Verificar que tu IP está en el mismo rango que la puerta de enlace.

   - Ejemplo: si tu gateway es `192.168.1.1`, tu PC no puede tener `192.168.0.10`.

3. Renovar la IP en Windows:

   ```
   ipconfig /release
   ipconfig /renew
   ```

4. Actualizar controladores de red.
5. Revisar cables y configuración WiFi.

### Bloquear la puerta: cómo proteger su red

Dado que la puerta de enlace (router) es **la entrada y salida de tu red**, protegerla es esencial.

📌 Buenas prácticas de seguridad:

- Cambiar la contraseña por defecto del router.
- Desactivar administración remota (solo permitir acceso desde LAN).
- Usar protocolos de seguridad fuertes en WiFi (WPA3 o WPA2).
- Mantener el firmware actualizado.
- Deshabilitar puertos o servicios innecesarios (Telnet, UPnP).
- Usar firewall y reglas de acceso.

✅ **Resumen final**:

- La **red local (LAN)** conecta dispositivos dentro de un área pequeña.
- La **puerta de enlace predeterminada** es la IP del router que conecta tu LAN con el exterior.
- Puedes encontrarla en cualquier sistema operativo (Windows, macOS, Android, iOS).
- Si falla, afecta la conexión a internet.
- Proteger la puerta de enlace equivale a **proteger toda tu red**.

---

[🔼](#índice)

---

## **84. Enrutadores y puertas de enlace predeterminadas**

### ¿Qué es un enrutador (router)?

- **Dispositivo de red de Capa 3 (modelo OSI)** que **interconecta redes distintas** y decide por dónde enviar paquetes IP según su **tabla de enrutamiento**.
- Se fija **en la IP de destino** (no en la MAC) para elegir la **siguiente “salida” (next hop)**.

**Ejemplo simple**

```
Red doméstica (LAN) 192.168.1.0/24
   PC: 192.168.1.50/24
   Router (LAN): 192.168.1.1
   Router (WAN): IP pública del ISP
→ El router conecta tu LAN con Internet (otra red).
```

### ¿Qué es la puerta de enlace predeterminada?

- En cada **host** (PC, móvil, servidor) es la **IP del router** al que le entregará **todo tráfico destinado a redes que no conoce**.
- Se le llama “predeterminada” porque se usa **cuando no existe una ruta más específica** en el host.

**Regla mental**:

Si el destino **está en mi misma red** → lo envío **directo** (no uso gateway).
Si el destino **está en otra red** → lo envío a **mi puerta de enlace predeterminada**.

**Ejemplo IPv4**

- PC: `192.168.1.50/255.255.255.0`
- Gateway: `192.168.1.1`
- Destino: `8.8.8.8` (otra red).
  El PC manda el paquete al **192.168.1.1**, y el router ya lo saca a Internet.

**Ejemplo IPv6**

- PC: `2001:db8:1::50/64`
- Gateway: `fe80::1` (link-local del router en la LAN)
- Default route IPv6: `::/0`

### ¿Cómo decide un host si usa o no el gateway?

1. Calcula si la **IP destino** cae dentro de su **subred** (usando su máscara o prefijo).
2. Si **sí**, resuelve la MAC del destino con **ARP** (o **ND** en IPv6) y le envía directo.
3. Si **no**, busca una ruta. Si no hay ruta específica, usa la **0.0.0.0/0** (o `::/0`) que apunta a la **puerta de enlace**.

**Mini ruta en un PC típico**

```
Destino        Máscara        Puerta de enlace    Interfaz
0.0.0.0        0.0.0.0        192.168.1.1         192.168.1.50   ← default
192.168.1.0    255.255.255.0  Enlace directo      192.168.1.50
```

### Flujo paso a paso (paquete hacia Internet)

1. Tu PC quiere llegar a `142.250.72.14` (por ejemplo, Google).
2. Ve que **no está** en 192.168.1.0/24 → decide usar **192.168.1.1**.
3. Si no conoce su **MAC**, hace **ARP**: “¿Quién es 192.168.1.1?” → el router responde con su MAC.
4. El PC **encapsula** el paquete IP (destino=142.250.72.14) en una **trama Ethernet** (destino=MAC del router) y la envía.
5. El router lo **desencapsula**, consulta su **tabla de enrutamiento**, y lo reenvía hacia el ISP.
6. Usualmente hace **NAT** (traduce tu IP privada a una IP pública) para salir a Internet.
7. La respuesta regresa por el camino inverso y el router “recuerda” la traducción NAT para devolvértela.

### ¿En qué se diferencian “router” y “puerta de enlace”?

- **Router**: el equipo que **interconecta redes** (pueden coexistir muchos).
- **Puerta de enlace predeterminada**: **para un host**, _cuál IP de router_ usar **por defecto** cuando no sabe otra ruta.

Piensa: _“El router es el edificio; mi default gateway es la **puerta** por la que yo salgo de mi piso cuando quiero ir a otra ciudad.”_

### Ejemplos prácticos

#### 1) Casa con un único router

- Router Wi-Fi:

  - LAN: `192.168.0.1/24`
  - DHCP reparte: `192.168.0.100–192.168.0.200` + **gateway `192.168.0.1`**

- Tu laptop recibe por DHCP: `192.168.0.120/24`, gateway `192.168.0.1`, DNS del ISP.
- Navegar a `8.8.8.8` → sales por `192.168.0.1`. Fácil.

#### 2) Oficina con varias redes y enrutamiento interno

- VLAN 10 (Usuarios): `10.10.10.0/24`, gateway `10.10.10.1` (SVI del switch L3 o del router)
- VLAN 20 (Servidores): `10.10.20.0/24`, gateway `10.10.20.1`
- Router perimetral tiene **ruta por defecto** hacia el ISP.
- Los hosts de cada VLAN usan su **gateway local**; el router/switch L3 **inter-VLAN** enruta entre 10.10.10.0/24 y 10.10.20.0/24 y, si es Internet, lo manda al perímetro.

#### 3) Dos routers en casa (doble NAT)

- Router del ISP: `192.168.1.1`
- Tu propio router detrás: WAN `192.168.1.2`, LAN `10.0.0.1` (tu red privada).
- Tus equipos (10.0.0.x) usan **gateway 10.0.0.1**.
- Hacia Internet habrá **NAT doble** (10.0.0.x → 192.168.1.2 → IP pública). Funciona, pero puede complicar juegos/servicios entrantes.

### Rutas específicas vs ruta por defecto

- **Ruta específica**: “para 10.20.30.0/24, usa 10.10.10.254”.
- **Ruta por defecto**: “para **todo lo demás** (0.0.0.0/0), usa 192.168.1.1”.
- El sistema siempre prefiere la **más específica** (mayor prefijo).
  Ej.: si tienes `10.0.0.0/8` y también `10.10.0.0/16`, el `/16` gana para ese rango.

### ¿Cómo se configura/ve en distintos sistemas?

**Windows**

```bat
ipconfig            :: ver gateway bajo “Puerta de enlace predeterminada”
route print         :: ver tabla de rutas
```

**Linux**

```bash
ip route show
# Ejemplo de salida:
# default via 192.168.1.1 dev eth0
# 192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.50

# añadir o cambiar default (temporal)
sudo ip route replace default via 192.168.1.1 dev eth0
```

**macOS**

```bash
route -n get default
networksetup -getinfo "Wi-Fi"
```

**Routers domésticos**

Suelen anunciar el gateway por **DHCP** (campo “Router”/“Default Gateway”). En routers CLI (ej. Cisco):

```text
ip route 0.0.0.0 0.0.0.0 <next-hop>
# o con interfaz de salida
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/0
```

**IPv6 (host)**

```bash
ip -6 route
# default via fe80::1 dev eth0 proto ra  # por SLAAC/RA
```

### Errores comunes (y cómo diagnosticarlos)

- **Gateway fuera de la subred del host**

  PC: `192.168.10.50/24` con gateway `192.168.20.1` → **no funciona**.
  El gateway **debe estar en la misma red** del host (p. ej., `192.168.10.1`).

- **Máscara equivocada**

  Dos PCs creen estar en redes distintas y se obligan a usar el router innecesariamente (o al revés).

- **IP duplicada**

  Si alguien usa `192.168.1.1` por error, tumba al router. Revisa el DHCP y reservas.

- **DNS ≠ Gateway**

  Puedes **hacer ping a 8.8.8.8** pero **no resolver nombres** → problema de **DNS**, no del gateway.

- **Métrica/prioridad de rutas**

  Con varias interfaces (Wi-Fi + Ethernet + VPN), la **métrica** decide cuál default usar.

**Comandos útiles**

```bash
ping <gateway>          # ¿llego a mi puerta de enlace?
traceroute 8.8.8.8      # camino hacia fuera (Windows: tracert)
arp -a                  # ver caché ARP (Linux: ip neigh)
ipconfig /all           # ver DHCP, DNS, gateway (Windows)
```

### NAT y la puerta de enlace

- En redes privadas (192.168.x.x, 10.x.x.x, 172.16–31.x.x), el **router-gateway** suele hacer **NAT** para salir a Internet.
- **Port forwarding**: regla en el router para **permitir conexiones entrantes** hacia un host interno (ej., abrir `TCP 3389` a `192.168.1.50`).

### Resumen rápido

- **Router**: equipo que **enruta entre redes** con una **tabla de rutas**.
- **Puerta de enlace predeterminada**: **la IP del router que usa un host** cuando el destino **no está en su subred**.
- **Clave**: el gateway debe estar **en la misma red** del host, y la **ruta por defecto** es `0.0.0.0/0` (o `::/0`).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**                | **Siguiente 6**     |
| ------------------ | -------------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_4_Subnet_mask.md) | [⏩](./4_6_VLAN.md) |
