| **Inicio**         | **atrás 4**            | **Siguiente 6**     |
| ------------------ | ---------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./7_4_Netstat.md) | [⏩](./7_6_NMAP.md) |

---

## **Índice**

| Temario                                  |
| ---------------------------------------- |
| [167. Comando Route](#167-comando-route) |

# **Route**

## **167. Comando Route**

(En redes, “route” suele referirse a **la tabla de enrutamiento** y a los comandos para **ver, agregar o eliminar rutas**. En Linux moderno se usa más `ip route`, pero también existen `route` clásico, y en Windows `route`/`netsh`.)

### 1) ¿Qué es una ruta y la “tabla de enrutamiento”?

Una **ruta** dice “para llegar a esta red, envía los paquetes por tal puerta de enlace (gateway) usando tal interfaz”.
Tu equipo mantiene una **tabla de enrutamiento** con entradas del tipo:

- **Destino** (red/host, p.ej. `10.10.0.0/16` o `8.8.8.8/32`)
- **Máscara/CIDR** (qué parte identifica la red)
- **Gateway** (el siguiente salto, p.ej. `192.168.1.1`)
- **Interfaz** (p.ej. `eth0`, `Wi-Fi`)
- **Métrica** (prioridad: menor = preferida)

Regla de oro: **gana la coincidencia más específica** (longest prefix match). Si nada coincide, se usa la **ruta por defecto** (`0.0.0.0/0` en IPv4, `::/0` en IPv6).

### 2) Linux: `ip route` (recomendado) y `route` (clásico)

#### Ver rutas

```bash
ip route show
# Ejemplo de salida:
# default via 192.168.1.1 dev eth0 proto dhcp metric 100
# 10.10.0.0/16 via 192.168.1.254 dev eth0 metric 50
# 192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.10 metric 100
```

Equivalente clásico:

```bash
route -n
```

#### Probar decisión de enrutamiento

```bash
ip route get 8.8.8.8
# Muestra por dónde saldría ese destino (interfaz, gateway, src…)
```

#### Agregar una ruta de red

```bash
sudo ip route add 10.10.0.0/16 via 192.168.1.254 dev eth0 metric 50
```

Clásico:

```bash
sudo route add -net 10.10.0.0 netmask 255.255.0.0 gw 192.168.1.254 dev eth0
```

#### Agregar ruta a un host específico

```bash
sudo ip route add 203.0.113.7/32 via 192.168.1.254 dev eth0
```

#### Ruta por defecto

```bash
sudo ip route replace default via 192.168.1.1 dev eth0 metric 100
```

#### Eliminar ruta

```bash
sudo ip route del 10.10.0.0/16 via 192.168.1.254
```

#### Rutas “blackhole” (bloqueo por IP/red)

```bash
sudo ip route add blackhole 203.0.113.0/24
# Todo paquete a esa red se descarta localmente.
```

#### IPv6

```bash
ip -6 route show
sudo ip -6 route add 2001:db8:1::/64 via fe80::1 dev eth0
```

#### Persistencia en Linux

Las rutas añadidas con `ip` **no persisten** tras reiniciar. Para hacerlas persistentes:

- **NetworkManager (común en desktop/servers modernos)**

  ```bash
  nmcli connection show            # identifica tu conexión
  nmcli connection modify "<Nombre>" +ipv4.routes "10.10.0.0/16 192.168.1.254"
  nmcli connection up "<Nombre>"
  ```

- **Netplan (Ubuntu server moderno)** – ejemplo `sudoedit /etc/netplan/01-netcfg.yaml`:

  ```yaml
  network:
    version: 2
    ethernets:
      eth0:
        addresses: [192.168.1.10/24]
        routes:
          - to: 10.10.0.0/16
            via: 192.168.1.254
          - to: 0.0.0.0/0
            via: 192.168.1.1
  ```

  Luego: `sudo netplan apply`.

_(En distros antiguas: `/etc/network/interfaces` o archivos `route-<iface>` en RHEL/CentOS legacy.)_

### 3) Windows: `route` y `netsh`

#### Ver la tabla

```bat
route print
```

Muestra IPv4/IPv6, interfaces y métricas.

#### Agregar ruta (temporal)

```bat
route add 10.10.0.0 mask 255.255.0.0 192.168.1.254 metric 50
```

#### Agregar ruta **persistente** (sigue tras reinicio)

```bat
route -p add 10.10.0.0 mask 255.255.0.0 192.168.1.254 metric 50
```

#### Elegir interfaz explícita (si tienes varias)

Primero mira el **Índice de interfaz** en `route print`, luego:

```bat
route -p add 10.10.0.0 mask 255.255.0.0 192.168.1.254 IF 12
```

#### Quitar ruta

```bat
route delete 10.10.0.0
```

#### Alternativa con `netsh`

```bat
netsh interface ipv4 add route prefix=10.10.0.0/16 interface="Ethernet" nexthop=192.168.1.254 store=persistent
netsh interface ipv4 delete route prefix=10.10.0.0/16 interface="Ethernet" nexthop=192.168.1.254
```

### 4) macOS / BSD

#### Ver rutas

```bash
netstat -rn
route -n get default
```

#### Agregar ruta

```bash
sudo route -n add 10.10.0.0/16 192.168.1.254
```

_(En macOS, esto suele **no persistir**; para persistencia, configúralo vía tu gestor de red/VPN o con un script de `launchd`.)_

### 5) Ejemplos prácticos (casos típicos)

#### A) Enviar tráfico a una red interna por un router específico

**Situación:** tu LAN es `192.168.1.0/24`, y hay otra red `10.10.0.0/16` detrás del router `192.168.1.254`.

- **Linux**

  ```bash
  sudo ip route add 10.10.0.0/16 via 192.168.1.254 dev eth0
  ping -c 3 10.10.5.20
  traceroute 10.10.5.20
  ```

- **Windows**

  ```bat
  route -p add 10.10.0.0 mask 255.255.0.0 192.168.1.254
  ping 10.10.5.20
  tracert 10.10.5.20
  ```

#### B) Split tunneling con VPN

Solo la red corporativa `10.0.0.0/8` debe ir por la VPN (gateway virtual `10.8.0.1`), y lo demás por Internet normal.

- **Linux**

  ```bash
  sudo ip route add 10.0.0.0/8 via 10.8.0.1 dev tun0
  # Asegúrate de que tu default siga apuntando a tu router doméstico:
  ip route replace default via 192.168.1.1 dev eth0 metric 100
  ```

- **Windows**

  ```bat
  route -p add 10.0.0.0 mask 255.0.0.0 10.8.0.1
  ```

#### C) Dos gateways (failover simple por métrica)

- **Linux**

  ```bash
  sudo ip route replace default via 192.168.1.1 dev eth0 metric 100
  sudo ip route add default via 192.168.2.1 dev eth1 metric 200
  # Usará 192.168.1.1 mientras esté disponible (menor métrica).
  ```

- **(Avanzado) Balanceo ECMP**

  ```bash
  sudo ip route replace default scope global \
    nexthop via 192.168.1.1 dev eth0 weight 1 \
    nexthop via 192.168.2.1 dev eth1 weight 1
  ```

#### D) Bloquear una IP “ruidosa”

- **Linux**

  ```bash
  sudo ip route add blackhole 203.0.113.45/32
  ```

### 6) Cómo decide el sistema qué ruta usar (resumen)

1. **Coincidencia más específica** (ej.: `/32` > `/24` > `/0`).
2. **Métrica** (menor = mejor).
3. Otros criterios del SO (alcance, tipo de ruta, política).

### 7) Errores comunes y cómo evitarlos

- **Gateway fuera de tu subred local.** El gateway debe ser **alcanzable directamente** por la interfaz (p.ej., tu IP `192.168.1.10/24` → gateway debe ser `192.168.1.x`, no `192.168.2.1`).
- **Máscara/CIDR incorrecta.** `255.255.0.0` = `/16`, `255.255.255.0` = `/24`.
- **Rutas solapadas** con métricas mal puestas → tráfico “se va” por donde no esperas.
- **Persistencia no configurada** → tras reiniciar desaparecen.
- **Olvidar probar** con `ping`/`traceroute`/`ip route get`.

### 8) Bonus: Enrutamiento por políticas (policy routing) en Linux (avanzado)

Para elegir rutas según IP de origen, marca, etc.:

```bash
# Tabla nueva 100
echo "100 vpn" | sudo tee -a /etc/iproute2/rt_tables

# Rutas dentro de esa tabla
sudo ip route add default via 10.8.0.1 dev tun0 table vpn

# Regla: si el tráfico sale con IP origen 192.168.50.10, usa la tabla 'vpn'
sudo ip rule add from 192.168.50.10/32 table vpn
sudo ip rule show
```

#### Checklist de práctica rápida

- Ver rutas: `ip route show` / `route print`.
- ¿Por dónde saldría X?: `ip route get X` / `tracert X`.
- Agregar red: `ip route add <red> via <gw> dev <if>` / `route [-p] add`.
- Default: `ip route replace default via <gw>`.
- Quitar: `ip route del …` / `route delete …`.
- Persistencia: `nmcli`/Netplan (Linux), `-p` o `netsh` (Windows), script/gestor (macOS).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**            | **Siguiente 6**     |
| ------------------ | ---------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./7_4_Netstat.md) | [⏩](./7_6_NMAP.md) |
