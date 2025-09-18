| **Inicio**         | **atrás 26**              | **Siguiente 28**            |
| ------------------ | ------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_26_GTFOBINS.md) | [⏩](./13_28_Event_Logs.md) |

---

## **Índice**

| Temario                                                                                                                                    |
| ------------------------------------------------------------------------------------------------------------------------------------------ |
| [463. WADCOMS](#463-wadcoms)                                                                                                               |
| [464. WADComs: Hoja de trucos interactiva de Windows/Active Directory](#464-wadcoms-hoja-de-trucos-interactiva-de-windowsactive-directory) |

# **WADCOMS**

## **463. WADCOMS**

### 1) ¿Qué es WADComs?

**WADComs** es un _cheat-sheet interactivo_ (proyecto de código abierto) que reúne una **lista curada de herramientas ofensivas y sus comandos** orientadas a entornos Windows / Active Directory. Su propósito es servir como referencia rápida para profesionales de seguridad (pentesters, red teams y también blue teams que quieran conocer TTPs).

### 2) Origen, autor y dónde encontrarlo

WADComs fue creado por John Woodman y se mantiene como sitio web + repositorio en GitHub (`WADComs/WADComs.github.io`). El proyecto es público, colaborativo y acepta contribuciones mediante archivos YAML/Markdown que describen cada “WAD Command”.

### 3) ¿Qué contiene exactamente? — estructura y tipos de entradas

Cada entrada en WADComs describe una **herramienta** (o técnica), su **propósito**, ejemplos de **comandos** y **contexto** (por ejemplo: enumeración, explotación, movimiento lateral, escalada). Las entradas están organizadas por categorías (enumeration, exploitation, persistence, privilege-escalation, etc.) y cada comando se guarda en un archivo `.md` con metadatos en YAML. Esto facilita búsquedas rápidas durante pruebas.

### 4) Ejemplos representativos (qué vas a encontrar)

A modo ilustrativo (sin dar recetas explotivas), ejemplos de herramientas/entradas que aparecen:

- **Impacket — `ntlmrelayx`**: herramienta para ataques de relay/NTLM que puede actuar como servidor SMB/HTTP y relevar credenciales a otros servicios. Es listada como técnica de explotación/relay. (entrada en WADComs).
- **Impacket — `samrdump` / `smb` utilities**: utilidades para enumerar cuentas, shares y otros servicios en AD. (entrada en WADComs).

> Observación: WADComs centraliza muchas utilidades comunes en engagement de Windows/AD (Impacket, PowerView/PowerSploit, BloodHound patterns, etc.), lo que facilita recordar sintaxis o parámetros.

### 5) ¿Por qué es útil para Red Teams y Pentesters?

- **Rapidez:** permite recordar y ejecutar comandos frecuentes sin perder tiempo buscando sintaxis.
- **Estandarización:** unifica comandos y ejemplos probados en entornos Windows/AD.
- **Documentación para reports:** al terminar un engagement el equipo puede referenciar entradas concretas.

  Usarlo, **siempre** en entornos autorizados, acelera pruebas y reduce errores de sintaxis.

### 6) ¿Por qué también es valioso para Blue Teams (defensa)?

- **Conocer TTPs**: saber qué herramientas y comandos usan los atacantes permite crear detecciones (SIEM, EDR, HIPS).
- **Hunting & Threat-intel**: ejecutar búsquedas por patrones de comandos o procesos padres que coincidan con entradas de WADComs.
- **Priorización de controles**: si muchas entradas muestran abuso de NTLM relay, el blue team puede priorizar mitigaciones contra NTLM/SMB.

### 7) Ejemplo práctico — tabla corta (herramienta → objetivo → detección defensiva)

(esta tabla es orientativa y útil para incorporar en playbooks)

| Herramienta / área                  | Objetivo (uso típico)                          | Señales / detecciones recomendadas                                                                                                  |
| ----------------------------------- | ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| Impacket `ntlmrelayx`               | Relay de credenciales NTLM → acceso a recursos | Tráfico SMB/LDAP anómalo desde hosts no esperados; creación de listeners SMB/HTTP; conexiones hacia múltiples hosts tras autenticar |
| `samrdump` (Impacket)               | Enumeración de cuentas/recursos SAMR           | Consultas RPC/SAMR inusuales; scripts Python invocados por usuarios no admin                                                        |
| PowerShell (módulos de enumeración) | Recolección de Active Directory info           | Exec de `powershell.exe` con cmdlines largos, llamadas a `Get-AD*`, uso de Invoke-Command desde hosts no esperados                  |

(para cada ítem el Blue Team implementa detecciones en Sysmon/EDR/SIEM: ProcessCreate + NetworkConnect + FileWrite).

### 8) Mapeo a MITRE ATT\&CK — por qué importa esto

WADComs agrupa comandos que suelen mapearse a técnicas ATT\&CK (Credential Access, Discovery, Lateral Movement, Exfiltration, etc.). Mapear entradas a ATT\&CK ayuda a: priorizar detecciones, comunicar impactos y redactar playbooks. Ej.: `ntlmrelayx` → **T1557** (Adversary-in-the-Middle) / **T1189** (Drive-by Compromise) dependiendo del vector. (Mapear cada herramienta al TTP correspondiente es práctica recomendada).

### 9) Riesgos y ética — cómo usar WADComs responsablemente

- WADComs es una **herramienta de doble uso**: su contenido puede facilitar ataques si se usa sin autorización.
- **Solo** utilízalo en entornos donde tengas permiso explícito (engagements contratados, labs privados).
- Blue teams pueden usarlo para construir detecciones y subirle la dificultad a los atacantes.

### 10) Cómo usar WADComs para crear detecciones (workflow práctico)

1. **Inventario**: identifica las entradas de WADComs que aplican a tu entorno (ej. Impacket, PowerShell).
2. **Extrae patrones**: campos típicos (nombres de ejecutables, flags, rutas temporales).
3. **Crear reglas**: en SIEM/EDR (p. ej. Sysmon ProcessCreate + CommandLine contiene `ntlmrelayx` o `samrdump`).
4. **Hunt**: ejecutar búsquedas históricas por commandline y procesos padres anómalos.
5. **Probar & ajustar**: usar testbeds controlados para calibrar falsos positivos.

### 11) ¿Cómo contribuir o personalizar WADComs?

WADComs es open-source: se contribuye añadiendo archivos `.md` con la metadata de cada comando en el repo. Si quieres que tu equipo tenga un cheat-sheet privado, puedes forkar el repo y mantener una versión interna (por ejemplo con solo las herramientas permitidas/curadas para tu org).

### 12) Buenas prácticas para equipos (resumen de acciones concretas)

Para **Red Teams**:

- Usar WADComs en pentests autorizados, documentar TTPs y parámetros usados.
- Evitar dañar datos; siempre obtener permiso por escrito.

Para **Blue Teams / SOC**:

- Incorporar los patrones de WADComs en reglas de detección (ProcessCreate.CommandLine, NetConn, FileWrite).
- Priorizar mitigaciones: deshabilitar servicios innecesarios, aplicar autenticación fuerte (p. ej. mitigaciones contra NTLM relay), segmentación.

Para **Gerencia / Compliance**:

- Tratar WADComs como fuente de conocimiento que revela vectores reales; financiar controles y ejercicios red/blue basados en hallazgos.

### 13) Recursos y lectura (para profundizar)

- Sitio oficial WADComs (cheatsheet interactivo).
- Repositorio GitHub (README, contribuciones y estructura).
- Entradas concretas (ej.: Impacket `ntlmrelayx`, `samrdump`) dentro del sitio WADComs.

---

[🔼](#índice)

---

## **464. WADComs: Hoja de trucos interactiva de Windows/Active Directory**

### ¿Qué es WADComs?

**WADComs** (Windows / Active Directory Commands) es un proyecto open-source —una hoja de trucos/cheatsheet interactiva— que recopila **comandos útiles y sintaxis comunes** para herramientas ofensivas y de post-explotación en entornos Windows y Active Directory. Está pensado para pentesters y red teams, pero es igualmente valioso para blue teams porque documenta TTPs reales que los defensores deben conocer.

Principales propiedades:

- Entradas por herramienta/función con **comandos de ejemplo** y contexto.
- Organización por categorías típicas: **enumeración**, **credenciales**, **relay**, **persistencia**, **movimiento lateral**, **exfiltración**, etc.
- Metadatos en archivos Markdown/YAML que facilitan búsqueda y mantenimiento.
- Diseñado para ser forkeado y adaptado para uso interno (por ejemplo, una versión corporativa).

### ¿Por qué importa WADComs para tu equipo?

- **Red Team / Pentest:** acelera pruebas, reduce errores de sintaxis y sirve como referencia limpia durante engagements.
- **Blue Team / SOC:** te dice _qué buscar_ — los patrones de comandos, nombres de ejecutables y parámetros que delatan actividad ofensiva.
- **Audit / Compliance:** ayuda a priorizar mitigaciones concretas (por ej., desactivar NTLMv1, endurecer LDAP, aplicar SMB hardening).

### Estructura típica de una entrada

Cada entrada suele incluir:

- Nombre de la herramienta (ej. Impacket, Rubeus, PowerView)
- Categoría / objetivo (ej. enumeración AD, relay NTLM, extracción de hashes)
- Comandos de ejemplo con parámetros (breve, sin pasos explotables en profundidad)
- Notas sobre contexto/prerrequisitos
- Mapeo a MITRE ATT\&CK (técnica(s) asociadas)
- Posibles detecciones y recomendaciones de mitigación

### Ejemplos representativos (concepto → por qué y cómo detectarlo)

> **Nota:** los ejemplos que siguen son conceptuales para defensa y detección — no son exploits paso a paso.

1. **Impacket — `ntlmrelayx` (relay NTLM)**

   - **Objetivo:** relé de credenciales NTLM para acceder a recursos (SMB/LDAP/HTTP) sin conocer la contraseña.
   - **Señales (Blue):** aparición de listeners SMB/HTTP en hosts no usuales, incremento de autenticaciones NTLM a servicios que normalmente usan Kerberos, conexión desde un host a múltiples servidores poco después de una autenticación.
   - **Mitigación:** deshabilitar NTLM donde sea posible, aplicar SMB signing/LDAP signing, segmentación y proxying, monitoreo de autenticaciones inusuales.

2. **Impacket — `smbclient` / `smbexec` / `psexec`**

   - **Objetivo:** movimiento lateral y ejecución remota a través de SMB/Windows admin shares.
   - **Señales:** conexiones SMB salientes desde estaciones de trabajo, creación/ejecución de servicios remotos, uso de cuentas administrativas desde endpoints no administrativos.
   - **Mitigación:** restringir shares admin, usar JIT/JEA, aplicar lista de control de hosts para SMB y MFA en admin paths.

3. **PowerView / PowerShell AD enumeration (`Get-NetUser`, `Get-NetComputer`)**

   - **Objetivo:** recolección de información del dominio (usuarios, grupos, ACLs, trusts).
   - **Señales:** `powershell.exe` con módulos o comandos de AD, largas cadenas en commandline con `Get-AD*` o `Get-Net*`, procesos de PowerShell originados por servicios web.
   - **Mitigación:** habilitar PowerShell logging completo, restringir ejecución remota, monitorizar parent→child de procesos.

4. **Rubeus (Kerberos abuse)**

   - **Objetivo:** extracción/renovación de tickets Kerberos (AS-REP roasting, overpass-the-hash, etc.).
   - **Señales:** actividad inusual en los servidores KDC, tickets de servicio no habituales, scripts o procesos usando `kirbi`/`ccache` artefactos.
   - **Mitigación:** endurecer krbtgt, aplicar monitorización KDC, limitar cuentas con SPNs innecesarios, rotación controlada de claves.

5. **SharpHound/BloodHound collection**

   - **Objetivo:** mapeo de relaciones AD para encontrar caminos de escalada.
   - **Señales:** queries LDAP masivas desde hosts de usuario, actividad de recolección repetida, consultas de grupo/membership atípicas.
   - **Mitigación:** monitorizar queries LDAP grandes, limitar capacidades de lectura de AD según necesidad.

### Mapeo a MITRE ATT\&CK (por qué usarlo)

WADComs facilita mapear cada comando/herramienta a técnicas ATT\&CK (por ejemplo: discovery T1087, credential access T1555, lateral movement T1021, persistence T1053). Ese mapeo:

- Ayuda a priorizar detecciones en SIEM.
- Permite comunicar riesgo en lenguaje de amenazas reconocido.
- Facilita reportes y playbooks alineados a frameworks.

### Cómo usar WADComs en tu organización (workflow práctico)

#### Para Red Team

1. Forquear la hoja y mantener un listado de “herramientas permitidas” para cada engagement.
2. Usar la cheatsheet para ejecutar pruebas controladas y documentar exactamente qué entradas TTPs dispararon detecciones.
3. Generar reportes que incluyan: comando usado, telemetría faltante (qué no se detectó) y recomendaciones.

#### Para Blue Team / SOC

1. **Inventario de entradas relevantes:** filtrar WADComs por herramientas que existen en tu entorno (Impacket, PowerShell, etc.).
2. **Extraer patrones:** nombres de ejecutables, flags, rutas temporales, procesos padres típicos.
3. **Crear reglas** (Sysmon / EDR / SIEM) basadas en dichos patrones.
4. **Hunt**: ejecutar búsquedas históricas por commandline/parent process y priorizar por riesgo.
5. **Validar**: simular detecciones en lab (con autorización) para calibrar falsos positivos.

### Reglas de detección ejemplo (conceptuales — adaptar a tu telemetry)

#### Sysmon / SIEM (pseudocódigo SIGMA-like)

- **Detección 1 — PowerView enumeration**

```yaml
title: PowerShell AD enumeration via PowerView
detection:
  selection:
    EventID: 1 # Sysmon process create
    Image|endswith: '\powershell.exe'
    CommandLine|contains: "Get-Net"
  condition: selection
level: high
description: Detect PowerView AD enumeration usage.
```

- **Detección 2 — ntlmrelay listener**

```yaml
title: Suspicious SMB/HTTP listener on non-admin host
detection:
  selection:
    EventID: 3                 # NetworkConnect
    RemotePort: 445 OR 80 OR 8080
    ProcessImage|contains: 'python' OR 'node' OR 'ruby'
  condition: selection and process.parent not in expected_admin_tools
level: high
description: Detect probable relay/listener processes on unexpected hosts.
```

- **Detección 3 — BloodHound / SharpHound LDAP mass query**

```yaml
title: Massive LDAP queries from single host
detection:
  selection:
    EventID: 4624 or 4688   # or LDAP query logs
    Count_of_LDAP_queries_in_5m: > threshold
  condition: selection
level: medium
description: Possible BloodHound collection / AD reconnaissance
```

> Ajusta campos a tu formato de logs (Sysmon, Windows Event IDs, EDR fields).

### Regla práctica: qué correlaciones reducen falsos positivos

- **Parent→Child**: PowerShell launched by `explorer.exe` o `w3wp.exe` → más sospechoso que por `cmd.exe`.
- **Sequence**: Enumeración (Get-AD\*) → creación de proceso de red (smbclient) → intento de ejecución remota → alta prioridad.
- **Destino**: conexiones a IPs / dominios no usados históricamente por esa máquina.

### Integración operativa: playbook mínimo para SOC (detección → respuesta)

1. **Alerta**: regla SIGMA dispara por `Get-Net*` + network connect a múltiples hosts.
2. **Enriquecimiento**: recolectar proceso padre, user, host role (server/desktop), grupos AD del usuario.
3. **Hunt**: buscar actividad similar en red, horas previas y posteriores.
4. **Contención**: aislar host (según política), bloquear credenciales si hay evidencia de abuso de credenciales.
5. **Recolección**: volcado de memoria, Sysmon logs, ejecución `whoami /groups`, lista de procesos.
6. **Remediación**: eliminar artefactos, reset credenciales afectadas, aplicar mitigaciones (patches, configuración).
7. **Lessons Learned**: actualizar reglas y playbooks; identificar gaps telemétricos.

### Cómo personalizar WADComs para tu organización

- **Fork/privatiza** el repo: mantener solo entradas relevantes y añadir notas de detección internas.
- **Añade metadatos**: mapea cada comando a la telemetría que necesitas (fields de Sysmon / EDR).
- **Crea CSV** exportable con columnas: `herramienta|comando_ejemplo|TTP(MITRE)|detección_sugerida|prioridad` — listo para alimentar en trackers/POA\&M.
- **Mantén un changelog**: qué entradas han aparecido en ejercicios Red Team y qué reglas detectaron cada una.

### Riesgos / ética

- WADComs es doble-uso: su documentación puede ayudar a atacantes si se publica sin control.
- Usa WADComs **solo** en entornos autorizados; en una empresa, mantén una versión interna si el repo público contiene demasiada información sensible para publicar.

### Tabla rápida de referencia (30s cheat sheet — conceptual)

| Herramienta               |                Uso típico | Señal defensiva                               | Acción inmediata                           |
| ------------------------- | ------------------------: | --------------------------------------------- | ------------------------------------------ |
| Impacket `ntlmrelayx`     | Relay NTLM → pivot/acceso | Listeners SMB/HTTP en host no habitual        | Bloquear egress, aislar host               |
| SharpHound                |            Recolección AD | Pulsos LDAP masivos desde host                | Investigar, bloquear cuenta si necesario   |
| PowerView (`Get-NetUser`) |            Enumeración AD | PowerShell cmdlines con Get-Net\*             | Revisar parent→child y usuario             |
| Rubeus                    |            Kerberos abuse | Actividad KDC inusual                         | Alertar KDC admin, revisar tickets         |
| `psexec` / `smbexec`      |        Movimiento lateral | Creación de servicios remotos, conexiones SMB | Buscar lateral movement, aislar host       |
| `mimikatz` (o variantes)  |         Dump credenciales | Proceso inyectando LSASS access               | Capturar evidencias, forzar reset de creds |

### Qué puedo generar ahora mismo (elige una opción y lo creo)

1. **CSV/Tabla** (lista de \~25 entradas WADComs: herramienta, comando ejemplo, TTP MITRE, detección sugerida, prioridad).
2. **Conjunto de reglas SIGMA** básicas para Sysmon/Windows orientadas a PowerView / Impacket / SharpHound (lista lista para adaptar).
3. **Playbook SOC** (paso a paso) para un incidente típico detectado con una entrada WADComs (detección→contención→recolección→remediación).
4. **Versión interna personalizada** estilo WADComs para tu empresa (fork con notas de detección y políticas).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 26**              | **Siguiente 28**            |
| ------------------ | ------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_26_GTFOBINS.md) | [⏩](./13_28_Event_Logs.md) |
