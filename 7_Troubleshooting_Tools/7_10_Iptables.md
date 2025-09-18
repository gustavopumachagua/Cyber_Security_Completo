| **Inicio**         | **atrás 9**             | **Siguiente 11**                |
| ------------------ | ----------------------- | ------------------------------- |
| [🏠](../README.md) | [⏪](./7_9_Nslookup.md) | [⏩](./7_11_Packet_Sniffers.md) |

---

## **Índice**

| Temario                                                                |
| ---------------------------------------------------------------------- |
| [178. Página de manual de iptables](#178-página-de-manual-de-iptables) |
| [179. Guía completa de iptables](#179-guía-completa-de-iptables)       |

# **IPtables**

## **178. Página de manual de iptables**

![IPtables](/img/7_Troubleshooting_Tools/IPtables.jpg "IPtables")

### ¿Qué es `iptables`?

`iptables` es la herramienta de usuario para administrar las reglas del **netfilter** en el kernel Linux. Con ella defines qué paquetes se aceptan, rechazan, registran o transforman (NAT). Se usa para crear firewalls, NAT/masquerading, port-forwarding, limitación de conexiones y más.

> **Aviso importante:** ejecutar reglas puede dejar tu máquina inaccesible (ej. si trabajas por SSH). Antes de aplicar reglas agresivas, añade una regla que permita tu sesión SSH o usa herramientas como `iptables-apply`/scripts con rollback automático.

### Estructura básica (lo que verás en la “man page”)

**SYNOPSIS (resumen de la sintaxis)**

```
iptables [-t tabla] comando cadena reglas ... [-j target]
```

- `-t tabla` — tabla a usar (`filter`, `nat`, `mangle`, `raw`, `security`).
- `comando` — acción sobre la cadena (`-A` append, `-I` insert, `-D` delete, `-L` list, `-F` flush, `-P` set policy, `-N` new chain, `-X` delete chain, ...).
- `cadena` — chain objetivo (`INPUT`, `OUTPUT`, `FORWARD` para `filter`; `PREROUTING`, `POSTROUTING`, `OUTPUT` para `nat`, etc.).
- `-j target` — acción final (ACCEPT, DROP, REJECT, LOG, DNAT, SNAT, MASQUERADE, RETURN, etc.).

**DESCRIPTION** — explica tablas, cadenas y objetos.
**OPTIONS** — todas las banderas (por ejemplo `-s`, `-d`, `-p`, `-i`, `-o`, `-m` para módulos).
**EXTENSIONS / MATCHES** — `-m conntrack`, `-m limit`, `-m recent`, `-m tcp`, etc.
**EXAMPLES** — reglas frecuentes.
**FILES** — ubicaciones de estado o utilidades (`/etc/iptables/` según distro).
**SEE ALSO** — `iptables-save`, `iptables-restore`, `ip6tables`, `nft` (nftables).

### Tablas y cadenas (resumen)

- **filter** (por defecto): INPUT, FORWARD, OUTPUT → control del forwarding y tráfico local.
- **nat**: PREROUTING, POSTROUTING, OUTPUT → NAT y DNAT/SNAT/masquerade (solo para la creación de nuevas conexiones).
- **mangle**: para modificar campos IP/TCP (TOS, TTL, marca).
- **raw**: para deshabilitar seguimiento de conexión (`NOTRACK`) o reglas anteriores al conntrack.
- **security**: reglas LSM (si está habilitado).

### Comandos habituales (con explicación)

- `iptables -L` — listar reglas (usa `-v -n --line-numbers` para más info).
- `iptables -S` — imprimir reglas en formato de comandos (útil para guardar).
- `iptables -A CHAIN ... -j TARGET` — añadir al final.
- `iptables -I CHAIN [pos] ... -j TARGET` — insertar en posición.
- `iptables -D CHAIN [rule-spec|num]` — borrar (por especificación o por número de línea).
- `iptables -F [CHAIN]` — vaciar cadena.
- `iptables -P CHAIN ACCEPT|DROP|REJECT` — política por defecto.
- `iptables -N NAME` — crear cadena personalizada.
- `iptables -X NAME` — borrar cadena vacía.
- `iptables-save > /etc/iptables/rules.v4` y `iptables-restore < /etc/iptables/rules.v4` — exportar/importar.

### Matches y módulos comunes

- `-p tcp/udp/icmp` — protocolo.
- `-s 1.2.3.0/24` — origen.
- `-d 10.0.0.5` — destino.
- `-i eth0` / `-o eth0` — interfaz entrada/salida.
- `-m conntrack --ctstate NEW,ESTABLISHED,RELATED` — estado de conexión (recomendado `conntrack` en lugar de `state`).
- `-m limit --limit 5/min --limit-burst 10` — limitar logs/paquetes.
- `-m recent --name PORTSCAN --set` / `--update --seconds 60 --hitcount 20` — detectar escaneos.
- `-m tcp --dport 22` — puerto destino TCP.
- `-j LOG --log-prefix "IPTables: " --log-level 4` — loguear antes de drop.
- `-j DNAT --to-destination 192.168.1.100:8080` — para PREROUTING (port forward).
- `-j MASQUERADE` — SNAT dinámico para conexiones salientes (uso en NAT con IP dinámica).

### Ejemplos prácticos (explicados paso a paso)

> **Consejo de seguridad:** si trabajas por SSH, antes de poner una política `DROP` asegúrate de permitir tu puerto SSH:
> `iptables -I INPUT 1 -p tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT`

#### 1) Firewall básico “stateful” (ejemplo completo, copiar/pegar)

```bash
# limpiar reglas actuales (opcional, con cuidado)
iptables -F
iptables -t nat -F
iptables -X

# política por defecto: denegar tráfico entrante, permitir saliente
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# loopback
iptables -A INPUT -i lo -j ACCEPT

# permitir conexiones ya establecidas/relacionadas
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# permitir SSH (puedes cambiar puerto)
iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT

# permitir HTTP/HTTPS
iptables -A INPUT -p tcp --dport 80  -m conntrack --ctstate NEW -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -m conntrack --ctstate NEW -j ACCEPT

# permitir ICMP ping (opcional, con limit)
iptables -A INPUT -p icmp -m limit --limit 1/second --limit-burst 5 -j ACCEPT

# registrar descartes (limitado)
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "IPTables-DROP: " --log-level 4
iptables -A INPUT -j DROP
```

Explicación: se permite loopback, tráfico ya establecido, SSH/HTTP/HTTPS nuevos; todo lo demás se registra y se descarta.

#### 2) Compartir Internet (NAT / Masquerade)

Interfaz hacia Internet = `eth0`, red LAN = `192.168.1.0/24`.

```bash
# Habilitar forwarding en kernel
sysctl -w net.ipv4.ip_forward=1

# SNAT dinámico (si IP pública dinámica)
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE

# Permitir forwarding (si usas firewall por defecto DROP)
iptables -A FORWARD -i eth0 -o eth1 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -i eth1 -o eth0 -j ACCEPT
```

Explicación: `MASQUERADE` reescribe la IP origen de paquetes salientes para que parezcan su origen en la IP pública; las reglas FORWARD permiten el reenvío entre interfaces.

#### 3) Port forwarding (DNAT) — exponer servicio interno

Redirección: todas las conexiones TCP que lleguen al puerto 80 de la IP pública pasan a `192.168.1.100:80`.

```bash
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to-destination 192.168.1.100:80
iptables -A FORWARD -p tcp -d 192.168.1.100 --dport 80 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
```

Explicación: DNAT cambia destino; FORWARD permite el tráfico hacia la IP interna.

#### 4) Bloquear una IP o rango

```bash
# bloquear una IP concreta
iptables -A INPUT -s 203.0.113.45 -j DROP

# bloquear red completa
iptables -A INPUT -s 198.51.100.0/24 -j DROP
```

O rechazar con mensaje (ICMP):

```bash
iptables -A INPUT -s 203.0.113.45 -j REJECT --reject-with icmp-port-unreachable
```

#### 5) Limitar intentos de conexión (anti-brute force)

Usando `connlimit` o `recent`:

```bash
# rechazar más de 5 conexiones simultáneas desde la misma IP en el puerto 22
iptables -A INPUT -p tcp --dport 22 -m connlimit --connlimit-above 5 -j REJECT

# o con 'recent' para 20 intentos en 60s
iptables -A INPUT -p tcp --dport 22 -m recent --set --name SSH
iptables -A INPUT -p tcp --dport 22 -m recent --update --seconds 60 --hitcount 20 --name SSH -j REJECT
```

#### 6) Detectar y mitigar portscans (ejemplo simple)

```bash
# marcar IPs que envían muchos paquetes SYN
iptables -N PORTSCAN
iptables -A PORTSCAN -p tcp --tcp-flags SYN,ACK,FIN,RST SYN -m recent --set --name PORTSCAN
iptables -A PORTSCAN -m recent --update --seconds 60 --hitcount 20 --name PORTSCAN -j DROP

# incorporar en INPUT
iptables -A INPUT -p tcp -j PORTSCAN
```

#### 7) Loguear paquetes (antes de dropear)

```bash
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "IPTables-INPUT-DROP: " --log-level 4
iptables -A INPUT -j DROP
```

Los logs van a `dmesg` o `/var/log/kern.log` o `/var/log/messages` según distro/config.

### Administración y persistencia

- Ver reglas: `iptables -L -v -n --line-numbers`
- Ver en formato restaurable: `iptables-save`
- Guardar reglas:

  ```bash
  iptables-save > /etc/iptables/rules.v4
  ```

  Restaurar:

  ```bash
  iptables-restore < /etc/iptables/rules.v4
  ```

- En Debian/Ubuntu: paquete `iptables-persistent` o `netfilter-persistent` para cargar reglas al arranque.
- En RedHat/CentOS: usar `service iptables save` (dependiendo de versión) o systemd + scripts.

### IPv6

- Para IPv6 usa `ip6tables` (mismas ideas, tablas y cadenas pero elementos IPv6: `-s` acepta direcciones IPv6, NAT distinto — no usar MASQUERADE para IPv6).

### Notas modernas: nftables y compatibilidad

- `iptables` histórico puede estar en modo _legacy_ o como front-end hacia **nftables** (`nf_tables`). Muchas distribuciones modernas usan `iptables` como wrapper sobre `nft`. Revisa `iptables --version` si necesitas saber qué backend tienes.
- Para configuraciones nuevas y de alto rendimiento, **nftables** es la alternativa recomendada, pero `iptables` sigue muy usado y compatible.

### Buenas prácticas y precauciones

- **Siempre** permitir SSH antes de poner política `DROP`. Ejemplo: `iptables -I INPUT 1 -p tcp --dport 22 -j ACCEPT`.
- Haz pruebas desde otra consola y deja un cron/at que restaure reglas en X minutos si te quedas bloqueado.
- Documenta y comenta reglas en tu inventario.
- Evita reglas redundantes que afecten rendimiento; agrupa con cadenas personalizadas.
- Usa `-m conntrack --ctstate` para firewall stateful eficiente.
- Ten en cuenta NAT y forward: NAT cambia solo _nuevas_ conexiones; reglas en NAT son consultadas en el momento de creación de la conexión.

### Comandos útiles resumen rápido

```bash
iptables -L -v -n --line-numbers        # listar reglas con contadores
iptables -S                             # salida como comandos iptables
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 22 -j ACCEPT
iptables -D INPUT 3                      # borra la regla número 3
iptables -F                               # vacía todas las cadenas
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
iptables-save > /etc/iptables/rules.v4
iptables-restore < /etc/iptables/rules.v4
```

---

[🔼](#índice)

---

## **179. Guía completa de iptables**

### 1. ¿Qué es `iptables` (resumen)

`iptables` es la interfaz de usuario para administrar las reglas del **netfilter** del kernel Linux. Con `iptables` controlas qué paquetes se aceptan, se rechazan o se transforman (NAT).
Conceptos clave: **tablas**, **cadenas (chains)**, **matches** y **targets** (ACCEPT, DROP, REJECT, LOG, DNAT, SNAT, MASQUERADE, …).

### 2. Flujo de paquetes y tablas/cadenas (visión práctica)

Cuando llega o sale un paquete, pasa por varias cadenas dependiendo del contexto:

- **PREROUTING (nat/mangle/raw)** — antes de decidir el destino (se usa en NAT para cambiar destino).
- **INPUT (filter)** — paquetes destinados a la máquina local.
- **FORWARD (filter)** — paquetes que se reenvían entre interfaces (router).
- **OUTPUT (filter)** — paquetes generados por la máquina local.
- **POSTROUTING (nat/mangle)** — después de decidir salida (se usa para SNAT/MASQUERADE).

Tablas más comunes:

- `filter` — por defecto (INPUT, FORWARD, OUTPUT).
- `nat` — NAT (PREROUTING, POSTROUTING, OUTPUT).
- `mangle` — alterar campos (TOS, TTL, MARK).
- `raw` — reglas previas al conntrack (ej. NOTRACK).

### 3. Estados de conexión (conntrack)

Usa el módulo `conntrack` para reglas stateful:

- `NEW` — nueva conexión.
- `ESTABLISHED` — conexión ya establecida.
- `RELATED` — conexión relacionada (ej. data channel FTP).
- `INVALID` — paquete inválido.

Ejemplo de match: `-m conntrack --ctstate ESTABLISHED,RELATED`

### 4. Comandos básicos y cómo ver reglas

- Listar reglas:

  ```bash
  iptables -L -v -n --line-numbers        # legible con contadores
  iptables -S                             # muestra como comandos iptables
  iptables -t nat -L -n -v                # ver tabla nat
  ```

- Guardar / restaurar:

  ```bash
  iptables-save > /etc/iptables/rules.v4
  iptables-restore < /etc/iptables/rules.v4
  ```

- Version / backend:

  ```bash
  iptables --version
  # En distros modernas puede decir iptables v1.x (nf_tables) ó legacy
  ```

### 5. Sintaxis de reglas (rápido)

- `-A CHAIN` — append (añadir al final)
- `-I CHAIN [pos]` — insert (insertar en posición)
- `-D CHAIN rule-spec|num` — delete (borrar)
- `-F [CHAIN]` — flush (vaciar)
- `-P CHAIN TARGET` — policy por defecto (ACCEPT/DROP/REJECT)
- `-N NAME` — crear cadena personalizada
- `-X NAME` — borrar cadena vacía

Parámetros comunes en regla:

- `-p tcp/udp/icmp` — protocolo
- `-s 1.2.3.0/24` — origen
- `-d 10.0.0.5` — destino
- `-i eth0` / `-o eth0` — interfaz entrada/salida
- `-m conntrack --ctstate NEW,ESTABLISHED` — estado

Target final:

- `-j ACCEPT`, `-j DROP`, `-j REJECT`, `-j LOG`, `-j DNAT --to-destination ...`, `-j SNAT --to-source ...`, `-j MASQUERADE`

### 6. Ejemplo: Firewall básico "stateful" seguro (plantilla)

> **IMPORTANTE**: si administras el host por SSH, ejecuta **ANTES** de cambiar políticas una regla que permita tu sesión SSH (o usa el método “safe apply” al final).

Script ejemplo (copiar/pegar y adaptar interfaces / puertos):

```bash
#!/bin/bash
# firewall-basico.sh — ejemplo seguro
set -e

# --- guardar reglas actuales (backup)
iptables-save > /tmp/iptables.backup.$(date +%s)

# --- política por defecto: negar entrada y forwarding, permitir salida
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT ACCEPT

# borrar reglas existentes (opcional si quieres partir de 0)
iptables -F
iptables -X

# permitir loopback
iptables -A INPUT -i lo -j ACCEPT

# permitir conexiones ya establecidas/relacionadas
iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# permitir SSH (ajusta puerto si no es 22)
iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT

# permitir HTTP / HTTPS
iptables -A INPUT -p tcp --dport 80 -m conntrack --ctstate NEW -j ACCEPT
iptables -A INPUT -p tcp --dport 443 -m conntrack --ctstate NEW -j ACCEPT

# permitir ping con limitación para evitar flood
iptables -A INPUT -p icmp -m limit --limit 1/second --limit-burst 5 -j ACCEPT

# registrar descartes con limitación (evitar log flood)
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "IPTables-DROP: " --log-level 4

# por defecto: todo lo demás cae por la política DROP
```

**Explicación rápida**: permitimos loopback, tráfico saliente y respuestas (ESTABLISHED), abrimos SSH y web, limitamos ICMP y logs; el resto se descarta.

### 7. Cómo aplicar reglas _de forma segura_ (evitar quedarte fuera)

Método A — `iptables-apply` (recomendado si está disponible):

```bash
sudo apt install iptables  # ya suele venir; iptables-apply es parte del paquete
sudo iptables-apply /ruta/a/tu_rules_file
```

`iptables-apply` aplica reglas y te pregunta si mantienes los cambios; si no confirmas, revierte.

Método B — backup + plan de rollback automático (si _no_ tienes iptables-apply):

```bash
iptables-save > /tmp/iptables.before

# aplicar nuevas reglas (por ejemplo con iptables-restore)
iptables-restore < /tmp/rules.new

# crear rollback en 2 minutos: si todo OK, borramos el job; si no, restauramos
(sleep 120 && iptables-restore < /tmp/iptables.before ) & disown
# Si confirmas manualmente que todo está OK, mata el proceso de rollback o borra /tmp/iptables.before
```

**Nota**: el `sleep`+restore es un remedio rápido; úsalo con cuidado y considera usar `at` o `systemd-run` para un rollback más robusto.

### 8. NAT (Masquerade, SNAT, DNAT) — ejemplos prácticos

#### Habilitar forwarding

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

#### Masquerade (uso común en conexión compartida con IP dinámica)

```bash
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```

#### SNAT (IP pública fija)

```bash
iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth0 -j SNAT --to-source 203.0.113.5
```

#### Port forwarding (PREROUTING DNAT)

```bash
# redirige 203.0.113.5:80 --> 192.168.1.100:80
iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j DNAT --to-destination 192.168.1.100:80
iptables -A FORWARD -p tcp -d 192.168.1.100 --dport 80 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
```

### 9. Limitar ataques y brute-force (connlimit / recent / limit)

#### Limitar conexiones simultáneas por IP (connlimit)

```bash
# permitir máximo 4 conexiones simultáneas al SSH por IP
iptables -A INPUT -p tcp --dport 22 -m connlimit --connlimit-above 4 --connlimit-mask 32 -j REJECT --reject-with tcp-reset
```

#### Anti-brute (recent)

```bash
# marcar intentos nuevos
iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -m recent --set --name SSH --rsource

# si la IP hace >10 intentos en 60s, bloquear
iptables -A INPUT -p tcp --dport 22 -m recent --update --seconds 60 --hitcount 10 --name SSH --rsource -j DROP
```

#### Limitar logs / ICMP (limit)

```bash
iptables -A INPUT -p icmp -m limit --limit 1/second --limit-burst 5 -j ACCEPT
```

### 10. Logueo (recomendación)

Siempre **limita** el logging para no inundar el syslog:

```bash
iptables -A INPUT -m limit --limit 5/min -j LOG --log-prefix "IPTables-INPUT-DROP: " --log-level 4
```

Los logs salen a `dmesg` o `/var/log/kern.log` o `/var/log/messages` según distro/rsyslog.

### 11. Detectar port scans (ejemplo simple)

```bash
iptables -N PORTSCAN
iptables -A PORTSCAN -p tcp --tcp-flags SYN,ACK,FIN,RST SYN -m recent --set --name PORTSCAN --rsource
iptables -A PORTSCAN -m recent --update --seconds 60 --hitcount 20 --name PORTSCAN --rsource -j DROP
iptables -A INPUT -p tcp -j PORTSCAN
```

Explicación: usa `recent` para seguir IPs con muchos SYNs en poco tiempo.

### 12. Inspección de conexiones (conntrack)

Instala `conntrack` (package `conntrack`) para ver estado de conexiones:

```bash
conntrack -L             # listar conexiones
conntrack -E             # eventos en vivo
```

### 13. Persistencia (qué hacer para que las reglas sobrevivan reinicio)

#### Debian / Ubuntu

```bash
apt install iptables-persistent
# guarda reglas actuales en /etc/iptables/rules.v4 y rules.v6
iptables-save > /etc/iptables/rules.v4
ip6tables-save > /etc/iptables/rules.v6
```

`iptables-persistent` carga estos archivos al arranque.

#### RHEL / CentOS (varía por versión)

- En sistemas antiguos con `iptables-services`:

```bash
service iptables save   # guarda en /etc/sysconfig/iptables
```

- En sistemas con `firewalld` activo, **no mezcles** `iptables` manual con `firewalld`. Usa `firewall-cmd` o desactiva `firewalld`.

#### Systemd unit (si quieres control custom)

Crea `/etc/systemd/system/iptables-restore.service` con:

```ini
[Unit]
Description=Load iptables rules
Before=network-pre.target
Wants=network-pre.target

[Service]
Type=oneshot
ExecStart=/sbin/iptables-restore /etc/iptables/rules.v4
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

Luego `systemctl enable --now iptables-restore.service`.

### 14. IPv6 — `ip6tables`

- Usa `ip6tables` para reglas IPv6.
- No es habitual usar NAT en IPv6 (se desaconseja).
- Guardar/restore con `ip6tables-save` / `ip6tables-restore`.

### 15. `iptables` vs `nftables` (transición)

En distribuciones modernas `iptables` puede ser una _compatibilidad_ sobre `nftables` (backend nf_tables). Revisa:

```bash
iptables --version
# o
update-alternatives --config iptables  # en Debian/Ubuntu para elegir legacy vs nft
```

Para convertir reglas a `nft`:

```bash
iptables-translate < regla_iptables
```

`nftables` es más moderno y tiene ventajas de rendimiento y expresividad; para nuevas infraestructuras considera migrar.

### 16. Problemas comunes y cómo depurarlos

- **No persistencia**: olvidaste guardar (`iptables-save`).
- **SSH bloqueado**: siempre añadir excepción antes de aplicar DROP o usar `iptables-apply`.
- **Reglas no tienen efecto**: orden equivocado (las reglas se evalúan en orden); usar `-I` para insertar al inicio.
- **Docker**: Docker manipula iptables para NAT/forward; cuando usas Docker, tenlo en cuenta.
- **Confusión con firewalld**: firewalld gestiona reglas propias, evita mezclar con iptables manual.
- **`*filter` no aplicado** en las interfaces esperadas: revisa `ip addr` e interfaces activas.

Comandos útiles de depuración:

```bash
iptables -L -v -n --line-numbers
iptables -t nat -L -v -n
iptables -S
dmesg | grep IPTables-DROP
lsmod | egrep 'conntrack|nf_conntrack'
sysctl net.ipv4.ip_forward
```

### 17. Ejemplos rápidos “copy-paste” (cheat sheet)

Lista y estado:

```bash
iptables -L -v -n --line-numbers
iptables -t nat -L -n -v
iptables-save > /tmp/backup.rules
```

Permitir puerto:

```bash
iptables -I INPUT 1 -p tcp --dport 8080 -m conntrack --ctstate NEW -j ACCEPT
```

Bloquear IP:

```bash
iptables -I INPUT 1 -s 203.0.113.45 -j DROP
```

Port forward:

```bash
iptables -t nat -A PREROUTING -p tcp -i eth0 --dport 2222 -j DNAT --to-destination 192.168.1.50:22
iptables -A FORWARD -p tcp -d 192.168.1.50 --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
```

Exportar/Importar:

```bash
iptables-save > /etc/iptables/rules.v4
iptables-restore < /etc/iptables/rules.v4
```

### 18. Buenas prácticas y recomendaciones finales

- **Principio de menor privilegio**: abre sólo los puertos necesarios.
- **Stateful**: usa `-m conntrack --ctstate` para fiabilidad y rendimiento.
- **Logging**: siempre con `-m limit` para evitar inundar logs.
- **Backup** siempre antes de cambios (`iptables-save`).
- **Probar en entornos staging** antes de producción.
- **Automatización**: mantén reglas gestionadas por IaC (ansible/puppet) o usa `nftables` si comienzas nuevo.
- **Monitoreo**: revisa contadores con `iptables -L -v` para detectar patrones.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 9**             | **Siguiente 11**                |
| ------------------ | ----------------------- | ------------------------------- |
| [🏠](../README.md) | [⏪](./7_9_Nslookup.md) | [⏩](./7_11_Packet_Sniffers.md) |
