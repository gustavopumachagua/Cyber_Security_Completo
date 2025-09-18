| **Inicio**         | **atrás 33**               | **Siguiente 35**               |
| ------------------ | -------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./13_33_MAC_based.md) | [⏩](./13_35_Port_Blocking.md) |

---

## **Índice**

| Temario                                                                                  |
| ---------------------------------------------------------------------------------------- |
| [477. ¿Qué es el control de acceso a la red?](#477-qué-es-el-control-de-acceso-a-la-red) |
| [478. Control de acceso a la red](#478-control-de-acceso-a-la-red)                       |

# **NAC-based**

## **477. ¿Qué es el control de acceso a la red?**

![NAC-based](/img/13_Tools_for_Incident_Response_and_Discovery/NAC_based.webp "NAC-based")

### 🔹 1. Significado de Control de Acceso a la Red

El **NAC (Network Access Control)** es un conjunto de políticas, procesos y tecnologías que **controlan quién o qué puede conectarse a una red** y en qué condiciones.

Su objetivo es garantizar que solo **usuarios, dispositivos y aplicaciones autorizadas y seguras** tengan acceso.

👉 Piensa en NAC como el **portero de una discoteca**:

- Verifica **identidad** (¿quién eres?).
- Verifica **estado** (¿estás en condiciones? ej.: no borracho → en NAC: no infectado con malware).
- Decide si entras, te rechaza o te pone en “cuarentena”.

### 🔹 2. Ventajas del Control de Acceso a la Red

- **Seguridad reforzada** → solo dispositivos confiables acceden.
- **Cumplimiento normativo** (ej. HIPAA, PCI DSS) → asegura que dispositivos cumplen políticas.
- **Visibilidad completa** → identifica quién/qué está conectado en cada momento.
- **Reducción de ataques internos** → evita que empleados o visitantes inseguros propaguen malware.
- **Automatización de respuesta** → si un dispositivo se detecta vulnerable, se aísla automáticamente.

**Ejemplo práctico:**

En una universidad, un alumno conecta su laptop infectada. NAC detecta que no tiene antivirus actualizado y lo pone en **red de cuarentena** hasta que se corrige → protege al resto de la red.

### 🔹 3. Casos de uso comunes del NAC

1. **BYOD (Bring Your Own Device)** → empleados con laptops/teléfonos personales. NAC verifica que cumplan políticas antes de conectarse.
2. **Invitados/visitantes** → ofrece acceso restringido (ej.: solo Internet, no a servidores internos).
3. **Cumplimiento regulatorio** → bancos o hospitales usan NAC para asegurar que los dispositivos cumplen normas de seguridad.
4. **Respuesta ante incidentes** → si un endpoint se compromete, NAC lo aísla automáticamente.
5. **IoT** → controla cámaras, sensores o impresoras conectadas a la red.

### 🔹 4. Capacidades del NAC

- **Autenticación de usuario y dispositivo** (802.1X, credenciales, certificados).
- **Evaluación de postura de seguridad** → antivirus, parches, cifrado activo.
- **Control dinámico** → acceso completo, limitado o denegado según la política.
- **Segmentación de red** → asigna VLANs distintas (ej.: invitados, empleados, IoT).
- **Monitoreo continuo** → revisa cambios de estado en tiempo real.
- **Respuesta automática** → aislar/quitar acceso si detecta anomalías.

### 🔹 5. Importancia del Control de Acceso a la Red

El NAC es esencial porque:

- La red ya no es solo “oficina interna” → ahora incluye **cloud, IoT, dispositivos móviles**.
- Los atacantes suelen entrar por **usuarios legítimos comprometidos** o dispositivos mal configurados.
- Sin NAC, cualquiera que conecte un cable o WiFi podría tener acceso amplio.
- NAC asegura que **la identidad y el estado del dispositivo son confiables antes de permitir acceso**.

👉 En palabras simples: **NAC reduce la superficie de ataque** y **aumenta la confianza cero (Zero Trust)**.

### 🔹 6. Tipos de Control de Acceso a la Red

1. **NAC basado en 802.1X (estándar IEEE)**

   - Usa autenticación fuerte (certificados, RADIUS).
   - Ejemplo: laptop corporativa debe usar credencial AD + certificado digital.

2. **NAC basado en agentes**

   - Instala un software en el dispositivo que reporta postura de seguridad.
   - Ejemplo: el agente revisa si tienes antivirus actualizado.

3. **NAC sin agentes**

   - Evalúa el dispositivo sin instalar nada (útil para invitados).
   - Ejemplo: portal web de acceso para visitantes.

4. **NAC en línea (inline)**

   - El NAC se coloca en medio del tráfico → puede bloquear en tiempo real.

5. **NAC fuera de banda (out-of-band)**

   - NAC monitorea pero no interrumpe tráfico directamente; usa switches/routers para aplicar reglas.

### 🔹 7. Ejemplo simple de NAC en acción

Supongamos que en una empresa:

1. Un empleado conecta su laptop a la red.

2. El NAC:

   - Verifica **credenciales AD** (usuario válido).
   - Revisa que el SO tenga parches y antivirus actualizado.
   - Asigna **VLAN de empleados** con acceso a recursos corporativos.

3. Un visitante conecta su celular:

   - Se redirige a **portal cautivo**.
   - Solo obtiene acceso a Internet, no a los servidores internos.

4. Un IoT (impresora) se conecta:

   - NAC detecta que es un dispositivo no gestionado.
   - Se asigna a **VLAN IoT restringida**, sin acceso a datos sensibles.

✅ En resumen:

El **NAC es una capa crítica de ciberseguridad** que aplica el principio de “Zero Trust”: **no confiar en nada por defecto** y validar todo (usuario + dispositivo) antes de conceder acceso.

---

[🔼](#índice)

---

## **478. Control de acceso a la red**

### 1 — ¿Qué es NAC (definición breve)

**Network Access Control (NAC)** es el conjunto de tecnologías y políticas que **deciden si un dispositivo o usuario puede conectarse a la red y qué nivel de acceso recibe**, en función de su identidad, postura de seguridad y contexto (ubicación, hora, tipo de dispositivo).

Es la puerta de entrada dinámica que aplica el principio _Zero Trust_: **no confiar por defecto — verificar siempre**.

### 2 — Componentes esenciales de una solución NAC

- **Autenticación**: quién eres (802.1X / captive portal / MAC + RADIUS).
- **Posture assessment (evaluación de estado)**: ¿está parcheado, antivirus activo, configuración de cifrado, token de MDM/EDR?
- **Política / motor de decisión**: reglas que mapean identidad + postura → permiso (vlan, ACL, SGT, cuarentena).
- **Enforcement (aplicación)**: cómo se aplica la decisión (VLAN assignment, ACL push, SDN tag, bloqueo inline).
- **Orquestación / integración**: RADIUS, AD/LDAP, MDM/Intune, EDR, SIEM, firewall, switches/wireless controllers.
- **Visibilidad / reporting**: inventario en tiempo real de dispositivos y su estado.

### 3 — Cómo funciona (flujo típico, paso a paso)

1. **Conexión física o Wi-Fi**: un endpoint conecta al switch o al SSID.
2. **Autenticación**: 802.1X (EAP-TLS / PEAP) o fallback MAB/captive portal.
3. **Evaluación**: NAC pide posture info (agente o scan agentless).
4. **Decisión**: motor aplica política (si usuario es empleado y posture OK → VLAN corp; si BYOD y posture parcial → VLAN restringida; si no cumple → cuarentena).
5. **Enforcement**: switch asigna VLAN, firewall aplica ACLs, o se bloquea el puerto.
6. **Monitoreo continuo**: NAC re-evalúa posture y puede cambiar acceso al detectar cambios.

### 4 — Tipos / enfoques de NAC (con ejemplos)

- **802.1X (port-based)** — autenticación fuerte en switches y APs. Ejemplo: laptop corporativa con certificado (EAP-TLS) → RADIUS valida → asigna VLAN 100.
- **Agent-based NAC** — un agente instalado reporta posture (patch, AV). Ej.: agente con Intune + EDR informa estado a NAC.
- **Agentless NAC** — escaneo remoto/SSH/SNMP o inspección DHCP/DNS para identificar posture (útil para invitados/IoT).
- **Captive Portal (web-based)** — ideal para invitados: se redirige a login web y luego se aplica una política temporal.
- **MAB (MAC Authentication Bypass)** — fallback cuando 802.1X no está disponible (imprimoras, cámaras).
- **Inline vs Out-of-band** — inline puede bloquear tráfico en tiempo real; out-of-band usa switches/firewalls para enforcement (menos impacto en red).

### 5 — Ejemplos prácticos (casos de uso)

#### A — BYOD (empleado trae su laptop)

- **Flujo**: conecta → 802.1X PEAP o EAP-TLS → RADIUS consulta AD → agente posture valida AV y parche → si OK → VLAN `EMPLOYEE_FULL`; si no → VLAN `EMPLOYEE_RESTRICTED` con portal de remediación.
- **Remediación**: el NAC ofrece instrucciones y enlaces para actualizar OS/AV; cuando posture cambia, el NAC mueve al dispositivo al VLAN corporativa.

#### B — Visitante (guest wifi)

- **Flujo**: se conecta al SSID Guest → captive portal (registro con e-mail o voucher) → NAC asigna VLAN `GUEST_INTERNET_ONLY` y aplica ACLs que bloquean acceso interno.
- **Ventaja**: sin agente, fácil onboarding y control de duración del acceso.

#### C — IoT (cámara IP)

- **Flujo**: no soporta 802.1X → MAB identifica MAC → NAC asigna VLAN `IOT` con acceso restringido a servidores específicos y sin acceso a Internet directo.
- **Seguridad**: segmentación limita lateral movement si la cámara se compromete.

#### D — Equipo comprometido

- **Detección**: EDR detecta malware o NAC nota tráfico anómalo/patch faltante.
- **Acción**: NAC automáticamente mueve el host a `QUARANTINE_VLAN`, bloqueando acceso a recursos críticos y presentando instrucciones de remediación.

### 6 — Ejemplo técnico: 802.1X básico en switch Cisco (concepto)

```text
! Habilitar 802.1X global
dot1x system-auth-control

! Definir RADIUS server
radius server R1
 address ipv4 10.0.0.10 auth-port 1812 acct-port 1813
 key radiusSecret

! Configurar interfaz (puerto de acceso)
interface GigabitEthernet1/0/10
 switchport mode access
 switchport access vlan 10
 authentication open
 authentication port-control auto
 dot1x pae authenticator
 spanning-tree portfast
```

- RADIUS responde con VLAN o atributo para enforcement.
- Si 802.1X falla, se puede tener `authentication host-mode multi-auth` y fallback MAB.

> **Nota:** esto es configuración mínima de ejemplo; en producción hay que ajustar tiempoouts, seguridad, logging, y usar certificados EAP-TLS.

### 7 — Políticas NAC: ejemplo de Decision Matrix (simplificada)

| Identidad      | Posture (AV, Parches) | Dispositivo              | Acción / Enforcement                                     |
| -------------- | --------------------: | ------------------------ | -------------------------------------------------------- |
| Corporate user |                Cumple | Corporate-managed laptop | VLAN: `CORP_FULL` (acceso a apps)                        |
| Corporate user |               Parcial | BYOD                     | VLAN: `CORP_LIMITED` (no acceso DBs), portal remediación |
| Guest          |                   N/A | Personal phone           | VLAN: `GUEST_INTERNET` (solo internet)                   |
| IoT (camera)   |                   N/A | Unmanaged                | VLAN: `IOT` (ACLs a NVR only)                            |
| Any            |          Comprometido | Any                      | `QUARANTINE_VLAN` + ticket + notifications               |

### 8 — Integraciones típicas (qué conectar al NAC)

- **RADIUS / AAA** (Microsoft NPS, FreeRADIUS).
- **Directory services** (AD/LDAP) para identidad y grupos.
- **MDM / Intune / Jamf** para posture y certificados.
- **EDR (CrowdStrike, SentinelOne, etc.)** para detección amenazas y remediación.
- **SIEM / SOAR** para logging, orquestación y playbooks.
- **Switches / WLC / Firewalls** para enforcement (VLANs, ACLs, SGT tags en SD-Access).

### 9 — Capacidades clave que debe tener un buen NAC

- Autenticación fuerte (802.1X, certificados).
- Evaluación de posture automática (agent y agentless).
- Micro-segmentación / VLAN assignment / SDN tagging.
- Remediation workflows (portal + tickets).
- High-availability y escalabilidad (para grandes campus).
- Reporting e inventario en tiempo real.
- API para integraciones con IAM, EDR, SIEM.

### 10 — Métricas y KPIs útiles para NAC

- % de dispositivos **identificados** vs dispositivos conectados.
- % de dispositivos **compliant** (posture OK).
- **MTTI** (Mean Time To Isolate) cuando se detecta compromiso.
- Nº de dispositivos en **quarantine** y tiempo medio de remediación.
- Tasa de falsos positivos en aislamiento.
- Cobertura 802.1X (% de puertos con 802.1X habilitado).

### 11 — Buenas prácticas / recomendaciones

- **Empieza por pilot**: una VLAN / edificio / SSID.
- **Implementa 802.1X** en endpoints gestionados; usa captive portal para invitados.
- **Fallback controlado**: usa MAB solo para dispositivos sin 802.1X y parches post-identificación.
- **Integración EDR + MDM** para posture confiable; no confiar solo en MAC.
- **Seguridad de RADIUS**: usar IPsec/TLS, sincronizar certificados y proteger secretos.
- **Política de least privilege**: acceso mínimo necesario.
- **Monitoreo y logging**: enviar logs a SIEM y crear alertas para eventos críticos.
- **UX**: diseñar workflows de remediación simples para usuarios (instrucciones y soporte).
- **Gestión de excepciones** con ticketing y expiración automática.

### 12 — Errores frecuentes / limitaciones

- **Exceso de rigidez**: bloquear usuarios legítimos por posture demasiado estricto.
- **Confianza en MAC** (spoofing) sin otras verificaciones.
- **No integrar EDR/MDM** → posture incompleta.
- **No planear fallback** → impresoras y cámaras quedan fuera.
- **Logging insuficiente** → no se puede investigar incidentes.
- **Escalabilidad mal calculada** → NAC lento en picos (ej. entrada masiva a campus).

### 13 — Ejemplo de implementación por tamaño de empresa

#### PyME (≤200 empleados)

- **Recomendación**: 802.1X para portátiles corporativos + captive portal para invitados + básica segmentación VLAN para IoT.
- **Herramientas**: FreeRADIUS + switches que soporten 802.1X, MDM ligero (Intune).
- **Política**: dispositivos corporativos completos; BYOD con acceso limitado.

#### Empresa mediana / grande

- **Recomendación**: NAC comercial (Cisco ISE, Aruba ClearPass, FortiNAC) + integración EDR + MDM + SIEM + SDN para microsegmentación.
- **Alta disponibilidad** y orquestación para remediación automática.

### 14 — Consideraciones legales / privacidad

- Informar a usuarios sobre la evaluación de posture y la recolección de datos.
- Evitar recolectar PII innecesaria.
- Cumplir regulaciones locales (GDPR, LGPD) cuando se recolectan datos de dispositivos personales (BYOD).

### 15 — Flujo de respuesta ante incidente con NAC (ejemplo)

1. EDR detecta comportamiento malicioso en host A.
2. EDR envía evento a SIEM; SIEM notifica NAC (integration / webhook).
3. NAC mueve host A a `QUARANTINE_VLAN`, corta accesos críticos.
4. NAC crea ticket en ITSM y notifica al usuario con instrucciones de remediación.
5. IT/usuario remedia (antivirus, patch).
6. NAC re-evalúa posture; si OK, promueve al VLAN original y cierra ticket.

### 16 — Resumen rápido (qué hacer primero)

1. Inventario de dispositivos y segmentos.
2. Pilot con 802.1X en un segmento controlado.
3. Integrar RADIUS con AD y un MDM.
4. Habilitar posture checks básicos (AV, parche crítico).
5. Configurar captive portal para invitados.
6. Enviar logs a SIEM y crear alertas clave.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 33**               | **Siguiente 35**               |
| ------------------ | -------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./13_33_MAC_based.md) | [⏩](./13_35_Port_Blocking.md) |
