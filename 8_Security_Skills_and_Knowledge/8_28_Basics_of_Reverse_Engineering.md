| **Inicio**         | **atrás 27**                                                        | **Siguiente 29**                                        |
| ------------------ | ------------------------------------------------------------------- | ------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_27_Conceptos_basicos_de_la_gestion_de_vulnerabilidades.md) | [⏩](./8_29_Penetration_Testing_Rules_of_Engagement.md) |

---

## **Índice**

| Temario                                                                    |
| -------------------------------------------------------------------------- |
| [258. ¡Ingeniería inversa para todos!](#258-ingeniería-inversa-para-todos) |
| [259. ¿Qué es la ingeniería inversa?](#259-qué-es-la-ingeniería-inversa)   |

# **Fundamentos de la ingeniería inversa**

## **258. ¡Ingeniería inversa para todos!**

![ingeniería inversa](/img/8_Security_Skills_and_Knowledge/ingenieria_inversa.webp "ingeniería inversa")

La **ingeniería inversa** es el proceso de analizar un artefacto (software, firmware, protocolo, hardware) para **comprender su diseño, estructura o funcionamiento**, habitualmente sin disponer del código fuente o la documentación original.

Motivos legítimos: auditoría de seguridad, interoperabilidad, análisis forense, aprendizaje, investigación académica, recuperación de datos, y análisis de malware con fines defensivos.

### Legalidad y ética (imprescindible)

Antes de empezar: **solo trabaja sobre lo que te pertenece o tengas autorización explícita**. Evita usos que:

- vulneren licencias (DRM, software propietario sin permiso),
- permitan fraude o eludir protecciones,
- faciliten creación/propagación de malware.

Si tu objetivo es investigación defensiva, documenta permisos y mantén un entorno aislado (VMs, snapshots) para evitar daños.

### ¿Qué se puede analizar? (tipos de RE)

- **Binaries (ej. ELF, PE, Mach-O)** — aplicaciones compiladas.
- **Firmware / IoT** — imágenes de firmware de routers, cámaras.
- **Protocolos de red** — analizar cómo se comunican sistemas propietarios.
- **Aplicaciones móviles** — APKs Android, iOS apps.
- **Web / JavaScript minificado** — lógica del cliente, APIs ocultas.
- **Documentos y formatos binarios** — parseo y extracción de datos.
- **Hardware** — placas, chips, buses (más avanzado, requiere equipo).

### Grandes etapas del proceso (workflow típico)

1. **Reconocimiento** — ¿qué tipo de archivo? ¿arquitectura? ¿firma? (`file`, `readelf`, `pefile`)
2. **Análisis estático** — sin ejecutar: `strings`, `objdump`, decompilador (Ghidra, IDA).
3. **Análisis dinámico** — ejecutar en entorno controlado y observar comportamiento (`strace`, `ltrace`, debugger, Frida).
4. **Instrumentación / hooking** — interceptar funciones en tiempo real (Frida, DynamoRIO).
5. **Protocol/format RE** — capturar tráfico (Wireshark), extraer campos y recrear el protocolo.
6. **Documentación / reimplementación** — crear especificación o parchear/crear una implementación interoperable (solo con permiso).

### Herramientas esenciales (cheat-list)

- **Inspección básica:** `file`, `strings`, `hexdump`/`xxd`, `file`, `sha256sum`
- **ELF/PE utilities:** `readelf`, `objdump`, `nm`, `ldd`
- **Debuggers:** `gdb`, WinDbg
- **Dynamic tracing:** `strace`, `ltrace`, `procmon` (Windows)
- **Decompiladores/IDEs de RE:** **Ghidra** (gratis), **radare2 / Cutter**, **IDA Free/Pro**, **Binary Ninja**
- **Instrumentation:** **Frida**, **DynamoRIO**, **Pin**
- **Firmware / file carving:** **binwalk**, **firmware-mod-kit**, `dd`
- **Network / protocol:** **Wireshark**, **Scapy**
- **Mobile:** **apktool**, **jadx** (Android), **class-dump**, **cycript**/Frida (iOS)
- **Sandbox / Malware:** **Cuckoo**, VMs aisladas
- **Libraries:** **Capstone** (disasm), **Unicorn** (emulador)
  (Elige las que mejor encajen con tu objetivo.)

#### Ejemplo 1 — Analizar un binario “hello world” (seguro, en laboratorio)

Crea un pequeño programa C (propio). Analizarás sin hacer nada ilegal.

```c
// hello.c
#include <stdio.h>
int main() {
    printf("Hola, ingeniería inversa!\n");
    return 0;
}
```

Compila:

```bash
gcc -o hello hello.c
```

Pasos de RE (estático y dinámico):

1. **Tipo y dependencias**

```bash
file hello
ldd hello
```

2. **Buscar cadenas**

```bash
strings hello | grep 'Hola'
# Salida: Hola, ingeniería inversa!
```

3. **Desensamblar función main**

```bash
objdump -d -M intel hello | sed -n '/<main>/,/<_start>/p'
```

Interpretación: verás llamadas a `printf` y luego `exit`/`return`. Es un buen ejercicio para relacionar strings con llamadas a librerías.

4. **Ejecutar en gdb y ver call stack**

```bash
gdb ./hello
(gdb) break main
(gdb) run
(gdb) disassemble
```

**Qué aprendes:** cómo se representan las llamadas a funciones estándar en ensamblador, dónde están las cadenas, y cómo el binario enlaza a glibc. Todo sin modificar nada y con un binario que tú compilaste.

#### Ejemplo 2 — Tracing de llamadas dinámicas (sin ejecutar código malicioso)

A veces solo quieres saber qué librerías usa y qué llamadas hace:

```bash
strace -f -e trace=open,read,write,execve ./hello
```

Salida muestra `open()` de libc, `write()` a stdout, etc. Útil para entender comportamiento sin entrar a asm.

#### Ejemplo 3 — Decompilar con Ghidra (concepto)

1. Abre Ghidra → Nuevo proyecto → importa `hello`.
2. Ejecuta _Auto-Analyze_.
3. Examina la vista de funciones y la pseudo-decompilación de `main`.
   Ghidra muestra C-like pseudocódigo que te ayuda a entender la lógica sin leer ensamblador puro.

> Nota: Ghidra y radare2 son herramientas poderosas para aprender a mapear ensamblador → lógica de alto nivel.

#### Ejemplo 4 — Firmware / IoT (solo tu dispositivo o con permiso)

Supón que tienes una imagen de firmware que tú descargaste del fabricante de tu router.

1. **Detectar formato y extraer**:

```bash
binwalk -e firmware.bin
```

`binwalk` extrae sistemas de archivos incrustados (squashfs, cramfs). Después puedes montar la imagen extraída y examinar `/etc`, scripts, contraseñas en texto claro (lamentablemente frecuente).

2. **Montar y examinar**:

```bash
sudo mount -o loop squashfs-root.squashfs /mnt/fw
ls -la /mnt/fw
```

**Usos legítimos:** comprobar si tu dispositivo tiene credenciales por defecto, puertas traseras o servicios inseguros. Siempre con autorización o sobre tu propio hardware.

#### Ejemplo 5 — Reverse engineering de un protocolo (captura + análisis)

Supón que una app de IoT habla con un servidor en un puerto privado. Captura tráfico con Wireshark (en tu red de laboratorio):

1. Captura pcap con `tcpdump`:

```bash
sudo tcpdump -i eth0 -s 0 -w capture.pcap host 10.0.0.5
```

2. Abre en Wireshark y busca patrones: longitudes fijas, cabeceras, campos repetidos.
3. Identifica deltas en el tiempo, repeticiones y correlaciona con acciones de la app (botones, sensores).
4. Con esos indicios puedes escribir un script en **Scapy** que reproduzca mensajes para pruebas (solo en entorno autorizado).

### Técnicas avanzadas (breve explicación — sin pasos peligrosos)

- **Decompilación y reensamblado:** reconstruir lógica y (con permiso) parchear binarios para pruebas.
- **Emulación:** correr partes del binario en emuladores (Unicorn) para estudiar comportamiento aislado.
- **Hooking/runtime instrumentation:** Frida para interceptar funciones en apps móviles o desktop y ver parámetros en tiempo real.
- **Symbolic execution / fuzzing:** hallar rutas de ejecución y entradas problemáticas (AFL, libFuzzer). Esto se usa para descubrimiento de bugs y hardening.

> **Advertencia:** técnicas como patching o bypass de protecciones pueden ser dual-use. Úsalas solo para investigación legítima y documentada.

### Buenas prácticas de laboratorio

- Usa **VMs** o máquinas aisladas (red separada).
- Haz **snapshots** antes de cualquier cambio.
- Mantén registros/hashes de archivos originales.
- Documenta **fuente, pasos y hallazgos** (útil para reproducibilidad y legalidad).
- No publiques exploits o instrucciones para evadir protecciones sin contexto responsable.

### Proyectos y ejercicios para aprender (legales y educativos)

- **Reverse tu propio binario**: compila un C/C++ y analízalo.
- **Crackmes**: retos diseñados para RE (asegúrate que la web que los ofrece es legal).
- **Analizar un APK open-source**: descompila con `jadx`, estudia lógica.
- **Analizar pcap**: reconstruir protocolo y escribir un cliente compatible (Scapy).
- **Firmware analysis**: extrae y revisa tu router doméstico (solo tu equipo).
- **Implementa un parseador** para un formato binario encontrado: good practice para re-implementation.

### Rutas de aprendizaje y recursos

- **Libros:** “Practical Reverse Engineering”, “The IDA Pro Book”, “Practical Malware Analysis” (enfocado en malware defensivo).
- **Plataformas:** TryHackMe (módulos de RE), OpenSecurityTraining, CTFs (categoría RE).
- **Comunidades:** foros, GitHub repos con scripts y plugins de Ghidra/radare2.
- **Herramientas para dominar:** Ghidra, radare2/Cutter, Frida, Capstone.

### Resumen ejecutivo — ¿por qué aprender RE?

- Te vuelve **mejor ingeniero o analista**: entender cómo funciona un sistema a bajo nivel.
- Es clave para **seguridad defensiva**: auditoría, detección, análisis de muestras.
- Facilita **interoperabilidad**: recrear protocolos o formatos cuando la documentación no existe.
- Enseña pensamiento crítico: rastrear flujo de ejecución, hipótesis y verificación.

---

[🔼](#índice)

---

## **259. ¿Qué es la ingeniería inversa?**

### ¿Para qué sirve? (usos legítimos — con ejemplos)

- **Auditoría de seguridad / análisis de malware (defensivo).**

  _Ejemplo:_ analizar un binario sospechoso en un laboratorio para identificar qué hace y extraer indicadores de compromiso.

- **Interoperabilidad.**

  _Ejemplo:_ estudiar un protocolo propietario para crear un cliente compatible cuando no existe documentación oficial.

- **Recuperación y mantenimiento.**

  _Ejemplo:_ recuperar datos de una aplicación antigua cuyo código fuente se perdió.

- **Investigación y aprendizaje.**

  _Ejemplo:_ ver cómo un compilador optimiza código para aprender optimizaciones.

- **Verificación de cumplimiento y privacidad.**

  _Ejemplo:_ comprobar si una app móvil filtra datos sensibles.

### Aspectos legales y éticos — lo primero que debes saber

- **No** realices ingeniería inversa sobre software/firmware que **no te pertenece** o sin permiso explícito: puede infringir licencias (DRM), contratos o leyes locales.
- Para trabajo defensivo (pentest, malware analysis) **siempre** documenta y ten autorización por escrito.
- En la práctica profesional se suelen firmar **rules of engagement** o acuerdos de nondisclosure antes de empezar.

### Tipos comunes de ingeniería inversa

1. **Binaries / ejecutables** (PE/ELF/Mach-O) — aplicaciones compiladas.
2. **Firmware / IoT** — imágenes de dispositivos, routers, cámaras.
3. **Protocolos y formatos** — cómo se estructuran mensajes y archivos.
4. **Aplicaciones móviles** — APKs (Android) o paquetes iOS.
5. **Hardware / PCB** — análisis de placas, buses y chips (JTAG, UART).
6. **Web / JavaScript** — lógica cliente, ofuscación, APIs privadas.

### Técnicas principales (explicadas de forma clara)

#### 1. **Análisis estático**

Examinar el artefacto **sin ejecutarlo**.

- Objetivo: descubrir estructura, cadenas, símbolos, llamadas a librerías, secciones del binario.
- Resultado típico: listado de funciones, cadenas relevantes, dependencias, pseudocódigo si se usa un decompilador.
- Uso típico: primer acercamiento para formular hipótesis.

#### 2. **Análisis dinámico**

Ejecutar el objetivo **en un entorno controlado** y observar su comportamiento.

- Qué se observa: llamadas al sistema, archivos abiertos, conexiones de red, procesos hijos.
- Herramientas: depuradores, trazadores, sandboxes.
- Ideal para entender la lógica que solo aparece en ejecución.

#### 3. **Instrumentación / Hooking**

Modificar o interceptar llamadas internas en tiempo real (p. ej. con bibliotecas que injectan hooks) para ver parámetros y resultados de funciones. Muy útil en apps móviles/desktop.

#### 4. **Decompilación**

Usar un decompilador para transformar ensamblador en pseudocódigo legible (no perfecto, pero muy útil para entender algoritmos).

#### 5. **Análisis de firmware / filesystem**

Extraer y montar sistemas de archivos embebidos para inspeccionar scripts, credenciales por defecto o binarios auxiliares.

#### 6. **Análisis de protocolos**

Capturar tráfico (pcap), identificar patrones de mensaje y reconstruir el esquema de comunicación.

### Flujo de trabajo típico (alto nivel)

1. **Reconocimiento**: identificar tipo de archivo y arquitectura.
2. **Estático**: examinar strings, tablas de símbolos, secciones del binario y (si procede) decompilar.
3. **Dinámico**: ejecutar en laboratorio, trazar llamadas, observar red/FS.
4. **Instrumentación**: insertar hooks o emular funciones para ver datos internos.
5. **Documentación**: registrar hallazgos, IOCs, diagramas y posibles mitigaciones o reimplementaciones.
6. **(Opcional) Reimplementación**: crear una implementación compatible o parchear (solo con permiso).

### Herramientas representativas (nombres y para qué se usan)

- **Ghidra, IDA Pro, Binary Ninja, radare2/Cutter** → desensamblado y decompilación.
- **GDB, WinDbg** → depuración y análisis dinámico.
- **Frida** → instrumentación y hooking en tiempo real (apps móviles/desktop).
- **binwalk, firmware-mod-kit** → extracción de firmware / sistemas de archivos.
- **Wireshark, Scapy** → análisis y reproducción de protocolos.
- **strace, ltrace, procmon** → trazas de llamadas al sistema o librerías.
- **Unicorn/Capstone** → frameworks de emulación y disassembler para análisis programático.

> Nota: conocer el propósito de cada herramienta te permite combinarlas según el caso.

### Ejemplos prácticos (seguros y legales)

- **Ejemplo educativo (software propio):** compila un pequeño programa en C y usa un decompilador para ver cómo se transforma `printf` y cómo el compilador organiza las funciones. Esto ayuda a entender cómo se traduce código de alto nivel a ensamblador.

- **Ejemplo de firmware (propio dispositivo):** extraer la imagen de tu router doméstico (si el fabricante lo permite) y revisar scripts `/etc` para ver si existen credenciales por defecto o servicios inseguros.

- **Ejemplo de protocolo:** en un entorno de pruebas, captura el tráfico entre un dispositivo de tu laboratorio y su servidor para identificar campos de mensaje y replicar un cliente compatible (re-implementación para interoperabilidad).

### ¿Qué NO debes hacer?

- Evita publicar instrucciones o exploits que permitan eludir protecciones (DRM, autenticación) o propagar malware.
- No realices ingeniería inversa sobre software o hardware sin **autorización**.
- No intentes divulgar vulnerabilidades sin un proceso responsable de divulgación (coordinated disclosure).

### Cómo empezar (ruta de aprendizaje recomendada)

1. **Fundamentos**: C y C++, conceptos básicos de compilación y linking.
2. **Arquitectura**: conocimiento de x86/x64 (y ARM si trabajas móvil/IoT).
3. **Sistemas operativos**: cómo funcionan procesos, memoria, llamadas al sistema.
4. **Herramientas**: familiarizarte con Ghidra/radare2/Frida y con un depurador.
5. **Práctica controlada**: crackmes, CTFs categoría RE, VMs aisladas y retos educativos (TryHackMe, OverTheWire, etc.).
6. **Temas avanzados**: emulación, symbolic execution, fuzzing.

### Buenas prácticas y seguridad operacional

- Trabaja **siempre** en entornos aislados (VMs, redes separadas).
- Haz **snapshots** antes de experimentar.
- Mantén un **registro** (qué archivos, versiones y hashes) para reproducibilidad.
- Documenta hallazgos con claridad (para auditoría o divulgación responsable).
- Si descubres una vulnerabilidad real, sigue un **proceso responsable de divulgación** antes de publicar.

### Conclusión — por qué es valiosa la ingeniería inversa

La ingeniería inversa es una habilidad poderosa que abre puertas en **seguridad defensiva, interoperabilidad, investigación y reparación**. Bien practicada, ayuda a reducir riesgos reales: descubrir malware, cerrar fugas de información, y garantizar que nuestros sistemas se comporten según lo esperado. Pero es una disciplina que exige **ética, autorización y rigor técnico**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 27**                                                        | **Siguiente 29**                                        |
| ------------------ | ------------------------------------------------------------------- | ------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_27_Conceptos_basicos_de_la_gestion_de_vulnerabilidades.md) | [⏩](./8_29_Penetration_Testing_Rules_of_Engagement.md) |
