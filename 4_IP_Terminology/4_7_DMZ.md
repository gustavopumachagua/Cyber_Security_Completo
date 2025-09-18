| **Inicio**         | **atrás 6**         | **Siguiente 8**    |
| ------------------ | ------------------- | ------------------ |
| [🏠](../README.md) | [⏪](./4_6_VLAN.md) | [⏩](./4_8_ARP.md) |

---

## **Índice**

| Temario                                            |
| -------------------------------------------------- |
| [87. ¿Qué es una red DMZ?](#87-qué-es-una-red-dmz) |
| [88. DMZ explicada](#88-dmz-explicada)             |

# **DMZ**

## **87. ¿Qué es una red DMZ?**

![red DMZ](/img/4_IP_Terminology/DMZ.png "red DMZ")

### 🔹 ¿Qué es una red DMZ?

Una **DMZ (Demilitarized Zone)** es una **subred perimetral** que se coloca entre la red interna de una organización (LAN corporativa) y redes externas no confiables (como Internet).

👉 Su objetivo: **aislar y proteger la red interna**, colocando en la DMZ los servidores y servicios que deben ser accesibles desde fuera (ej. web, correo, DNS, FTP), evitando exponer la LAN directamente.

**Analogía:**

- Piensa en tu casa: la **LAN** es tu sala y cocina (privado).
- La **calle (Internet)** es pública.
- La **reja/jardín frontal (DMZ)** es un área intermedia donde dejas el buzón, interfono o paquetería. Los visitantes pueden llegar ahí, pero no a tu sala directamente.

### 🔹 ¿Cómo funciona una red DMZ?

Una DMZ se implementa normalmente con **firewalls o routers** que controlan el tráfico entre tres zonas:

1. **Internet (untrusted zone)**
2. **DMZ (perimeter zone)**
3. **LAN interna (trusted zone)**

El firewall define **reglas distintas para cada zona**:

- Internet → DMZ: permitido solo lo necesario (ej. puerto 80/443 a servidor web).
- Internet → LAN: **prohibido**.
- DMZ → LAN: limitado (ej. servidor web en DMZ puede consultar base de datos interna si es estrictamente necesario).
- LAN → DMZ/Internet: permitido, pero filtrado.

**Ejemplo típico:**

- Un cliente externo quiere acceder a tu página web.
- Llega a la IP pública de tu firewall.
- El firewall redirige el tráfico (NAT) hacia el **servidor web en la DMZ**.
- Si ese servidor es comprometido, el atacante está “encerrado” en la DMZ y no tiene acceso directo a la LAN interna.

### 🔹 Beneficios de usar una DMZ

1. **Aislamiento de servicios públicos**: servidores accesibles desde Internet no exponen tu LAN.
2. **Defensa en profundidad**: agrega una capa extra de seguridad entre el exterior y la red interna.
3. **Mitigación de ataques**: un ataque exitoso a un servidor en la DMZ no compromete automáticamente toda la red corporativa.
4. **Control granular de tráfico**: el firewall dicta qué puede fluir entre Internet, DMZ y LAN.
5. **Visibilidad y monitoreo**: más fácil aplicar IDS/IPS, honeypots y auditorías en el perímetro.
6. **Cumplimiento normativo**: muchas regulaciones (PCI DSS, ISO 27001) exigen segmentar redes y aislar servicios críticos.

### 🔹 Diseño y arquitectura de DMZ

#### 1) **DMZ con un solo firewall (3 interfaces)**

- **IF1 = Internet**
- **IF2 = LAN interna**
- **IF3 = DMZ**
  Más simple, económico, pero si el firewall cae, toda la seguridad depende de él.

```
[Internet] ---[FW]---(DMZ: Servidores públicos)
                      |
                      (LAN interna segura)
```

#### 2) **DMZ con doble firewall (firewall perimetral + interno)**

- **FW1 (perimetral)**: separa Internet de DMZ.
- **FW2 (interno)**: separa DMZ de LAN interna.
  Más seguro, porque incluso si FW1 o un servidor en la DMZ es comprometido, aún hay protección adicional.

```
[Internet] -- [FW1] -- (DMZ: Web, Mail, DNS) -- [FW2] -- (LAN interna)
```

#### 3) **DMZ basada en VLANs (virtual)**

- Usando un **firewall o router con VLANs** se segmenta la red lógica.
- Más barato y flexible, pero depende de configuraciones correctas para no mezclar tráfico.

### 🔹 Importancia y usos de la DMZ

Las DMZ se usan en entornos donde se necesita exponer servicios al público sin comprometer la seguridad interna.

**Usos típicos:**

- **Servidor web** accesible desde Internet.
- **Servidor de correo (SMTP)** que recibe correos de Internet.
- **Servidor DNS público** que responde consultas externas.
- **VPN concentrator** o portal de acceso remoto.
- **Proxy/reverse proxy** para filtrar tráfico hacia aplicaciones internas.
- **Honeypots** para estudiar ataques en un área controlada.

### 🔹 Preguntas frecuentes sobre DMZ en ciberseguridad

**1. ¿Es lo mismo una DMZ que una VLAN separada?**

No. Una VLAN puede ser usada como DMZ (segmentación lógica), pero la DMZ implica además reglas de firewall estrictas entre zonas.

**2. ¿Es obligatorio tener dos firewalls para una DMZ?**

No. Puedes hacerlo con uno de 3 interfaces, pero dos firewalls agregan seguridad adicional (modelo de defensa en profundidad).

**3. ¿La DMZ me protege de todos los ataques?**

No. La DMZ **reduce riesgos** al aislar, pero los servidores allí deben estar endurecidos (hardening), parcheados y monitoreados.

**4. ¿Qué pasa si no uso DMZ y publico un servidor en mi LAN?**

Si ese servidor es vulnerado, el atacante ya estaría **dentro de tu red corporativa**. Es como dejar la puerta principal abierta.

**5. ¿Puedo poner bases de datos en la DMZ?**

No es lo recomendado. Normalmente se ponen en la LAN interna y se configuran accesos controlados desde servidores de aplicación ubicados en la DMZ.

### 🔹 Resumen final

- Una **DMZ** es una **zona intermedia** que permite exponer servicios sin poner en riesgo la LAN interna.
- Funciona mediante **segmentación de red + firewalls** que controlan estrictamente qué tráfico fluye.
- Existen varios diseños: un firewall (3 interfaces), dos firewalls, o VLANs.
- Beneficios: **seguridad, aislamiento, cumplimiento normativo y visibilidad**.
- Es clave en arquitecturas de **ciberseguridad perimetral moderna**.

---

[🔼](#índice)

---

## **88. DMZ explicada**

### 1) ¿Qué es una DMZ?

**DMZ** (Demilitarized Zone, zona desmilitarizada) es una **zona de red intermedia** situada entre la red interna de confianza (LAN) y redes no confiables (Internet).
Su objetivo principal: **exponer solo lo necesario** (servicios públicos como web, correo, DNS) y **aislar** esos servicios del resto de la red interna para limitar el impacto si son comprometidos.

Piensa: la DMZ es el recibidor de tu casa; los visitantes pueden dejar paquetes allí, pero no entrar al salón.

### 2) ¿Cómo funciona (concepto de tráfico)?

Un diseño típico tiene **tres zonas**:

- **Internet (untrusted)** ← tráfico externo
- **DMZ (perimeter)** ← servidores públicos / servicios expuestos
- **LAN interna (trusted)** ← bases de datos, sistemas críticos, estaciones de trabajo

Las reglas básicas:

- Internet → DMZ: solo permitir los puertos necesarios (ej. 80/443 al servidor web).
- Internet → LAN: **bloquear** o restringir fuertemente.
- DMZ → LAN: **muy limitado** (p. ej. servidor de aplicaciones en DMZ puede conectarse a puerto 1433 del servidor DB interno solo desde IPs específicas).
- LAN → DMZ: permitido para administración/monitorización (con controles).

### 3) Arquitecturas comunes (con diagramas ASCII)

#### A) DMZ simple (un firewall con 3 interfaces)

```
[Internet] --- [Firewall] --- (DMZ: 192.0.2.0/24)
                          \
                           (LAN: 10.0.0.0/24)
```

- Ventaja: económico, más simple.
- Riesgo: único punto de falla / compromiso del firewall afecta todo.

#### B) DMZ doble firewall (defensa en profundidad)

```
[Internet] -- [FW Perimetral] -- (DMZ) -- [FW Interno] -- (LAN)
```

- Más seguro: aun si FW perimetral/falla, FW interno protege LAN.
- Recomendado para entornos críticos.

#### C) DMZ por VLAN (virtual)

- Separa DMZ y LAN mediante **VLANs** en switches y controla tráfico con un firewall o ACLs.
- Flexible y económico; exige buena configuración de trunks y seguridad.

#### D) DMZ en la nube (ej. AWS/Azure)

- Concepto similar: subred pública (ELB/NAT), subred privada (aplicaciones) y reglas de seguridad (security groups / NACLs).
- Las implementaciones y elementos cambian (LBs, security groups, NAT gateways).

### 4) Ejemplo práctico ON-PREM (IPs, NAT y reglas básicas)

**Topología sencilla**

- Internet ↔ Firewall (IP pública: 198.51.100.10)
- DMZ: 192.168.100.0/24

  - web1.dmz = 192.168.100.10 (HTTP/HTTPS)

- LAN: 10.0.0.0/24

  - db1 = 10.0.0.10

**Objetivo:** publicar web1 hacia Internet sin exponer la LAN.

#### Reglas conceptuales (ejemplo):

- Permitir HTTP/HTTPS desde Internet → 192.168.100.10.
- Permitir solo conexiones desde web1 → db1 a puerto 3306 (si fuera MySQL).
- Bloquear todo acceso directo Internet → LAN.
- Permitir administración desde LAN → DMZ (SSH), pero no desde Internet.

#### Ejemplo con `iptables` (Linux firewall/NAT)

```bash
# 1) NAT para HTTP al servidor web en DMZ
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to-destination 192.168.100.10:80
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 443 -j DNAT --to-destination 192.168.100.10:443

# 2) Permitir forward hacia el servidor web (estados NEW,ESTABLISHED)
iptables -A FORWARD -i eth0 -o eth1 -p tcp -d 192.168.100.10 --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A FORWARD -i eth0 -o eth1 -p tcp -d 192.168.100.10 --dport 443 -m state --state NEW,ESTABLISHED -j ACCEPT

# 3) Permitir respuestas desde el servidor
iptables -A FORWARD -s 192.168.100.10 -o eth0 -m state --state ESTABLISHED -j ACCEPT

# 4) Bloquear todo tráfico no explícito from Internet -> LAN
iptables -A FORWARD -i eth0 -o eth2 -j DROP

# 5) NAT saliente (si es necesario)
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

_(eth0 = WAN, eth1 = DMZ, eth2 = LAN). Explicación: PREROUTING DNAT direcciona tráfico a la DMZ; FORWARD permite el tráfico; POSTROUTING hace SNAT/MASQUERADE para que los hosts internos salgan.)_

### 5) Ejemplo práctico en CLOUD (patrón AWS recomendado)

- **Public subnets**: Application Load Balancer (ALB) o bastion host.
- **Private subnets**: servidores web / app sin IP públicas.
- **DB subnets (private)**: base de datos, solo accesible desde app SG.
- **NAT Gateway** para que recursos privados salgan a Internet para actualizarse.

Seguridad:

- ALB SG: permite 80/443 desde 0.0.0.0/0.
- Web SG: permite tráfico solo desde ALB SG (no desde Internet directamente).
- DB SG: permite tráfico solo desde Web SG en puerto DB.

Ventaja: el balanceador/ALB es el “punto público”; las instancias están aisladas.

### 6) ¿Qué tipos de servicios se colocan en la DMZ?

- Servidores web (front-end).
- Servidores de correo (MTA/relays).
- DNS públicos (resolvers autoritativos).
- VPN concentrators o portales de acceso remoto (a menudo con controles fuertes).
- Reverse proxies, WAFs, y load balancers.
- Bastion/jump hosts (si están bien controlados) — aunque normalmente el bastion se coloca en una zona especial con acceso restringido.

**Nunca** poner bases de datos o sistemas críticos directamente accesibles desde Internet; esos van en LAN interna.

### 7) Hardening / buenas prácticas (resumen práctico)

1. **Principio de menor privilegio**: solo los puertos y hosts estrictamente necesarios.
2. **Segmentación**: separar management, DMZ y LAN en subredes/VLANs distintas.
3. **Registro y monitorización centralizada**: logs de acceso, IDS/IPS, SIEM.
4. **Parches y actualizaciones**: aplicar hotfixes y parches frecuentes a servidores en DMZ.
5. **Mínimos servicios**: desactivar daemons innecesarios, cerrar puertos no usados.
6. **Control de accesos administrativos**: gestión desde la LAN interna mediante bastion o VPN; no permitir administración directa desde Internet.
7. **Filtrado de salida (egress)**: controla a qué destinos pueden conectar las máquinas en DMZ (evita que un servidor comprometido contacte C2).
8. **WAF y reverse proxies**: para mitigar OWASP, inyección, etc.
9. **Backups y planes de respuesta**: tener playbooks de incident response para servidores DMZ.
10. **Pruebas periódicas**: escaneo de vulnerabilidades y pentests.

### 8) Ejemplo de política de firewall (alto nivel)

- **Zone: Internet → DMZ**: permitir TCP 80,443 → IPs de web servers; permitir TCP 25 → MTA (si es mail); denegar todo lo demás.
- **Zone: Internet → LAN**: deny.
- **Zone: DMZ → LAN**: permitir puertos específicos (DB port) desde IPs específicas; denegar el resto.
- **Zone: LAN → DMZ**: permitir admin (SSH/RDP) desde rangos de administración y con MFA.
- **Stateful**: permitir tráfico establecido / relacionado.

### 9) Riesgos / ataques comunes y mitigaciones

- **Servidor DMZ comprometido → lateral movement**
  Mitigación: egress filtering, firewall interno, segmentación, least privilege.
- **Vulnerabilidades en software público (web apps)**
  Mitigación: WAF, parcheo, code review, CSP e HSTS.
- **DDoS hacia servicios en DMZ**
  Mitigación: CDN + WAF + rate limiting + scrubbing services.
- **Configuración errónea de NAT/ACL** (exponer LAN por error)
  Mitigación: revisiones de config, auditoría, cambios controlados (change control).

### 10) Mini-caso práctico (pequeña empresa)

**Requisitos**

- Web pública (sitio corporativo)
- Portal VPN para empleados remotos
- Base de datos interna (no pública)
- Administración remota solo desde la oficina

**Diseño sugerido**

- DMZ subnet: 192.168.50.0/24

  - web1: 192.168.50.10
  - vpn: 192.168.50.20

- LAN interna: 10.10.0.0/24

  - db1: 10.10.0.10

- Firewall perimetral con reglas:

  - WAN → 192.168.50.10 tcp/80,443 (NAT al web)
  - WAN → 192.168.50.20 udp/500,4500,tcp/443 (VPN) – con rate limit
  - DMZ(web) → LAN(db): 192.168.50.10 → 10.10.0.10 tcp/3306 (solo desde IP del web)
  - LAN(admin-net) → DMZ: permitir SSH solo desde rangos de administración

**Operación**

- Parches automáticos para web/OS fuera de horario de producción.
- Backups del web, replicación DB interna.
- Monitorización: IDS en tráfico DMZ y alertas en SIEM.

### 11) DMZ en la práctica: ¿qué NO poner en la DMZ?

- Datos sensibles o bases de datos críticas.
- Servicios de administración sin autenticación fuerte.
- Sistemas que no controlas o que no están bien parchados.

La DMZ **acoge los frontales**; los backends siguen en la zona segura.

### 12) Checklist rápido antes de lanzar una DMZ

- [ ] Subred/VLAN dedicadas creadas.
- [ ] Reglas de firewall documentadas (allow/deny).
- [ ] NAT/Port-forward configurado y testeado.
- [ ] IDS/IPS y logging habilitado.
- [ ] Backups y parcheo automatizado.
- [ ] Monitoreo y alertas (SIEM).
- [ ] Plan de respuesta a incidentes.
- [ ] Pruebas de penetración y escaneo de vulnerabilidades ejecutadas.
- [ ] Acceso administrativo limitado a management VLAN o VPN + MFA.

### 13) Preguntas frecuentes (FAQ)

**¿Puedo administrar servidores DMZ desde Internet?**

No es recomendable. Usa un bastion host con MFA y sólo permitido desde direcciones autorizadas o mediante VPN.

**¿Necesito dos firewalls?**

No es obligatorio, pero para entornos críticos o cumplimiento es una buena práctica (defensa en profundidad).

**¿Puedo usar VLANs en lugar de hardware separado?**

Sí. VLANs + firewall lógico son comunes, pero cuida trunks, native VLANs y control de administración.

**¿La DMZ evita 100% las intrusiones?**

No. Reduce impacto y exposición, pero requiere hardening, parches y monitoreo constantes.

**¿Dónde colocar un WAF o CDN?**

Antes del servidor (entre Internet y servidor), idealmente en la capa pública (CDN + WAF delante del origen en la DMZ).

### 14) Conclusión corta

Una DMZ es una **práctica esencial** para exponer servicios públicos con seguridad: **segmenta**, **controla**, **monitoriza**. Usada correctamente reduce el riesgo de comprometer la red interna y permite aplicar controles y monitoreo más efectivos.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**         | **Siguiente 8**    |
| ------------------ | ------------------- | ------------------ |
| [🏠](../README.md) | [⏪](./4_6_VLAN.md) | [⏩](./4_8_ARP.md) |
