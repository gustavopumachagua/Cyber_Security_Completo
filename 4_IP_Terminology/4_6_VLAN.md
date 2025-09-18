| **Inicio**         | **atrás 5**                    | **Siguiente 7**    |
| ------------------ | ------------------------------ | ------------------ |
| [🏠](../README.md) | [⏪](./4_5_Default_gateway.md) | [⏩](./4_7_DMZ.md) |

---

## **Índice**

| Temario                                      |
| -------------------------------------------- |
| [85. ¿Qué es una VLAN?](#85-qué-es-una-vlan) |
| [86. VLAN explicada](#86-vlan-explicada)     |

# **VLAN**

## **85. ¿Qué es una VLAN?**

![VLAN](/img/4_IP_Terminology/VLAN.gif "VLAN")

Una **VLAN (Virtual Local Area Network)** es una **red lógica de Capa 2** que agrupa puertos/hosts como si estuvieran en la misma red física, aunque físicamente estén en switches diferentes. Técnicamente, una VLAN define un **dominio de difusión** virtual separado dentro de un conmutador o entre conmutadores conectados por trunks. Esto permite segregar tráfico sin cambiar el cableado físico.

### Tipos de VLAN (principales)

- **VLAN por defecto (Default VLAN)**: normalmente VLAN 1 en muchos switches; es la VLAN por defecto para puertos no asignados.
- **VLAN de datos (Data VLAN)**: usada para el tráfico normal de usuarios (ej. VLAN 10 = usuarios).
- **VLAN de voz (Voice VLAN)**: optimizada para teléfonos IP; los switches pueden etiquetar/priorizar tráfico de voz.
- **VLAN de administración (Management VLAN)**: dedicada al acceso administrativo de equipos (SSH/HTTPS/SNMP).
- **VLAN nativa (Native VLAN)**: en trunks 802.1Q, la VLAN cuyo tráfico se envía sin tag (por defecto VLAN 1 en muchos fabricantes).
- **VLANs privadas (Private VLANs / PVLANs)**: permiten aislar puertos dentro de la misma VLAN primaria (útil para hoteles/ISPs).
- **Clasificación por método**: _estáticas/port-based_ (asignas puertos manualmente) y _dinámicas_ (basadas en MAC, autenticación 802.1X, política, etc.).

(El estándar que define el etiquetado/tags en tramas Ethernet para soportar múltiples VLANs es **IEEE 802.1Q** — esencial para trunks y subinterfaces).

### Cómo administrar y configurar VLAN (pasos y ejemplos)

#### 1) Planificación (antes de tocar equipos)

- Asigna **IDs** y **nombres** (ej.: VLAN 10 — USUARIOS, VLAN 20 — SERVIDORES, VLAN 99 — MGMT).
- Define rangos IP/subred por VLAN (ej. 10.10.10.0/24, 10.10.20.0/24).
- Decide: ¿ruteo inter-VLAN con L3 switch (SVI) o router-on-a-stick?
- Política de seguridad: VLAN de management separada, no usar VLAN 1 para administración, limitar VLANs permitidas en trunks.

#### 2) Comandos básicos (ejemplos Cisco IOS)

Crear VLAN y poner puerto en modo access:

```text
S1(config)# vlan 10
S1(config-vlan)# name USUARIOS
S1(config)# interface GigabitEthernet0/5
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10
```

Configurar trunk (802.1Q) entre switches:

```text
S1(config)# interface GigabitEthernet0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk allowed vlan 10,20,30
S1(config-if)# switchport trunk native vlan 99
```

Inter-VLAN con SVI (L3 switch):

```text
S1(config)# interface vlan 10
S1(config-if)# ip address 10.10.10.1 255.255.255.0
S1(config-if)# no shutdown
```

Router-on-a-stick (subinterfaces en el router):

```text
R1(config)# interface GigabitEthernet0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 10.10.10.254 255.255.255.0
```

(Estos pasos y ejemplos son la práctica habitual en guías de Cisco/CCNA).

#### 3) Ejemplo rápido en Linux (cliente/host o router Linux)

Crear interfaz VLAN 10 sobre `eth0`:

```bash
sudo ip link add link eth0 name eth0.10 type vlan id 10
sudo ip addr add 10.10.10.100/24 dev eth0.10
sudo ip link set up dev eth0.10
```

#### 4) Comandos de verificación / diagnóstico

- **Cisco**: `show vlan brief`, `show interfaces trunk`, `show mac address-table`, `show ip route`, `show running-config`
- **Linux**: `ip -d link show`, `bridge vlan show`, `ip addr`, `ip route`
- Comprueba: ¿trunk permitido?, ¿VLAN existe en ambos extremos?, ¿native VLAN coincide?

### Ventajas de usar VLAN

1. **Segmentación lógica**: separas grupos (finanzas, RRHH, invitados) sin cambiar cableado.
2. **Reducción de broadcast**: cada VLAN es su propio dominio de difusión → menos tráfico innecesario.
3. **Mejor seguridad**: puedes aplicar ACLs y políticas por VLAN, aislar usuarios/guest.
4. **Flexibilidad y escalabilidad**: mover un usuario de VLAN es una configuración en el switch, no re-cableado.
5. **QoS y servicios dedicados**: voice VLAN, priorización y políticas por clase.
6. **Optimización de recursos**: más eficiente que tener redes físicas separadas.
   (Estas ventajas son las motivaciones principales para desplegar VLANs en entornos empresariales).

### Diferencias entre LAN y VLAN (resumen comparativo)

| Característica                 | LAN (Local Area Network) física                    | VLAN (Virtual LAN) lógica                              |
| ------------------------------ | -------------------------------------------------- | ------------------------------------------------------ |
| Naturaleza                     | Física: segmento de red conectado por cable/switch | Lógica: partición dentro de un (o varios) switch(es)   |
| Dominio de difusión            | Determinado por conexiones físicas                 | Definido por la pertenencia a la VLAN                  |
| Requerimiento para comunicarse | Si están en la misma LAN no requiere ruteo         | Diferentes VLANs requieren ruteo (L3) para comunicarse |
| Escalabilidad                  | Escalar = más hardware/cables                      | Escalar = crear más VLANs y trunks (menos cables)      |
| Uso típico                     | Pequeñas redes físicas, o red sin segmentación     | Segmentación por departamentos, seguridad y control    |

### Buenas prácticas rápidas y errores comunes

- **No uses VLAN 1** para administración; crea una VLAN de gestión separada.
- **Limita VLANs permitidas** en los trunks (`switchport trunk allowed vlan ...`).
- **Asegura la native VLAN** (mismatch puede causar problemas y vulnerabilidades).
- **Documenta** nombres/IDs/IPs y map de puertos.
- **Evita VTP sin control** (puede propagar cambios peligrosos); si lo usas, protege con contraseñas y modos adecuados.
- **Monitorea MAC tables y STP**; corrupción de STP/VLAN puede causar bucles.
- Diagnóstico: si un host “no ve” su gateway, revisa que su puerto sea access en la VLAN correcta y que la VLAN exista en el switch uplink.

### Ejemplo práctico de diseño (pequeña oficina)

- VLAN 10 — USUARIOS — 10.10.10.0/24 — SVI 10.10.10.1
- VLAN 20 — SERVIDORES — 10.10.20.0/24 — SVI 10.10.20.1
- VLAN 30 — GUEST — 192.168.30.0/24 — SVI 192.168.30.1 (con ACL a Internet)
- VLAN 99 — MGMT — 10.10.99.0/24 — acceso SSH/HTTPS a switches (solo desde IPs admin)

Puertos de acceso para PCs se asignan a VLAN 10, uplinks entre switches en modo trunk (802.1Q) permitiendo VLANs 10,20,30,99; el ruteo entre VLANs lo realiza un L3 switch (SVI) o router-on-a-stick si no hay L3 switch.

---

[🔼](#índice)

---

## **86. VLAN explicada**

### ¿Qué es una VLAN?

Una **VLAN (Virtual Local Area Network)** es una **LAN lógica de Capa 2** que agrupa puertos o dispositivos en un mismo dominio de difusión aunque estén en distinto hardware físico.
En otras palabras: **separa el tráfico** en grupos lógicos sin tocar el cableado físico. Cada VLAN forma su propio **dominio de broadcast**.

Analogía rápida: imagina un edificio de oficinas donde, aunque todos los empleados comparten el mismo edificio (misma infraestructura física), cada departamento tiene su propia sala a la que solo puede entrar su gente. Eso es una VLAN.

### Conceptos clave (cómo funciona)

- **VLAN ID**: identificador numérico (0–4095, normalmente se usan 1–4094).
- **Dominio de difusión**: cada VLAN es su propio dominio; los broadcasts de una VLAN no cruzan a otra sin ruteo.
- **Puerto access**: puerto del switch asignado a UNA sola VLAN (tramas entran/salen **sin tag**).
- **Puerto trunk**: lleva tráfico de _varias_ VLANs entre switches o hacia routers; usa **802.1Q** para etiquetar (tag) tramas.
- **802.1Q (tagging)**: estándar que inserta un tag de 4 bytes en la trama Ethernet para indicar la VLAN.

  - Estructura simplificada de la etiqueta 802.1Q: `TPID (0x8100)` + `TCI (2 bytes)`
  - TCI = `PCP (3 bits)` + `DEI (1 bit)` + `VLAN ID (12 bits)`.
  - Ejemplo: VLAN 10 → tag bytes: `81 00 00 0A` (0x000A = VLAN ID 10).

- **Native VLAN**: VLAN cuyo tráfico en el trunk se envía _sin tag_ (por defecto VLAN 1 en muchos equipos).
- **SVI (Switch Virtual Interface)**: interfaz lógica en un switch L3 que actúa como gateway de una VLAN (ej. `interface vlan 10` con IP).

### Tipos / categorías comunes de VLAN

- **Data VLAN** (usuarios): tráfico normal (ej. VLAN 10).
- **Voice VLAN**: para teléfonos IP; prioriza QoS y separación de voz.
- **Management VLAN**: destinada al acceso a la administración (SSH, SNMP) de switches/routers.
- **Native VLAN**: sin tag en trunks (ajústala con cuidado).
- **Default VLAN**: VLAN por defecto (VLAN 1 normalmente). Evitar usarla para management.
- **Private VLANs (PVLAN)**: son VLANS dentro de una primaria que permiten aislar puertos (isolate, community, promiscuous) — útiles en proveedores/hoteles.
- **VLAN dinámica**: asignación mediante 802.1X o servidores RADIUS (usuario autenticado → asignación de VLAN).

### ¿Qué pasa con un paquete? — flujo simplificado

1. Host A (puerto access en VLAN 10) quiere hablar con Host B (VLAN 10). Genera una trama **sin tag**.
2. El switch, sabiendo que el puerto es access VLAN 10, **añade mentalmente** la VLAN 10 como atributo. Si la trama sale por un trunk, el switch **etiqueta (tag)** la trama con 802.1Q (VLAN 10).
3. Si el destino está en otra VLAN, la trama llega al router o SVI (inter-VLAN routing) que la enruta a la VLAN destino y devuelve la trama desencapsulada al switch, que la entrega sin tag en el puerto access destino.

Diagrama ASCII (simple):

```
PC-A (VLAN10) ---[Access port VLAN10]--- SwitchA ---[Trunk 802.1Q VLAN10,20]--- SwitchB
                                                       |
                                                   Router (subinterfaces)
```

### Ejemplos de configuración (Cisco IOS y Linux)

#### 1) Crear VLAN y poner puerto en access (Cisco)

```text
S1(config)# vlan 10
S1(config-vlan)# name USUARIOS

S1(config)# interface GigabitEthernet0/5
S1(config-if)# switchport mode access
S1(config-if)# switchport access vlan 10
```

#### 2) Configurar trunk 802.1Q entre switches (Cisco)

```text
S1(config)# interface GigabitEthernet0/1
S1(config-if)# switchport trunk encapsulation dot1q   ! (en algunos switches)
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk allowed vlan 10,20,30
S1(config-if)# switchport trunk native vlan 99          ! (si decides usar VLAN99 como native)
```

**Nota:** para seguridad conviene `no switchport trunk native vlan 1` y usar una VLAN no usada como native o forzar tagging del native con `vlan dot1q tag native` (Cisco).

#### 3) Inter-VLAN — SVI en switch L3 (Cisco)

```text
S1(config)# interface vlan 10
S1(config-if)# ip address 10.10.10.1 255.255.255.0
S1(config-if)# no shutdown

S1(config)# interface vlan 20
S1(config-if)# ip address 10.10.20.1 255.255.255.0
```

Cada host de VLAN10 tendría gateway `10.10.10.1`.

#### 4) Router-on-a-stick (router con subinterfaces)

```text
R1(config)# interface GigabitEthernet0/0.10
R1(config-subif)# encapsulation dot1Q 10
R1(config-subif)# ip address 10.10.10.254 255.255.255.0

R1(config)# interface GigabitEthernet0/0.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip address 10.10.20.254 255.255.255.0
```

El switch uplink a `R1` debe estar en modo trunk permitiendo VLANs 10 y 20.

#### 5) Crear interfaz VLAN en Linux (host o router)

```bash
sudo ip link add link eth0 name eth0.10 type vlan id 10
sudo ip addr add 10.10.10.100/24 dev eth0.10
sudo ip link set up dev eth0.10
```

#### 6) DHCP en router (Cisco) por VLAN

```text
ip dhcp pool VLAN10
 network 10.10.10.0 255.255.255.0
 default-router 10.10.10.1
 dns-server 8.8.8.8
```

### Ejemplo práctico completo (pequeña oficina)

Diseño:

- VLAN 10 — USUARIOS — 10.10.10.0/24 — Gateway SVI 10.10.10.1
- VLAN 20 — SERVIDORES — 10.10.20.0/24 — Gateway SVI 10.10.20.1
- VLAN 30 — GUEST — 192.168.30.0/24 — Gateway SVI 192.168.30.1 (con ACL a Internet)
- VLAN 99 — MGMT — 10.10.99.0/24

Switch S1 (core, L3) tendría SVIs y sería el default gateway para cada VLAN. Uplinks entre switches en trunk permitiendo 10,20,30,99. APs en modo trunk para llevar SSIDs mapeados a VLAN (corporate → VLAN10, guest → VLAN30).

### Troubleshooting (problemas comunes y comandos)

**Problema:** Host no alcanza su gateway.

- Revisar puerto: ¿está en `access VLAN X`?
- ¿La VLAN existe en el switch uplink y en el otro extremo del trunk?
- ¿El trunk permite la VLAN? (allowed vlan)
- ¿Native VLAN mismatch? (puede causar pérdida de tráfico)

**Comandos útiles (Cisco):**

- `show vlan brief` — lista VLANs y puertos.
- `show interfaces trunk` — qué trunks están up y qué VLANs pasan.
- `show mac address-table` — ver qué MAC está en qué puerto/VLAN.
- `show ip route` — rutas en el L3 switch/router.
- `show running-config` — revisar configuración.
- `ping` / `traceroute` / `arp` desde hosts.

**Linux:**

- `ip -d link show` / `bridge vlan show` / `ip addr` / `ip route`.

### Seguridad y buenas prácticas

- **No uses VLAN 1** para administración. Crear VLAN de gestión separada (ej. VLAN 99).
- **Limita VLANs en trunks:** `switchport trunk allowed vlan ...`.
- **Desactiva DTP** en puertos de usuario: `switchport nonegotiate` o `switchport mode access`.
- **Asigna una VLAN no usada como native** o fuerza tag al native (`vlan dot1q tag native`) para mitigar ataques de VLAN hopping.
- **Cierra puertos no usados** (`shutdown`, asignar a VLAN de cuarentena).
- **Port security**: limitar número de MACs por puerto.
- **802.1X + RADIUS**: asignación dinámica de VLANs y control de acceso.
- **ACLs y firewalling** entre VLANs para limitar lateral movement.
- **Monitorea STP y MAC tables** para detectar loops o spoofing.
- **Si usas VTP**, ten cuidado: puede propagar cambios de VLAN accidentalmente — usa `transparent` o contraseña y control.

### Vulnerabilidades (qué vigilar)

- **VLAN Hopping (switch spoofing / double-tagging)**: un atacante configura un puerto para ser trunk y así inyecta tráfico a otras VLANs. Mitigación: desactivar negociación en puertos no-trunk, no usar native VLAN 1, usar dot1q tag native.
- **Trunk misconfiguration**: native VLAN distinto en extremos → pérdida de tráfico o exposición.
- **Uso incorrecto de VLAN 1**: muchos equipos usan VLAN1 para management; mejor aislarla.

### Diferencia breve entre LAN y VLAN

- **LAN (física)**: segmento real conectado por cable/switch.
- **VLAN (lógica)**: partición virtual dentro de la infraestructura que crea múltiples “LANs” lógicas sobre el mismo hardware físico.
  → Una VLAN **implementa** varias LANs lógicas en la misma red física.

### Resumen práctico (puntos clave)

- VLAN = segmentación lógica de Capa 2 que reduce broadcast, mejora seguridad y flexibilidad.
- Trunks (802.1Q) permiten transportar varias VLANs entre switches/routers.
- Para comunicar VLANs hace falta ruteo (SVI o router).
- Planifica IDs y subredes, usa VLAN de gestión, limita trunks y asegura puertos de usuario.
- Verifica siempre `show vlan`, `show interfaces trunk` y `show mac address-table` cuando algo falla.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**                    | **Siguiente 7**    |
| ------------------ | ------------------------------ | ------------------ |
| [🏠](../README.md) | [⏪](./4_5_Default_gateway.md) | [⏩](./4_7_DMZ.md) |
