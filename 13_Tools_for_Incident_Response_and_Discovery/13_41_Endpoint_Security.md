| **Inicio**         | **atrás 40**                 | **Siguiente 42**            |
| ------------------ | ---------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_40_Jump_Server.md) | [⏩](./13_42_VirusTotal.md) |

---

## **Índice**

| Temario                                                                                                                                   |
| ----------------------------------------------------------------------------------------------------------------------------------------- |
| [490. ¿Qué es la seguridad de endpoints?](#490-qué-es-la-seguridad-de-endpoints)                                                          |
| [491. Los puntos finales son la puerta de entrada de TI: ¡protéjalos!](#491-los-puntos-finales-son-la-puerta-de-entrada-de-ti-protéjalos) |

# **Endpoint Security**

## **490. ¿Qué es la seguridad de endpoints?**

![Endpoint Security](/img/13_Tools_for_Incident_Response_and_Discovery/Endpoint_Security.webp "Endpoint Security")

### 🔹 ¿Qué es la seguridad de puntos finales?

La **seguridad de endpoints** (endpoint security) es el conjunto de **herramientas, políticas y tecnologías** que protegen los **dispositivos finales** (endpoints) contra amenazas cibernéticas.

Se trata de evitar que los atacantes utilicen una laptop, PC, móvil o servidor como **puerta de entrada** hacia la red de la organización.

Ejemplo real:

- Si un empleado descarga un archivo malicioso en su laptop, un buen sistema de seguridad de endpoints puede detectar y bloquear la amenaza antes de que se propague a la red corporativa.

### 🔹 ¿Qué se considera un punto final?

Un **endpoint** es cualquier dispositivo que se conecta a la red y que puede ser un objetivo para atacantes.

Ejemplos:

- Laptops y PCs de empleados.
- Smartphones y tablets.
- Servidores físicos o virtuales.
- Máquinas de punto de venta (POS).
- IoT (cámaras IP, impresoras conectadas, routers).

💡 En ciberseguridad, **cada endpoint es un posible eslabón débil** que hay que proteger.

### 🔹 Importancia de la seguridad de los endpoints

- **Superficie de ataque grande**: cada nuevo dispositivo en la red es un riesgo potencial.
- **Puerta de entrada común**: muchos ciberataques empiezan en un endpoint comprometido.
- **Trabajo remoto**: empleados fuera de la red corporativa exponen dispositivos a redes poco seguras (WiFi públicas).
- **Cumplimiento normativo**: normas como GDPR, HIPAA, PCI-DSS exigen protección de datos en todos los dispositivos.

Ejemplo: Un ransomware que entra por la PC de un usuario puede cifrar toda la red en cuestión de horas.

### 🔹 Cómo funciona la protección de endpoints

Un sistema de **Endpoint Protection Platform (EPP)** o **Endpoint Detection and Response (EDR)** protege de la siguiente manera:

1. **Instalación de un agente en cada endpoint**

   - Un software ligero que monitoriza procesos, archivos y comportamiento del dispositivo.

2. **Prevención**

   - Bloquea malware conocido con firmas, heurísticas y listas negras.
   - Ejemplo: Detectar un archivo .exe malicioso y bloquearlo antes de que se ejecute.

3. **Detección**

   - Usa IA y machine learning para identificar comportamientos sospechosos.
   - Ejemplo: Un proceso desconocido que empieza a cifrar cientos de archivos rápidamente → posible ransomware.

4. **Respuesta**

   - Aísla el endpoint comprometido para evitar propagación.
   - Envía alertas a los administradores de seguridad (SOC).
   - Ejemplo: Desconectar automáticamente una laptop infectada de la red.

5. **Análisis forense**

   - Guarda registros (logs) de lo ocurrido para investigar cómo entró la amenaza.

### 🔹 Software de protección de endpoints vs. software antivirus

- **Antivirus tradicional (AV)**

  - Se centra en detectar **malware conocido** con bases de firmas.
  - Poco efectivo contra ataques modernos (malware polimórfico, fileless, zero-day).

- **Protección de endpoints (EPP/EDR)**

  - Prevención + detección de amenazas avanzadas.
  - Usa IA, machine learning y análisis de comportamiento.
  - Responde automáticamente y proporciona análisis forense.

Ejemplo comparativo:

- Un antivirus detecta un virus ya registrado en su base de datos.
- Un EDR detecta que un proceso intenta ejecutar **PowerShell** para descargar malware oculto (fileless attack), aunque nunca se haya visto antes.

### 🔹 Funcionalidad principal de una solución de protección de endpoints

- Antivirus y antimalware de nueva generación.
- Detección de comportamiento sospechoso.
- Prevención contra ransomware.
- Control de dispositivos (USB, externos).
- Firewall de endpoint.
- Aislamiento de red en caso de compromiso.
- Actualización y parcheo centralizado.
- Alertas y reportes en tiempo real.

### 🔹 La importancia de la arquitectura basada en la nube

La mayoría de soluciones modernas de endpoint funcionan en la **nube**, lo que permite:

- **Escalabilidad**: manejar miles de dispositivos distribuidos globalmente.
- **Actualizaciones en tiempo real**: nuevas amenazas detectadas en un lugar se bloquean en todos los clientes casi al instante.
- **Menos consumo de recursos**: el procesamiento pesado se hace en la nube.
- **Gestión centralizada**: los administradores controlan todos los endpoints desde un solo panel.

Ejemplo: Una empresa con 2000 laptops distribuidas en diferentes países puede ver y controlar la seguridad desde un **dashboard en la nube**.

### 🔹 Protección avanzada de endpoints de CrowdStrike

**CrowdStrike Falcon** es un líder en protección de endpoints, con capacidades avanzadas:

- **EDR en la nube**: monitoreo en tiempo real con IA.
- **Prevención de ataques sin archivos (fileless)**.
- **Threat Hunting**: caza proactiva de amenazas.
- **Análisis forense en minutos**: reconstrucción de ataques para entender el vector de entrada.
- **Módulo Falcon Prevent**: reemplaza antivirus tradicional.
- **Escalabilidad**: protege desde 1 dispositivo hasta miles sin hardware extra.

Ejemplo real:

Una organización detecta que un atacante intentó usar **Mimikatz** para robar credenciales en una laptop. CrowdStrike:

1. Bloquea la ejecución.
2. Aísla la máquina.
3. Envía alerta al SOC.
4. Proporciona un timeline con cada paso del atacante.

### ✅ Conclusión

La **seguridad de endpoints** es esencial porque cada dispositivo conectado es un posible objetivo para ataques. Hoy en día, las soluciones modernas (EPP/EDR) van mucho más allá de un antivirus: **previenen, detectan, responden y permiten análisis forense** en tiempo real, muchas veces usando la nube.

Un buen ejemplo es **CrowdStrike Falcon**, que combina IA, hunting de amenazas y escalabilidad para proteger a empresas modernas contra ransomware, malware sin archivos y ataques dirigidos.

---

[🔼](#índice)

---

## **491. Los puntos finales son la puerta de entrada de TI: ¡protéjalos!**

### 1 — ¿Por qué los endpoints importan tanto?

Los endpoints (laptops, desktops, servidores, móviles, IoT, kioscos, POS) son **el primer y más frecuente punto de entrada** para ataques porque:

- están en manos de usuarios humanos (phishing, error humano),
- muchas veces están fuera del perímetro (teletrabajo),
- suelen tener software desactualizado o configuraciones débiles,
- exponen servicios (RDP, SSH, SMB) que pueden ser atacados desde Internet.

**Ejemplo real (ilustrativo):** un empleado abre un correo con un adjunto malicioso → se ejecuta un script PowerShell que descarga y ejecuta un cargador → el atacante obtiene persistencia y comienza a moverse lateralmente.

### 2 — Vectores de ataque habituales (con ejemplos)

- **Phishing / spear-phishing:** link o adjunto que ejecuta malware.
- **Software sin parches:** exploits públicos para servicios expuestos (ej.: RCE en servicios web).
- **Acceso remoto inseguro (RDP/SSH) expuesto:** fuerza bruta o exploits.
- **USB malicioso / social engineering:** autorun, cargas desde pendrives.
- **Malware fileless / PowerShell / WMI:** difícil de detectar con AV tradicional.
- **Privilegios excesivos:** usuarios con permisos de administrador que instalan o ejecutan código.
- **IoT no gestionado:** cámaras o impresoras como pivot.

### 3 — Consecuencias (por qué le duele a la empresa)

- **Ransomware** que cifra datos y paraliza operaciones.
- **Exfiltración de datos sensibles** (clientes, IP, credenciales).
- **Impagos/ sanciones** por incumplimiento normativo.
- **Pérdida de confianza y coste de remediación** (forense, notificaciones, litigios).

### 4 — Principio básico: defensa en profundidad para endpoints

No hay una única solución que lo arregle todo. Combina controles técnicos, procesos y formación:

**Capa técnica (ejemplos):**

1. **EPP + EDR** (protección + detección/respuesta en el endpoint).

   - EPP: prevención (antivirus NG).
   - EDR: detección de comportamiento, telemetría, aislamiento remediación.
   - Ejemplos: Microsoft Defender for Endpoint, CrowdStrike, SentinelOne, Sophos, Trend, Bitdefender.

2. **Gestión de parches** automatizada (Windows Update/WSUS, SCCM, Intune; apt/yum/spacewalk para Linux).
3. **Control de acceso**: MFA, control de privilegios (least privilege), JIT (Just-In-Time access).
4. **Network segmentation & NAC**: separar usuarios, servidores y IoT; aplicar NAC para evaluar posture.
5. **Application allowlisting** (solo ejecutar binarios permitidos).
6. **Hardening OS**: deshabilitar SMBv1, cerrar puertos innecesarios, activar firewall local.
7. **Cifrado de disco** (BitLocker, FileVault) para proteger datos en reposo.
8. **Backups y pruebas de restauración** (offline / immutable).
9. **Logging centralizado** y SIEM (ingest de EDR, logs de endpoint, proxy, firewall).
10. **Formación / phishing simulation** y políticas BYOD.

### 5 — Ejemplos / Comandos prácticos (rápido para ejecutar hoy)

#### Windows — checks básicos y hardening

- **Habilitar Firewall y ver estado:**

```powershell
# Encender Firewall
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True

# Ver reglas actuales
Get-NetFirewallRule | Select DisplayName, Enabled, Direction, Action
```

- **Habilitar BitLocker (disco del sistema):**

```powershell
Enable-BitLocker -MountPoint "C:" -EncryptionMethod XtsAes256 -UsedSpaceOnlyEncryption
```

- **Forzar Windows Defender (si usas otro, adapta):**

```powershell
Set-MpPreference -DisableRealtimeMonitoring $false
Get-MpComputerStatus
```

- **Bloquear ejecución macros Office via GPO:** `User Configuration → Administrative Templates → Microsoft Office → Disable macros with notification`.

#### Linux — hardening y detección

- **Instalar un agente EDR (ejemplo genérico):**

```bash
# ejemplo conceptual: instalar agente
curl -sSL https://eds.example/agent/install.sh | sudo bash
```

- **Habilitar firewall (ufw) y cerrar accesos:**

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp from 192.168.1.0/24      # solo desde gestión
sudo ufw enable
```

- **Habilitar auditd (auditoría de exec):**

```bash
sudo apt install auditd
sudo auditctl -w /usr/bin -p x -k exec-watch
```

- **Habilitar AppArmor/SELinux** (según distro) y aplicar perfiles.

#### Buenas reglas de firewall de host

- Permitir solo puertos necesarios (HTTP/HTTPS, management host IPs).
- Bloquear todo lo demás por defecto.

### 6 — Ejemplo de detección y respuesta (ransomware, flujo)

**Escenario:** un endpoint empieza a cifrar archivos y el EDR detecta actividad anómala.

**Flujo de respuesta automatizado:**

1. EDR detecta alta tasa de I/O y renombrado de extensiones → _alert_.
2. Políticas automáticas: **aislar** la máquina de la red, bloquear procesos sospechosos, crear ticket en SOAR.
3. SOC investiga: ver timeline de procesos (PowerShell iniciador, conexión C2).
4. Restablecer desde backup inmutable o snapshot si es daño irreparable.
5. Post-mortem: identificación vector (phishing), parche/updating, revocar credenciales.

**Ejemplo práctico:** EDR bloquea PowerShell con comportamiento inusual y ejecuta `network-isolate` API, además crea Snapshot y notifica al equipo.

### 7 — Políticas y controles organizativos (ejemplo de política corta)

**Política mínima de Endpoints (extracto):**

- Todos los endpoints corporativos deben tener **EDR activo** y reportando a SIEM.
- Windows y Linux deben aplicar **patches críticos** en 72h (SLA).
- **Disk encryption** obligatorio.
- Usuarios no tendrán privilegios locales por defecto; elevación por petición (JIT).
- Todo acceso remoto requiere **MFA** y conexión por **bastion (jump host)** o VPN corporate.
- Backups restaurables y probados cada 30 días.

### 8 — Herramientas y stack recomendado (ejemplos)

- **EDR/EPP**: CrowdStrike, Microsoft Defender, SentinelOne, Bitdefender.
- **Hunting/Telemetry**: osquery, Velociraptor, Wazuh, Elastic Endpoint.
- **Patch automation**: SCCM/WSUS/Intune, Ansible, Satellite/Spacewalk.
- **NAC**: Cisco ISE, Aruba ClearPass, FortiNAC.
- **Logging & SIEM**: Splunk, Elastic + Beats, QRadar, Azure Sentinel.
- **Backup immutable**: Veeam, Rubrik, AWS S3 Object Lock.

### 9 — Checklist de hardening y operación (qué hacer ya)

**Inmediato (hoy/48h):**

- Instalar/activar EDR en endpoints críticos.
- Forzar parches críticos pendientes.
- Activar firewall local y deshabilitar servicios innecesarios.
- Configurar backups y validar una restauración.

**Mediano plazo (30 días):**

- Aplicar control de privilegios (remove local admin).
- Implementar application allowlisting en servidores clave.
- Desplegar NAC para evaluar posture en inicio de sesión.

**Continuo:**

- Simulacros de phishing y formación.
- Revisiones regulares de logs y hunting proactivo.
- Pruebas de restauración de backup y playbooks IR.

### 10 — Métricas (qué medir para saber si estás mejorando)

- % endpoints con EDR activo (objetivo 100%).
- % endpoints compliant con parches (por criticidad).
- Mean Time To Detect (MTTD) y Mean Time To Remediate (MTTR).
- Número de endpoints aislados por mes y resultados.
- Detecciones por tipo (phishing, ransomware, lateral movement).

### 11 — Playbook básico de incidente (resumen rápido)

1. **Detect:** alerta EDR / SIEM.
2. **Triage:** identificar alcance (host(s), user, proceso, red).
3. **Contain:** aislar endpoint, bloquear cuentas comprometidas, capturar memoria si hace falta.
4. **Eradicate:** eliminar malware, revocar credenciales, parchear vector.
5. **Recover:** restaurar desde backup/restauración controlada.
6. **Lessons Learned:** root cause, actualizar controles, comunicar.

### 12 — Plan 30/60/90 días (ejecutable)

- **30 días:** inventario, EDR en críticos, parches urgentes, backups.
- **60 días:** políticas de least-privilege, NAC piloto, app allowlisting en servidores productivos.
- **90 días:** SIEM ingest completo, playbooks IR, pruebas de tabletop, formación a usuarios.

### 13 — Ejemplo narrativo (cómo EDR detiene un ataque)

1. Usuario abre doc malicioso (phishing).
2. Malware lanza `powershell` con una línea ofuscada → EDR detecta comportamiento inusual (download-execute pattern) y bloquea antes de payload.
3. EDR crea evento, extrae el script, marca IOC, aísla endpoint, y el SOC ve timeline para forense.
4. Gracias a backups y aislamiento, no hubo cifrado ni exfiltración.

### 14 — Recomendaciones finales y llamada a la acción

- **No esperes** a un incidente para invertir en EDR + procesos.
- Empieza por **inventario** y por llevar EDR a los endpoints más críticos.
- Automatiza parches y pruebas de restauración.
- Aplica el principio de **menor privilegio** y controla el acceso remoto con bastion hosts y MFA.
- Monitorea, practica y mejora: la seguridad de endpoints es una **actividad operativa continua**, no un proyecto puntual.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 40**                 | **Siguiente 42**            |
| ------------------ | ---------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_40_Jump_Server.md) | [⏩](./13_42_VirusTotal.md) |
