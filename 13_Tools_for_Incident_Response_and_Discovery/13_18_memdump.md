| **Inicio**         | **atrás 17**               | **Siguiente 19**            |
| ------------------ | -------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_17_Wireshark.md) | [⏩](./13_19_FTK_Imager.md) |

---

## **Índice**

| Temario                      |
| ---------------------------- |
| [446. memdump](#446-memdump) |

# **memdump**

## **446. memdump**

![memdump](/img/13_Tools_for_Incident_Response_and_Discovery/memdump.png "memdump")

**Resumen rápido:** _memdump_ (volcado de memoria) es la acción de **capturar el contenido de la memoria volátil (RAM)** de un sistema o de un proceso concreto en un fichero para su análisis posterior. Los volcados de memoria son fundamentales en forense digital, respuesta a incidentes y depuración, porque contienen procesos en ejecución, credenciales temporales, claves en claro, sockets abiertos, artefactos de red y mucho más —pero también son datos muy sensibles.

> **Aviso legal y ético:** haz volcados **solo** en sistemas que poseas o para los que tengas permiso explícito. Capturar memoria de otros sin autorización puede ser ilegal.

### 1) ¿Qué es exactamente un _memdump_?

- **Memdump de proceso:** capturar sólo el espacio de direcciones (memoria) de un proceso (ej. `chrome`, `svchost`, `httpd`).
- **Memdump físico (RAM física):** volcar toda la RAM del sistema (imagen completa).
- **Memdump de kernel / crash dump:** volcados creados por el kernel tras un fallo (crash dump).

Cada tipo sirve para distintos objetivos: procesos para analizar comportamiento de una aplicación; RAM física para análisis forense completo (residentes, conexiones, claves, artefactos).

### 2) ¿Por qué y cuándo hacer un memdump?

- **Respuesta a incidentes:** extraer evidencias de malware en ejecución, comandos en memoria, C2, procesos ocultos.
- **Forense digital:** reconstruir actividad de usuario, credenciales en memoria, volcar artefactos de navegador.
- **Depuración/Debugging:** inspeccionar el estado interno de una aplicación (variables, heap, stack).
- **Recuperación de claves/secretos** (solo con autorización y en contextos forenses).

**Ventaja clave:** la RAM contiene información volátil que desaparece al apagar el equipo —si necesitas esa información, la debes capturar en caliente.

### 3) Herramientas comunes (por sistema operativo) y ejemplos de uso

> **Precaución:** muchos comandos requieren privilegios de administrador/root. Haz siempre una copia/registro y calcula hashes del volcado.

#### Windows

**Herramientas comunes:**

- **DumpIt** (simple, portable — captura memoria física).
- **WinPmem / Imager / AVML** (Microsoft/Volatility project): captura en formatos compatibles con Volatility o AFF4.
- **Procdump (Sysinternals)**: volcado de memoria de procesos (`procdump -ma <PID> out.dmp`).
- **Task Manager / ProcExp (Process Explorer)** también permiten _Create Dump File_ por proceso (GUI).

**Ejemplos:**

- **Volcado completo de proceso con ProcDump (Sysinternals):**

```powershell
# Ejecutar en PowerShell como Administrador
procdump -ma 1234 C:\dumps\process1234.dmp
```

`-ma` genera un volcado completo (memory + handles).

- **Volcado físico con WinPmem (ejemplo):**

```powershell
# WinPmem (ejecutable) - produce imagen en formato raw o AFF4
winpmem.exe -o C:\evidence\memory.aff4 --format=aff4
```

(La sintaxis puede variar según versión; consulta la herramienta instalada.)

- **DumpIt** (solo ejecutar el exe con privilegios, genera .raw/.mem en la unidad destino).

#### Linux

**Herramientas comunes:**

- **LiME (Linux Memory Extractor)** — módulo kernel que crea volcados en disco o vía red.
- **dd** sobre `/dev/mem` o `/proc/kcore` (raro y con limitaciones, no siempre accesible).
- **gcore** — crea un core dump de un proceso (útil para debugging).
- **avml** (Microsoft, soporte cross-platform) o **makedumpfile** para crash dumps.

**Ejemplos:**

- **gcore → volcar memoria de proceso:**

```bash
# como root
pid=1234
sudo gcore -o /tmp/core.$pid $pid
# Resultado: /tmp/core.1234.1234
```

`gcore` crea un core dump que puede abrirse con gdb o herramientas forenses.

- **LiME (kernel module) — volcado de RAM físico a archivo:**

```bash
# compilar e insertar el módulo lime.ko (se requiere compilar LiME en el kernel objetivo)
sudo insmod lime.ko "path=/mnt/evidence/mem.lime format=lime"
# o para formato raw:
sudo insmod lime.ko "path=/mnt/evidence/mem.raw format=raw"
```

LiME escribe la imagen de RAM al archivo dado.

- **dd sobre /dev/mem** (no recomendable; muchos kernels no permiten):

```bash
sudo dd if=/dev/mem of=/mnt/evidence/mem.raw bs=1M
```

(Suele requerir kernel configurado con /dev/mem accesible y es arriesgado).

#### macOS

**Herramientas comunes:**

- **gcore** (incluido) para volcados de proceso.
- **OSXpmem / mac-ram-capture** proyectos que permiten volcar memoria física.
- **lldb** puede usarse para inspeccionar procesos y volcar memoria.

**Ejemplo gcore:**

```bash
sudo gcore -o /tmp/core.1234 1234
```

### 4) Flujo recomendado para captura (best practice)

1. **Documenta** localización física, hora, operador y motivos.
2. **Minimiza la alteración** del sistema (no ejecutar comandos innecesarios).
3. **Preferir herramientas forenses acreditadas** (WinPmem, LiME, DumpIt, FTK Imager) en modo lectura/solo captura.
4. **Generar hash** del volcado justo después (MD5/SHA256).

   ```bash
   sha256sum memory.raw > memory.raw.sha256
   ```

5. **Guardar metadatos**: hostname, PID list, uptime, comandos en ejecución (si es seguro capturarlos).
6. **Transferir y analizar** la imagen en entorno aislado (workstation forense).
7. **Mantener cadena de custodia** y registros.

### 5) Ejemplos de análisis (herramientas y comandos)

**Volatility / Rekall** son frameworks estándar para analizar volcados de memoria. Ejemplos:

- **Listar procesos (Volatility 2):**

```bash
volatility -f memory.raw --profile=Win7SP1x64 pslist
```

(Tienes que identificar el perfil correcto: `imageinfo` primero.)

- **Extraer memoria del proceso (Volatility memdump):**

```bash
volatility -f memory.raw --profile=Win7SP1x64 memdump -p 1234 -D ./dumps
```

Esto extrae el espacio de direcciones del PID 1234 a un fichero.

- **Buscar cadenas útiles (strings + grep):**

```bash
strings memory.raw | egrep -i 'password|pass|credential|login|ssh'
```

Útil para hallazgos rápidos; produce falsos positivos, siempre verificar contexto.

- **Extraer conexiones de red:**

```bash
volatility -f memory.raw --profile=Win7SP1x64 netscan
```

(Muestra sockets, procesos asociados y endpoints.)

- **Dump de credenciales (ejemplo Windows LSASS dump + mimikatz análisis)**

  - Se puede volcar el proceso LSASS (que contiene credenciales) — **solo en casos forenses autorizados**:

```powershell
# con procdump (ejecutar como admin)
procdump -ma lsass.exe C:\dumps\lsass.dmp
```

- Luego analizar offline con herramientas especializadas (p. ej. mimikatz) — **solo con permiso**.

### 6) Limitaciones, riesgos y contramedidas

- **Tamaño:** un volcado de RAM puede ser muy grande (igual a la RAM instalada). Gestiona almacenamiento y transferencia.
- **Integrity:** si no calculas hashes y registras, la evidencia puede no ser admisible en un juicio.
- **Encrypted memory / PFS:** técnicas modernas (e.g., Secure Boot, kernel protections, aplicaciones con PFS) dificultan recuperar claves; no siempre podrás extraer secretos.
- **Antiforense / evasión:** malware sofisticado puede detectar y alterar herramientas de volcado o cifrar su payload en memoria.
- **Alteración del sistema:** la propia captura altera el estado (es inevitable al correr un extractor). Minimiza cambios y documenta.

### 7) Ejemplo práctico completo (flujo Windows → analizar con Volatility)

1. **Captura** (en máquina objetivo, con permiso):

   - Ejecutar _WinPmem_ o _DumpIt_ para generar `memory.aff4` o `memory.raw`.

2. **Verificar hash (en la estación forense):**

```bash
sha256sum memory.aff4 > memory.aff4.sha256
```

3. **Identificar perfil (Volatility 2):**

```bash
volatility -f memory.aff4 imageinfo
# imageinfo sugiere posibles profiles (ej. Win7SP1x64)
```

4. **Listar procesos:**

```bash
volatility -f memory.aff4 --profile=Win7SP1x64 pslist
```

5. **Buscar procesos sospechosos y extraer su memoria:**

```bash
volatility -f memory.aff4 --profile=Win7SP1x64 psscan
volatility -f memory.aff4 --profile=Win7SP1x64 memdump -p <PID> -D ./proc_dumps
```

6. **Analizar strings o usar YARA sobre el volcado:**

```bash
strings ./proc_dumps/process.1234.dmp | grep -i "api_key"
volatility -f memory.aff4 --profile=Win7SP1x64 yarascan -Y rules.yara
```

### 8) Buenas prácticas y recomendaciones finales

- **Usa herramientas con soporte y actualizadas** (WinPmem, LiME, DumpIt, AVML).
- **Haz hash y documentación** inmediatamente; conserva original y trabajas con copias.
- **Aísla análisis** en máquinas forenses sin conexión directa al entorno investigado (para evitar propagación de malware).
- **Respeta la ley y políticas**: necesario permiso y cadena de custodia.
- **Aprende a usar Volatility / Rekall** para obtener valor real de los volcados.
- **Automatiza** con scripts si haces capturas recurrentes (pero conserva logs y metadatos).

### 9) Recursos y lecturas recomendadas

- Volatility Project (documentación y plugins) — framework para análisis de memoria.
- LiME — Linux Memory Extractor (github).
- WinPmem / AVML — herramientas de adquisición para Windows/Linux.
- Artículos y cursos de forense de memoria (SANS, blogs de forense).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 17**               | **Siguiente 19**            |
| ------------------ | -------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_17_Wireshark.md) | [⏩](./13_19_FTK_Imager.md) |
