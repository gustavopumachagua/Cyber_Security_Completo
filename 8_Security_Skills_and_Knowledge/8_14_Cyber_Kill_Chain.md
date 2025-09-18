| **Inicio**         | **atrás 13**                                      | **Siguiente 15**            |
| ------------------ | ------------------------------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./8_13_Understand_Backups_and_Resiliency.md) | [⏩](./8_15_MFA_and_2FA.md) |

---

## **Índice**

| Temario                                        |
| ---------------------------------------------- |
| [222. Cyber Kill Chain](#222-cyber-kill-chain) |

# **Cyber Kill Chain**

## **222. Cyber Kill Chain**

![Cyber Kill Chain](/img/8_Security_Skills_and_Knowledge/Cyber_Kill_Chain.jpg "Cyber Kill Chain")

La **Cyber Kill Chain** es un modelo que describe las **fases del ciclo de vida de un ataque** informático, desde la preparación hasta la consecución de los objetivos del atacante. Fue popularizada por Lockheed Martin para ayudar a entender cómo romper o interrumpir el ataque en cualquiera de sus etapas. Es muy útil para diseñar detecciones, controles y ejercicios de red/defensa.

La versión clásica tiene **7 fases**:

1. Reconocimiento (Reconnaissance)
2. Weaponization (Armado)
3. Delivery (Entrega)
4. Exploitation (Explotación)
5. Installation (Instalación)
6. Command & Control (C2)
7. Actions on Objectives (Acciones sobre el objetivo)

### Fase por fase — qué hace el atacante, señales y defensas (con ejemplos)

#### 1) Reconocimiento

**Qué hace el atacante:** recopila información pública y privada sobre la organización: dominios, subdominios, empleados clave, tecnologías expuestas, versiones de software, cuentas públicas en GitHub, redes sociales, certificados TLS, etc.

**Ejemplo:** buscar en LinkedIn al gerente financiero y encontrar su correo para preparar spear-phishing.

**Señales / IOCs:** búsquedas inusuales en OSINT, escaneos pasivos del dominio, tráfico DNS extraño desde IPs desconocidas hacia dominios de investigación.

**Defensas:**

- Reducción de superficie: minimizar exposición pública (gestión de subdominios, CT logs).
- Hardened public services (WAF, latest patches).
- Monitorización OSINT: alertas cuando aparecen subdominios nuevos o certificados para tu dominio.
- Formación a empleados sobre manipulación de información pública.

#### 2) Weaponization (armado)

**Qué hace el atacante:** prepara el payload y el vector: por ejemplo incrustar un loader en un documento Word con macro ofuscado, o construir un instalador que contenga malware.

**Ejemplo:** crear un documento `.docm` con macro que descarga un loader desde un servidor C2.

**Señales / IOCs:** archivos adjuntos con macros, binarios empaquetados, artefactos ofuscados en repositorios públicos.

**Defensas:**

- Políticas que bloqueen/marquen macros en correo.
- Sandbox y análisis automatizado de adjuntos.
- Repositorios de build firmados (code signing) para tus propios artefactos.

#### 3) Delivery (entrega)

**Qué hace el atacante:** envía el arma al objetivo: phishing por email, malvertising, USB físico, exploit contra un servidor público.

**Ejemplo:** spear-phishing con un link a un documento malicioso alojado en cloud.

**Señales / IOCs:** correos con URLs acortadas o dominios nuevos, adjuntos sospechosos, redirecciones a hosts desconocidos.

**Defensas:**

- Email security (SPF/DKIM/DMARC), URL rewriting + sandboxing (ATP).
- Filtrado anti-phishing, bloqueo de dominios maliciosos y detección de anomalías en correo.
- Políticas de bloqueo de dispositivos externos (USB).

#### 4) Exploitation (explotación)

**Qué hace el atacante:** ejecuta el exploit que permite ejecutar código en el host (por ejemplo una macro que ejecuta PowerShell ofuscado, o explotación de una vulnerabilidad en un servidor web).

**Ejemplo:** macro ejecuta `powershell -EncodedCommand` que descarga y ejecuta el payload.
**Señales / IOCs:** ejecución de PowerShell con parámetros codificados, procesos hijos inusuales del navegador o MS Office, exploits de aplicaciones web detectados por WAF/IDS.

**Defensas:**

- Apagar/limitar PowerShell/ROPs; bloquear comandos peligrosos.
- EDR con detección basada en comportamiento (parent/child process trees).
- Patching rápido y WAF/IPS en servicios expuestos.

#### 5) Installation (instalación / persistencia)

**Qué hace el atacante:** instala malware permanente (backdoor, servicio, tarea programada), crea cuentas o modifica configuraciones para persistir.

**Ejemplo:** crea un servicio que ejecuta el RAT en cada inicio; modifica `Autorun` o crea tarea programada.

**Señales / IOCs:** creación reciente de servicios, tareas programadas desconocidas, cambios en registros (Windows Registry), nuevas llaves SSH autorizadas.

**Defensas:**

- Monitoreo de cambios en el sistema (EDR/Integrity monitoring).
- Restricciones de creación de servicios y control de privilegios (PoLP).
- Alertas por creación de cuentas con privilegios.

#### 6) Command & Control (C2)

**Qué hace el atacante:** el malware contacta a su servidor de mando para recibir instrucciones (beaconing) y/o exfiltrar datos.

**Ejemplo:** host infectado hace llamadas periódicas a `abcd-malicious[.]com` por HTTPS con beacons.

**Señales / IOCs:** tráfico saliente regular a dominios no habituales, dominios recién registrados, patrones de beaconing (intervalos regulares), DNS tunneling.

**Defensas:**

- Egress filtering (bloquear salidas innecesarias).
- DNS monitoring, proxy con inspección HTTPS (TLS inspection si legalmente permitido).
- Threat Intelligence para bloquear IOCs y detección de patrones de beaconing.

#### 7) Actions on Objectives (acciones sobre el objetivo)

**Qué hace el atacante:** cumple su misión: exfiltración de datos, cifrado de archivos (ransomware), sabotaje, manipulación de registros, fraude financiero.

**Ejemplo:** cifrado masivo de shares de red y publicación de nota de rescate; transferencia fraudulenta de fondos.

**Señales / IOCs:** actividad masiva de lectura y compresión de archivos, conexiones a repositorios externos de datos, creación de archivos con extensiones nuevas (ransom), aumento de tráfico saliente hacia host de exfiltración.

**Defensas:**

- Detección de exfiltración (baselines de tráfico, DLP).
- Backups inmutables y tested restores (recuperación tras ransomware).
- IAM fuerte, encriptación de datos y segmentación para limitar alcance.

### Ejemplo integrado (escenario corto)

**Escenario:** atacante hace recon en LinkedIn → arma documento con macro → entrega por spear-phishing a contabilidad → macro explota Office y ejecuta PowerShell → instala un loader que se comunica con C2 → lateralmente roba credenciales y exfiltra listas de clientes.
Mapeo kill chain: Recon → Weaponization → Delivery → Exploitation → Installation → C2 → Actions (data exfiltration).

**Controles prácticos aplicables:** email sandboxing + EDR + password rotation + MFA + DLP + backups.

### Kill Chain vs MITRE ATT\&CK

- **Kill Chain** = vista de alto nivel del _flujo_ de un ataque (útil para estrategia y planificación de defensa).
- **MITRE ATT\&CK** = catálogo granular de **técnicas** y sub-técnicas (útil para detección, hunting y mapeo de telemetría).
  En la práctica se usan ambos: Kill Chain para orquestar detecciones por fase; ATT\&CK para escribir reglas Sigma / YARA y playbooks. Por ejemplo, la fase "Exploitation" en Kill Chain mapea a técnicas ATT\&CK como `Exploitation for Client Execution` o `Exploitation for Privilege Escalation`.

### Cómo usar la Kill Chain para defender mejor (acciones concretas)

1. **Instrumenta visibilidad por capa**: logs de endpoint (EDR), red (proxy, DNS, NDR), identidad (IAM, logs AD), cloud (CloudTrail).
2. **Diseña detecciones fase-por-fase**: cubrir Recon (OSINT alerts), Delivery (email sandbox), Exploitation (EDR signatures/behavior), C2 (DNS/proxy anomalies), Actions (DLP/exfiltration).
3. **Prioriza controles con impacto**: bloquear Delivery (phishing) y detectar Exploitation/Installation rápido reduce mucho el impacto.
4. **Simula con Red/Blue/Purple teams**: ejecutar ejercicios que atraviesen toda la Kill Chain y medir dónde te detectan.
5. **Automatiza respuesta (SOAR)**: por ejemplo, si EDR detecta beaconing, ejecutar playbook que aísle host y revocar credenciales.

### Métricas útiles derivadas del Kill Chain

- **% de ataques detenidos por fase** (p. ej. 40% en Delivery, 20% en Exploitation).
- **MTTD por fase** (tiempo medio hasta detectar explotación, instalación, C2).
- **MTTR** (tiempo medio a contener y erradicar).
- **Cobertura de telemetría** (endpoints con EDR, % de tráfico inspeccionable).
- **Tasa de falsos positivos por regla** (optimizar para reducir fatiga).

### Checklist / Playbook rápido para SOC (acciónable)

- [ ] Habilitar EDR en todos los endpoints y alertas por ejecución de PowerShell ofuscado.
- [ ] Activar email sandboxing y políticas DMARC/ DKIM/ SPF.
- [ ] Monitorizar DNS y proxy para patrones de beaconing (intervalicidad, dominios nuevos).
- [ ] Implementar backups inmutables y pruebas de restore regulares.
- [ ] Realizar ejercicios Purple Team trimestrales que atraviesen toda la Kill Chain.
- [ ] Crear SOAR playbooks para aislamiento automático de hosts con C2 confirmado.
- [ ] Mapear reglas y detecciones a MITRE ATT\&CK para gobernanza y reporting.

### Conclusión rápida

La **Cyber Kill Chain** es una herramienta poderosa para **pensar como el atacante**, planear defensas y priorizar detecciones. Romper el ataque en sus fases permite aplicar controles concretos y medibles —y, sobre todo, encontrar el balance costo-efectividad: prevenir Delivery y detectar Exploitation/Installation muy temprano reduce significativamente el impacto.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 13**                                      | **Siguiente 15**            |
| ------------------ | ------------------------------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./8_13_Understand_Backups_and_Resiliency.md) | [⏩](./8_15_MFA_and_2FA.md) |
