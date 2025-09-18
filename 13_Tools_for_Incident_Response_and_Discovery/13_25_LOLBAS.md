| **Inicio**         | **atrás 24**         | **Siguiente 26**          |
| ------------------ | -------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./13_24_CSF.md) | [⏩](./13_26_GTFOBINS.md) |

---

## **Índice**

| Temario                                                                                                            |
| ------------------------------------------------------------------------------------------------------------------ |
| [459. Entendiendo los riesgos de las LOLBAS en seguridad](#459-entendiendo-los-riesgos-de-las-lolbas-en-seguridad) |
| [460. LOLBAS T1105, Abuso de procesos de MS](#460-lolbas-t1105-abuso-de-procesos-de-ms)                            |

# **LOLBAS**

### 1. Descubriendo nuevas amenazas LOLBAS: paso a paso

**LOLBAS** son binarios, scripts o librerías ya presentes en el sistema operativo (Windows, Linux, macOS) que los atacantes abusan para ejecutar acciones maliciosas sin necesidad de malware externo.

- **Ejemplo**: en Windows, usar **`certutil.exe`** para descargar un archivo malicioso desde internet o **`mshta.exe`** para ejecutar código malicioso en HTML.

Este enfoque dificulta la detección porque:

- Se usan herramientas legítimas firmadas por Microsoft/Linux.
- No generan alertas típicas de malware (pues no son ejecutables desconocidos).

### 2. Paso 1: El enfoque manual

El análisis manual implica:

- Revisar documentación oficial del sistema (binarios incluidos en Windows/Linux).
- Probar uno a uno qué binarios permiten ejecución de código, movimiento lateral, persistencia o exfiltración de datos.
- **Ejemplo**:

  - Probar si `regsvr32.exe` permite cargar un script remoto.
  - Revisar si `wmic.exe` puede ejecutar comandos en máquinas remotas.

_Ventaja_: permite descubrir técnicas nuevas.

_Desventaja_: consume mucho tiempo.

### 3. Paso 2: El enfoque automatizado

Aquí se usan herramientas y scripts que analizan múltiples binarios y verifican:

- ¿Puede descargar archivos?
- ¿Puede ejecutar comandos externos?
- ¿Puede escalar privilegios o saltarse restricciones?

**Ejemplo**:

- En Windows, un script en PowerShell puede recorrer todos los binarios de `System32` y probar diferentes parámetros para ver si ejecutan payloads.
- En Linux, analizar automáticamente binarios con permisos **SUID/SGID** (`find / -perm -4000`) para identificar candidatos que un atacante podría usar.

_Beneficio_: descubrimiento masivo y sistemático.

### 4. Paso 3: Encontrar ejecutores de LOLBAS

No todos los binarios son igual de útiles. Los atacantes buscan:

- **Descargadores** → (`certutil.exe`, `bitsadmin.exe`).
- **Ejecutores de scripts** → (`mshta.exe`, `cscript.exe`).
- **Persistencia** → (`schtasks.exe`, `reg.exe`).
- **Movimiento lateral** → (`psexec.exe`, `wmic.exe`).

**Ejemplo real**:

Un ransomware puede desplegarse usando **`wmic.exe`** para ejecutar comandos en PCs vecinas sin necesidad de dropper adicional.

### 5. Puntos clave para la defensa contra LOLBAS

Defenderse de LOLBAS es un reto porque no se pueden simplemente eliminar esos binarios (el sistema los necesita).

#### Para **Red Teamers**:

- Úsalos para simular ataques realistas.
- Desarrolla cadenas de ataque usando binarios comunes para medir la visibilidad del Blue Team.

#### Para **Blue Teamers**:

- Implementar **detección basada en comportamiento**, no solo firmas.
- Configurar alertas SIEM para uso inusual de binarios (ejemplo: `certutil.exe` accediendo a internet).
- Limitar privilegios: AppLocker, Windows Defender Application Control (WDAC), políticas de ejecución.

#### Para **Investigadores**:

- Documentar y compartir nuevos casos de LOLBAS.
- Contribuir a proyectos como [LOLBAS Project](https://lolbas-project.github.io/).
- Probar constantemente binarios en entornos aislados.

### 6. Binarios y scripts de Vivir de la Tierra (LOLBAS): un desafío persistente

- Los **LOLBAS no son malware en sí**, sino **herramientas legítimas mal usadas**.
- Cambian con cada actualización de sistema, lo que significa que constantemente aparecen nuevos candidatos.
- **Ejemplo de persistencia**:

  - `mshta.exe` puede ejecutar un archivo `.hta` desde internet al inicio del sistema.
  - Un atacante solo necesita una línea en el registro de Windows para mantenerse después de reinicios.

👉 Por eso se consideran una amenaza **persistente**: no se pueden eliminar, solo controlar, monitorear y limitar.

✅ **Conclusión:**

Las **LOLBAS son armas de doble filo**: útiles para administradores y críticos para atacantes. Descubrirlos requiere investigación manual y automatizada, pero defenderse exige **visibilidad, políticas de restricción, detección de anomalías y conciencia continua**.

---

[🔼](#índice)

---

## **460. LOLBAS T1105, Abuso de procesos de MS**

### 1 — ¿Qué es T1105 / Ingress Tool Transfer y por qué importa con LOLBAS MS?

- **T1105 (Ingress Tool Transfer)** en MITRE ATT\&CK describe el movimiento inicial o posterior de **herramientas / binarios / payloads** hacia un host comprometido (transferir archivos desde un sistema remoto).
- **LOLBAS (Living Off The Land Binaries and Scripts)** son utilidades legítimas del sistema operativo que atacantes abusan para transferir, ejecutar o persistir sin traer malware "extra".
- Cuando un atacante usa **binarios Microsoft firmados** para transferir herramientas, reduce la telemetría "sospechosa" y complica la detección (firmas, whitelists, reputación).

### 2 — Principales ejecutores Microsoft / LOLBAS utilizados para T1105 (resumen)

Estas son las piezas más comunes que verás en investigaciones y ejercicios Red/Blue:

- **certutil.exe** (Windows) — utilitario para certificados; muy usado para descargar/decodificar archivos.
- **bitsadmin.exe** / **Background Intelligent Transfer Service (BITS)** / APIs Start-BitsTransfer — transferencia de archivos en background.
- **PowerShell (powershell.exe / pwsh)** — capacidades HTTP/FTP, Base64, Invoke-WebRequest/Net.WebClient/Start-BitsTransfer.
- **msiexec.exe** — instaladores MSI; puede instalar o desplegar paquetes descargados.
- **mshta.exe / wscript.exe / cscript.exe** — ejecutar HTA/VBScript/JScript que pueden descargar y ejecutar código.
- **bitsadmin** (obsoleto en muchas versiones pero aún presente en entornos) y **bits transfer via COM**.
- **certmgr, ieexec, regsvr32.exe (con scrobj.dll/exec) y rundll32.exe** — menos para transferencia directa, pero útiles en cadenas de ejecución tras descargar algo.
- **WebClient service / SMB/NetUse** — montar recursos remotos para copiar archivos.

> Nota: la lista evoluciona con sistemas y parches; hay proyectos que mantienen catálogos (p. ej. LOLBAS project).

### 3 — Ejemplos (con propósito defensivo / educativos)

A continuación muestro ejemplos **didácticos** de cómo estas utilidades se abusan y qué señales dejan. No son recetas de ataque — son **patrones para detección**.

#### Ejemplo A — uso de `certutil.exe` para traer un binario

**Patrón de abuso:** un proceso legítimo (p. ej. `svchost.exe` o `explorer.exe`) lanza `certutil.exe` que accede a una URL externa y crea un archivo ejecutable en `%TEMP%`.
**Señales defensivas:**

- `certutil.exe` con parámetros inusuales (download/encode/decode) o acceso a dominios no confiables.
- Escritura de ejecutables en carpetas temporales por `certutil.exe`.
- Descarga seguida de ejecución por un proceso no administrador.

#### Ejemplo B — BITS transfer (`bitsadmin` / Start-BitsTransfer)

**Patrón:** uso de BITS para bajar archivos en background; BITS puede reanudar y ser silencioso, y el tráfico puede parecer legítimo.
**Señales:**

- Creación de trabajos BITS por cuentas que normalmente no los crean.
- Conexiones HTTP(S) salientes desde endpoints que no deberían hacer transferencias.
- Baja tasa de error y transferencia "a trozos" fuera de patrones normales.

#### Ejemplo C — PowerShell con webclient / Invoke-WebRequest

**Patrón:** PowerShell invocado desde procesos no esperados (p. ej. `mshta` → `powershell`) o PowerShell sin registro de AMSI/Transcription.
**Señales:**

- Comandos PowerShell con cadenas Base64 largas, llamadas a `DownloadFile`, `WebClient`, `Invoke-WebRequest`, `Start-BitsTransfer`.
- Procesos padres anómalos (p. ej. `winword.exe` → `powershell.exe`).

### 4 — Detección: reglas y comportamientos que debes monitorizar

Para detectar transferencias via LOLBAS céntrate en **contexto, anomalía y secuencias** (parent→child→network). Ejemplos de detección:

#### Indicadores y heurísticas

- Ejecución de `certutil.exe`, `bitsadmin.exe`, `mshta.exe`, `msiexec.exe`, `regsvr32.exe`, `rundll32.exe` con parámetros que impliquen descarga o ejecución dinámica.
- Escritura de ejecutables o scripts a carpetas temporales (`%TEMP%`, `C:\Windows\Temp\`) seguida de ejecución.
- Creación/ejecución de trabajos BITS por cuentas de servicio o usuarios normales.
- PowerShell con base64 o comandos de descarga y sin registros de AMSI o PowerShell logging completos.
- Conexiones salientes HTTP(S) a dominios no habituales inmediatamente tras la ejecución de un LOLBAS.

#### Regla SIGMA (pseudocódigo) — ejemplo para `certutil` sospechoso

```yaml
title: Suspicious certutil download to temp
logsource:
  product: windows
  service: sysmon
detection:
  selection:
    EventID: 1
    Image|endswith: '\certutil.exe'
    CommandLine|contains|all:
      - "http"
      - ".exe"
  condition: selection
level: high
description: Detect certutil downloading an executable via http(s) into temp directory
```

(Adaptar a campos de Sysmon/Evtx del entorno.)

#### Detección BITS (conceptual)

- Alerta si `bitsadmin.exe` o acciones de BITS crean trabajos y el destino local es carpeta temporal + origen remoto no confiable.
- Monitorear eventos de creación de trabajos BITS (ETW / eventos de Windows).

### 5 — Reglas y ejemplos de alertas EDR/Correlation

- Correlación: `ProcessCreation` (certutil/mshta/powershell) + `NetworkConnect` (to suspicious domain) + `FileCreate` (temp\*.exe) → alerta crítica.
- Detección de “parent-child mismatch”: e.g., `explorer.exe` raremente debería lanzar `bitsadmin` o `certutil` en entornos hardening — si ocurre, investigarlo.
- Detección de descargas seguidas por ejecución desde `%TEMP%` o rutas de usuario.

### 6 — Mitigaciones efectivas (priorizadas)

1. **Políticas de control de aplicaciones** (AppLocker, WDAC)

   - Permitir solo binarios necesarios; bloquear ejecución de `certutil`, `bitsadmin`, `mshta` para usuarios no administrativos o entornos de endpoint.

2. **Harden services**

   - Deshabilitar el servicio **WebClient** si no se usa; restringir BITS si no es necesario.

3. **Egress control / Proxy**

   - Forzar todo tráfico HTTP(S) a pasar por proxy que inspeccione destinos; bloquear dominios/URLs sospechosas.

4. **Registro y telemetría**

   - Habilitar Sysmon, PowerShell logging (ModuleLogging, ScriptBlockLogging), Windows Event Forwarding y EDR (prevention+response).

5. **Monitorizar patrones de ejecución**

   - Alertas por ejecución de LOLBAS combinada con actividad de red inusual o creación de ficheros ejecutables en temp.

6. **Least privilege & account hygiene**

   - Evitar que cuentas de usuario tengan permisos para instalar/ejecutar operaciones administrativas; desactivar cuentas inactivas.

7. **Segmentación**

   - Limitar movimiento lateral para que, aun cuando algo se descargue, su impacto sea contenido.

8. **Respuesta automática**

   - Integrar playbooks: aislamiento de host, captura de volcado, recolección de evidencia (procmon/sysinternals, network capture).

### 7 — Qué deben hacer los equipos Red/Blue e investigadores (puntos clave)

#### Para Red Teamers (ético / pruebas)

- Simular cadenas que usen LOLBAS para evaluar detección, pero **hacerlo en entornos autorizados y con reglas de seguridad**.
- medir _visibility gaps_: ¿qué se detecta? ¿qué telemetría falta?
- Generar reportes con IOCs y recomendaciones concretas (parámetros, rutas, parents).

#### Para Blue Teamers

- Implementar logging profundo (Sysmon + EDR).
- Establecer reglas basadas en contexto (parent, destination, time).
- Automatizar respuestas rápidas para contener transferencias y posteriores ejecuciones.
- Revisar y endurecer AppLocker/WDAC para bloquear usos indebidos manteniendo operatividad.

#### Para Investigadores / Threat Intel

- Mantener catálogos de nuevos LOLBAS y parámetros de abuso (proyectos tipo LOLBAS).
- Compartir TTPs (tactics, techniques, procedures) y dominos/URLs maliciosos en feeds internos.

### 8 — Casos reales y patrones observados (resumen)

- Muchas campañas de malware y APTs usan `certutil` y BITS para descargar payloads y evadir detección.
- `mshta` y `regsvr32` han sido usados para ejecutar scripts remotos sin escribir archivos en disco (fileless stages).
- Las cadenas suelen combinar: **descarga (LOLBAS)** → **ejecución (PowerShell/rundll32)** → **persistencia (schtasks/registry)** → **movimiento lateral**.

### 9 — Checklist de respuesta rápida para un posible incidente T1105 con LOLBAS

1. **Aislar** el host sospechoso de la red.
2. **Recolectar**: Sysmon logs, PowerShell logs, captura de red, listado de procesos y trabajos BITS.
3. **Identificar**: comando exacto, parent process, destino de descarga, timestamps.
4. **Bloquear**: dominio/IP de descarga en proxy/firewall.
5. **Corroborar**: buscar otros hosts con misma URL o comportamiento (IOC hunting).
6. **Remediar**: eliminar artefactos, rotar credenciales si aplica.
7. **POA\&M**: ajustar controles (AppLocker, egress, logging) para prevenir reaparición.

### 10 — Reglas SIGMA/Splunk/ELK de ejemplo (más concretas para Blue)

- **SIGMA (conceptual)**: detectar `certutil` con URL y archivo `.exe` destino.
- **Splunk SPL (ejemplo conceptual)**:

```
index=wineventlog EventCode=1 Image="*\\certutil.exe" CommandLine="*http*"
| table _time ComputerName User Image CommandLine ParentImage
```

- **ELK / Elastic**: alertar si `bitsadmin` crea un job and the job target is external.

#### 11 — Buenas prácticas para minimizar falsos positivos y operatividad

- Basarse en **contexto** (¿ese usuario suele descargar? ¿ese proceso suele ejecutar ese binario?).
- Crear **listas blancas de dominios/URLs** de negocio para evitar alertas repetidas.
- Priorizar alertas con combinaciones (ej. `certutil` + descarga de exe + ejecución desde temp = alta prioridad).

### 12 — Recursos y acciones siguientes recomendadas (para tu equipo)

- Habilitar/afinar: **Sysmon**, **PowerShell Transcription / ScriptBlockLogging**, **EDR** con detección de comportamiento.
- Revisar AppLocker/WDAC y desplegar políticas por fases.
- Implementar reglas SIGMA/Correlación en SIEM para los patrones citados.
- Realizar ejercicios Red vs Blue centrados en T1105/LOLBAS y revisar gaps.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 24**         | **Siguiente 26**          |
| ------------------ | -------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./13_24_CSF.md) | [⏩](./13_26_GTFOBINS.md) |
