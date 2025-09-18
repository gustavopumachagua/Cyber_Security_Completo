| **Inicio**         | **atrás 15**          | **Siguiente 17**            |
| ------------------ | --------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./12_15_NIPS.md) | [⏩](./12_17_Sandboxing.md) |

---

## **Índice**

| Temario                                                                            |
| ---------------------------------------------------------------------------------- |
| [387. ¿Qué es un firewall basado en host?](#387-qué-es-un-firewall-basado-en-host) |
| [388. Cortafuegos basados ​​en host](#388-cortafuegos-basados-en-host)             |

# **Host-based Firewall**

## **387. ¿Qué es un firewall basado en host?**

![Host-based Firewall](/img/12_Secure_vs_Unsecure_Protocols/Host_based_Firewall.webp "Host-based Firewall")

Un **firewall basado en host (HBF – Host-Based Firewall)** es un software o servicio de seguridad instalado directamente en un dispositivo individual (PC, laptop, servidor).

Su función es **monitorear y filtrar el tráfico entrante y saliente de la máquina en la que se ejecuta**, según reglas definidas por el usuario o el administrador.

📌 Ejemplos reales:

- **Windows Defender Firewall** (en Windows).
- **iptables / ufw** en Linux.
- Firewalls personales como ZoneAlarm o Comodo Firewall.

### ⚙️ ¿Cómo funciona un firewall basado en host?

- Se ejecuta como un **servicio/daemon** en el sistema operativo.
- **Filtra tráfico** en la pila TCP/IP del host, antes de que llegue a las aplicaciones o salga hacia la red.
- Usa **reglas configuradas** (p. ej. bloquear todo menos el puerto 443, permitir solo IP internas, etc.).
- Puede basarse en:

  - **Dirección IP** (bloquear/permitir rangos de IP).
  - **Puerto/Protocolo** (ej. permitir solo HTTPS).
  - **Aplicación** (ej. solo Chrome puede salir a Internet).
  - **Estado de conexión** (ej. permitir solo conexiones iniciadas por el host).

📌 Ejemplo en Windows:

Si un usuario instala un nuevo programa (ej. un juego online), el firewall puede mostrar un mensaje:

> "¿Desea permitir que esta aplicación acceda a la red pública/privada?"

Esto significa que el firewall crea una regla de salida/entrada **por aplicación**.

### ✅ Beneficios del firewall basado en host

1. **Protección granular**: reglas por aplicación o servicio.
2. **Protección incluso fuera de la red corporativa** (ej. laptops de empleados en Wi-Fi público).
3. **Bajo costo**: la mayoría viene integrado en el SO (Windows, Linux).
4. **Control por usuario/administrador**: fácil de configurar para apps específicas.
5. **Mitigación de ataques internos**: puede bloquear conexiones dentro de la misma LAN.

### ❌ Desventajas del firewall basado en host

1. **Difícil de administrar en masa**: en empresas con cientos de hosts, requiere herramientas de gestión centralizada.
2. **Consumo de recursos**: ocupa memoria y CPU del host.
3. **Dependencia del usuario**: si el usuario desactiva o configura mal el firewall, pierde efectividad.
4. **Cobertura limitada**: protege solo al host donde está instalado, no a toda la red.
5. **Posible incompatibilidad** con aplicaciones que requieren múltiples puertos dinámicos.

### 🛠️ Cómo configurar un firewall basado en host

#### En **Windows**

1. Ir a _Panel de Control → Firewall de Windows Defender_.
2. Crear reglas de entrada/salida:

   - Ejemplo: bloquear todo tráfico entrante excepto RDP (puerto 3389).

3. Definir perfiles: _Doméstico, Trabajo, Público_.

#### En **Linux (iptables/ufw)**

- Ver reglas activas:

  ```bash
  sudo ufw status
  ```

- Permitir SSH:

  ```bash
  sudo ufw allow 22/tcp
  ```

- Denegar todo y permitir solo HTTP/HTTPS:

  ```bash
  sudo ufw default deny incoming
  sudo ufw allow 80/tcp
  sudo ufw allow 443/tcp
  ```

### 🔄 Firewalls basados en host vs Firewalls de red

| Característica | Host-based Firewall                       | Network Firewall                      |
| -------------- | ----------------------------------------- | ------------------------------------- |
| Alcance        | Protege solo el host donde está instalado | Protege toda la red en el perímetro   |
| Ubicación      | Instalado en PC/servidor                  | Appliance/virtual en router, gateway  |
| Granularidad   | Reglas por aplicación/proceso             | Reglas por IP, puerto, protocolo      |
| Escalabilidad  | Difícil de administrar muchos hosts       | Centralizado, protege múltiples hosts |
| Ejemplo        | Windows Defender Firewall                 | Cisco ASA, Palo Alto, Fortinet        |

### 🔄 Firewalls basados en host vs Firewalls basados en aplicaciones

| Característica | Host-based Firewall                       | Application Firewall                                    |
| -------------- | ----------------------------------------- | ------------------------------------------------------- |
| Enfoque        | Tráfico de red en un host                 | Tráfico de una aplicación específica (ej. servidor web) |
| Profundidad    | IPs, puertos, protocolos, apps instaladas | Inspección profunda (DPI), analiza comandos HTTP/SQL    |
| Ejemplo        | iptables, Windows Firewall                | ModSecurity (WAF), F5 ASM                               |
| Uso común      | Usuarios finales, servidores individuales | Aplicaciones web críticas, APIs                         |

### ❓ Preguntas frecuentes sobre firewalls basados en host

**1. ¿Un firewall basado en host reemplaza a un firewall de red?**

No. Son complementarios. El de red protege a nivel global, el de host protege de amenazas internas o personalizadas en cada máquina.

**2. ¿El firewall de Windows es suficiente?**

Para la mayoría de usuarios domésticos y pequeñas empresas, sí. Pero en entornos corporativos conviene gestionarlo centralmente con GPO o soluciones EDR.

**3. ¿Un antivirus sustituye a un firewall?**

No. El antivirus detecta malware ya dentro del sistema. El firewall previene conexiones no autorizadas de entrada o salida.

**4. ¿Qué pasa si desactivo el firewall de Windows porque “me molesta”?**

Expones el equipo a ataques externos (ej. gusanos de red, RDP brute force, puertos abiertos sin control). Nunca se recomienda desactivarlo salvo para pruebas controladas.

**5. ¿Se puede tener firewall de host y de red al mismo tiempo?**

Sí, de hecho es lo ideal: defensa en capas (_defense in depth_).

📌 **En resumen**:

Un **firewall basado en host** es una defensa **personal y granular** instalada en un equipo específico. Es clave para **seguridad en movilidad** (ej. laptops en redes públicas), complementa al firewall de red y permite bloquear tráfico no deseado a nivel de aplicación.

---

[🔼](#índice)

---

## **388. Cortafuegos basados ​​en host**

### ¿Qué es un cortafuegos basado en host?

Un **cortafuegos basado en host** (host-based firewall, HBF) es un software que se ejecuta en un único equipo (PC, servidor, laptop) y **filtra el tráfico de red entrante y saliente de ese equipo** según reglas configuradas. Actúa en la pila de red del propio sistema operativo y puede aplicar políticas por IP/puerto/protocolo **y** por aplicación.

Ejemplos comunes:

- **Windows Defender Firewall** (Windows)
- **iptables / nftables / ufw / firewalld** (Linux)
- **pf** (packet filter) y el firewall de aplicaciones en macOS

### ¿Cómo funciona (a grandes rasgos)?

1. **Hook en la pila de red**: el firewall intercepta paquetes antes de que lleguen a la aplicación (o antes de salir).
2. **Evaluación de reglas**: cada paquete/flujo se compara contra reglas (permitir/denegar por IP, puerto, app, perfil).
3. **Estado de conexión**: muchos HBF son _stateful_ (recuerdan conexiones válidas y permiten paquetes de retorno).
4. **Acciones**: permitir, denegar, registrar (log), o aplicar limitación.
5. **Reglas por contexto**: perfil de red (dominio/privada/pública), usuario, proceso o aplicación.

### Tipos de reglas que puede aplicar un HBF

- **Por puerto/protocolo**: p.ej. permitir TCP/443, bloquear TCP/23.
- **Por IP origen/destino**: bloquear subredes no confiables.
- **Por aplicación/programa**: permitir que `chrome.exe` acceda a Internet pero bloquear `bad.exe`.
- **Por estado de conexión**: permitir solo paquetes ESTABLISHED,RELATED.
- **Por perfil de red**: políticas distintas para redes públicas o corporativas.

### Ejemplos prácticos

#### Windows (PowerShell / Windows Defender Firewall)

Habilitar perfil público, bloquear entradas por defecto y permitir HTTPS:

```powershell
# Poner acciones por defecto
Set-NetFirewallProfile -Profile Public -DefaultInboundAction Block -DefaultOutboundAction Allow

# Permitir una regla entrante para HTTPS (puerto 443)
New-NetFirewallRule -DisplayName "Permitir HTTPS" -Direction Inbound -Protocol TCP -LocalPort 443 -Action Allow -Profile Public

# Permitir una aplicación saliente concreta
New-NetFirewallRule -DisplayName "Permitir AppX Salida" -Direction Outbound -Program "C:\Program Files\AppX\appx.exe" -Action Allow -Profile Private,Domain
```

Bloquear una aplicación específica de salir:

```powershell
New-NetFirewallRule -DisplayName "Bloquear BadApp Out" -Direction Outbound -Program "C:\Bad\bad.exe" -Action Block -Profile Domain,Private,Public
```

Habilitar logging (pfirewall.log):

```powershell
Set-NetFirewallProfile -Profile Domain,Private,Public -LogAllowed True -LogFileName 'C:\Windows\System32\LogFiles\Firewall\pfirewall.log'
```

Comprobar conectividad:

```powershell
Test-NetConnection -ComputerName ejemplo.com -Port 443
```

### Linux — ufw (interfaz simple), iptables e nftables

#### ufw (Ubuntu / Debian)

```bash
# Activar UFW
sudo ufw enable

# Políticas por defecto
sudo ufw default deny incoming
sudo ufw default allow outgoing

# Permitir SSH y HTTPS
sudo ufw allow 22/tcp
sudo ufw allow 443/tcp

# Bloquear una IP
sudo ufw deny from 203.0.113.55
```

#### iptables (ejemplo clásico, antes de nftables)

```bash
# Permitir conexiones SSH nuevas, permitir paquetes establecidos/relacionados
sudo iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW -j ACCEPT
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT

# Política por defecto: descartar paquetes
sudo iptables -P INPUT DROP
```

#### nftables (recomendado moderno)

```bash
sudo nft add table inet filter
sudo nft 'add chain inet filter input { type filter hook input priority 0 ; policy drop; }'
sudo nft add rule inet filter input ct state established,related accept
sudo nft add rule inet filter input tcp dport 22 ct state new accept
```

### macOS (PF y Application Firewall)

- PF sirve para reglas de paquete (archivo `/etc/pf.conf`).
- Firewall de aplicaciones controla acceso de apps (Preferencias > Seguridad y privacidad > Firewall).
  Ejemplo (activar PF):

```bash
sudo pfctl -e
sudo pfctl -f /etc/pf.conf
```

### Casos de uso comunes / escenarios

- **Laptop en Wi-Fi pública**: activar perfil _público_ que bloquee conexiones entrantes, permitir sólo aplicaciones confiables en salida.
- **Servidor en DMZ**: permitir sólo puertos necesarios (80/443 para web), bloquear todo lo demás.
- **Kiosk / punto de venta**: reglas muy restrictivas, permitir sólo procesos necesarios.
- **BYOD**: reglas por usuario y aplicación, uso de MDM para imponer políticas.

### Ventajas (beneficios) de cortafuegos basados en host

- **Granularidad por aplicación** (control fino).
- **Protección en movilidad**: protege al dispositivo incluso fuera de la red corporativa.
- **Capas adicionales**: complementa firewalls de red (defense-in-depth).
- **Políticas específicas al host**: diferentes reglas por rol (servidores vs estaciones).
- **Fácil de desplegar en SO modernos** (muchos vienen integrados).

### Desventajas / limitaciones

- **Gestión en gran escala**: administrar reglas en cientos/ miles de hosts requiere consolas centralizadas (GPO, Intune, Ansible).
- **Consumo de recursos**: ligero pero existe overhead.
- **Dependencia del host**: si el host es comprometido, un atacante puede deshabilitar el firewall (usar tamper protection).
- **Cobertura limitada**: protege sólo el host local, no la segmentación de red general.

### Host firewall vs Network firewall vs Application firewall (resumen)

| Aspecto    |            Host-based firewall | Network firewall                   | Application (WAF)              |
| ---------- | -----------------------------: | ---------------------------------- | ------------------------------ |
| Alcance    |                      Un equipo | Segmento / perímetro               | Aplicación web                 |
| Control    |              App / puerto / IP | IP / puerto / protocolo            | Contenido HTTP, ataques web    |
| Gestión    | Local o centralizada (GPO/MDM) | Central en appliance               | Implementado frente a app      |
| Mejor para |      Protección endpoint móvil | Filtrado perimetral y segmentación | Mitigar XSS / SQLi / WAF rules |

### Mejores prácticas (recomendadas)

1. **Política por defecto**: _deny inbound_ por defecto; _allow outbound_ o _deny outbound_ según necesidad.
2. **Principio de mínimo privilegio**: abrir sólo los puertos/ apps necesarios.
3. **Aplicar perfiles de red** (dominio/privada/pública) y reglas distintas por perfil.
4. **Gestión centralizada**: GPO/Intune para Windows; Ansible/Salt/Chef para Linux, MDM para macOS.
5. **Tamper protection**: impedir que usuarios no autorizados desactiven el firewall.
6. **Registro y envío de logs a SIEM**: Windows Event Forwarding, syslog, WEF→SIEM.
7. **Tuning y pruebas**: desplegar en modo _auditoría_ antes de bloquear en producción.
8. **Integración**: con EDR/HIPS para prevenir que el malware desactive reglas.

### Troubleshooting / diagnóstico (qué hacer cuando algo no conecta)

- **Verificar reglas**:

  - Windows: `Get-NetFirewallRule` / `Get-NetFirewallProfile`
  - Linux: `sudo ufw status verbose` / `sudo iptables -S` / `sudo nft list ruleset`

- **Comprobar logs**:

  - Windows: visor de eventos → _Applications and Services Logs_ → _Microsoft\Windows\Windows Firewall with Advanced Security_
  - Linux: `/var/log/ufw.log` o syslog

- **Probar conectividad**:

  - `Test-NetConnection -ComputerName host -Port 443` (PowerShell)
  - `nc -vz host 22` o `curl -v`

- **Temporarily allow for test**: crear regla temporal permitiendo puerto y probar; revertir tras la prueba.
- **Si una app no conecta**, comprobar si firewall bloquea por path (aplicación firmada diferente) y añadir regla por SID o por certificado (Windows).

### Ejemplos de políticas de empresa (plantillas)

- **Workstation (empleados)**

  - Default inbound: **Deny**
  - Default outbound: **Allow** (monitorizar) o **Deny** (si empresa restrictiva)
  - Permitir: HTTP/HTTPS, DNS, VPN client, MDM agent
  - Bloquear: SMB entrante, RDP entrante

- **Servidor web (DMZ)**

  - Permitir inbound: TCP 80, 443 desde Internet (resto denegar)
  - Permitir management: SSH/RDP sólo desde jump hosts (IP whitelist)
  - Outbound: sólo destinos explícitos (actualizaciones, backups remotos)

- **Kiosk / POS**

  - Default inbound/outbound: **Deny** excepto puertos y servicios esenciales
  - No permitir instalación de apps por usuarios

### Seguridad adicional: evitar que el firewall sea desactivado

- **Windows**: activar _Tamper Protection_ (Defender), aplicar GPO que impida cambios locales.
- **Linux**: gestionar con herramientas de configuración y bloquear acceso root a usuarios no autorizados; auditar cambios.
- **EDR/HIPS**: supervisar intentos de manipulación del firewall y revertir reglas.

### Auditoría y cumplimiento

- Guardar logs de firewall (tamaño, rotación).
- Enviar logs a SIEM para correlación con eventos (login, EDR).
- Generar reportes periódicos: reglas activas, cambios, bloqueos críticos.

### Resumen práctico / checklist rápido

- [ ] Default inbound = Deny (salvo excepciones).
- [ ] Habilitar protección por aplicación.
- [ ] Gestionar reglas centralmente (GPO/Intune/Ansible).
- [ ] Habilitar logging y enviar a SIEM.
- [ ] Testear políticas en modo auditoría antes de bloquear.
- [ ] Habilitar tamper protection y supervisión EDR.
- [ ] Revisar reglas y whitelists regularmente.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 15**          | **Siguiente 17**            |
| ------------------ | --------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./12_15_NIPS.md) | [⏩](./12_17_Sandboxing.md) |
