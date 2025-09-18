| **Inicio**         | **atrás 6**        | **Siguiente 8**       |
| ------------------ | ------------------ | --------------------- |
| [🏠](../README.md) | [⏪](./13_6_dd.md) | [⏩](./13_8_hping.md) |

---

## **Índice**

| Temario                                              |
| ---------------------------------------------------- |
| [424. Tail man page](#424-tail-man-page)             |
| [425. Linux Tail Comandos](#425-linux-tail-comandos) |

# **Tail**

## **424. Tail man page**

![Tail](/img/13_Tools_for_Incident_Response_and_Discovery/tail.png "Tail")

### ¿Qué es `tail`?

`tail` es una utilidad para mostrar las **últimas líneas** (o bytes) de uno o más archivos. Es muy usada para ver los últimos registros de logs y para **seguir** (monitorizar) la salida que va creciendo en tiempo real.

### Cómo leer la man page de `tail`

Ejecuta:

```bash
man tail
```

La man page típica tiene secciones:

- **NAME** — nombre breve.
- **SYNOPSIS** — forma de invocar (`tail [OPCIONES] [ARCHIVO]...`).
- **DESCRIPTION** — explicación general de comportamiento.
- **OPTIONS** — lista de flags (`-n`, `-f`, `-c`, `-F`, `--pid=`, `--retry`, …) y su significado.
- **EXAMPLES** — (a veces la incluye) ejemplos de uso.
- **EXIT STATUS** — códigos de salida (0 = ok, >0 = error).
- **SEE ALSO** — comandos relacionados (`head`, `less`, `multitail`, `tac`).

Consejo: `man tail | col -b` quita caracteres de control si necesitas limpiar la salida.

### Sintaxis general

```
tail [OPCIONES] [ARCHIVO]...
```

Si no se pasa `ARCHIVO`, `tail` lee de la **entrada estándar** (stdin).

### Opciones clave (resumen práctico)

- `-n NUM`, `--lines=NUM`

  Mostrar las últimas `NUM` líneas. `-n 10` es el valor por defecto: las últimas 10 líneas.

  Ejemplo: `tail -n 50 archivo.log` → últimas 50 líneas.

- `-n +NUM`

  Empieza a mostrar desde la línea `NUM` (contando desde 1). Ejemplo `tail -n +1` imprime todo desde la primera línea (equivalente a `cat`).

- `-c BYTES`, `--bytes=BYTES`

  Mostrar los últimos `BYTES` bytes del archivo. Ej.: `tail -c 512 archivo.bin`.

- `-f`, `--follow[=descriptor|=name]`

  Seguir el archivo: muestra las últimas líneas y espera nuevas entradas (útil para logs).

  `--follow=descriptor` (el comportamiento por defecto) sigue el descriptor abierto (si el archivo es reemplazado, `tail` seguirá el descriptor viejo).

  `--follow=name` seguirá el nombre del archivo (cuando rotan logs y se crea un nuevo archivo con el mismo nombre, `tail` seguirá el nuevo).

- `-F`

  Prácticamente `--follow=name --retry`. Muy útil para logs rotados: si el archivo desaparece y reaparece, `tail -F` volverá a conectarse.

- `--retry`

  Si el archivo no existe, intenta reabrirlo hasta que aparezca. Combinado con `--follow=name` es muy útil.

- `-q`, `--quiet`, `--silent`

  No imprimir encabezados (cuando se pasan varios archivos).

- `-v`, `--verbose`

  Siempre imprimir encabezados con el nombre del archivo (aunque sea uno solo).

- `-s SECS`, `--sleep-interval=SECS`

  Intervalo entre comprobaciones cuando se usa `-f` (por defecto suele ser 1 s). Puedes aumentarlo para reducir carga.

- `--pid=PID`

  (GNU) Cuando se usa con `-f`, terminar `tail` automáticamente cuando el proceso `PID` deje de existir. Muy práctico en scripts.

- `--help`, `--version`

  Ayuda y versión.

> Nota: algunas opciones (`--pid`, `--retry`, `--follow=name`, `-F`) son de GNU `tail` (coreutils) y pueden variar en BSD/macOS. Comprueba `man tail` en tu sistema.

### Comportamiento con rotación de logs — punto crítico

- `tail -f archivo.log` sigue el descriptor abierto. Si `archivo.log` es renombrado y se crea un nuevo `archivo.log`, **`tail -f` seguirá el archivo renombrado** (no el nuevo).
- `tail -F archivo.log` o `tail --follow=name --retry archivo.log` **seguirá el nuevo archivo** tras rotación (comportamiento deseado para logs rotados por `logrotate`).

### Ejemplos prácticos y explicaciones

1. **Mostrar últimas 10 líneas (default)**

```bash
tail /var/log/syslog
```

2. **Mostrar últimas 50 líneas**

```bash
tail -n 50 /var/log/syslog
```

3. **Seguir (monitorizar) un log en tiempo real**

```bash
tail -f /var/log/nginx/access.log
```

Presiona `Ctrl+C` para parar.

4. **Seguir y mostrar el nuevo archivo después de rotación**

```bash
tail -F /var/log/nginx/access.log
```

5. **Seguir y filtrar patrones en tiempo real (grep con buffer de línea)**

```bash
tail -F /var/log/nginx/access.log | grep --line-buffered ' 500 '
```

`--line-buffered` hace que `grep` no espere a bufferizar líneas; útil para monitorizar en tiempo real.

6. **Seguir y terminar cuando un PID muere**

```bash
tail --pid=12345 -f /var/log/app.log
```

`tail` terminará automáticamente si el proceso `12345` finaliza.

7. **Mostrar últimos 1 KB (1024 bytes)**

```bash
tail -c 1024 archivo.bin
```

8. **Mostrar desde la línea 100 en adelante**

```bash
tail -n +100 archivo.txt
```

9. **Ver varias fuentes a la vez**

```bash
tail -f /var/log/auth.log /var/log/syslog
```

`tail` imprimirá encabezados con el nombre de archivo (puedes usar `-q` para suprimirlos).

10. **Seguir un archivo remoto vía SSH**

```bash
ssh user@server 'tail -F /var/log/nginx/access.log'
```

11. **Usar `less` como alternativa interactiva**

```bash
less +F /var/log/syslog
```

`less +F` es parecido a `tail -f` pero permite retroceder (presiona `Ctrl+C` dentro de `less` para salir del modo seguimiento).

12. **Obtener progreso de grandes archivos (tail con sed/awk)**
    No aplica directamente como `dd`, pero para extraer rangos usa `sed -n '100,200p' archivo`.

### Ejemplo simulado de salida

```bash
$ tail -n 3 ejemplo.log
2025-09-15 10:01:23 INFO Service started
2025-09-15 10:02:01 WARN High memory usage
2025-09-15 10:02:34 ERROR Connection timeout
```

Con `tail -F ejemplo.log` verás estas líneas y luego nuevas a medida que se escriban.

### Buenas prácticas / trucos útiles

- Para producción y scripts que deben sobrevivir a rotación de logs usa `tail -F` (o `--follow=name --retry`).
- Al pipear `tail -f` a `grep`, añade `--line-buffered` a `grep` para ver coincidencias en tiempo real.
- Para visualización y búsqueda interactiva usa `less +F`.
- Usa `--pid` para detener `tail` automáticamente cuando termine un proceso supervisor.
- Si supervisas muchos archivos y necesitas colores/ventanas usa `multitail` o `lnav`.

### Diferencias entre GNU (Linux) y BSD/macOS

- GNU `tail` (coreutils) tiene opciones como `--pid`, `--retry`, `--follow=name` y `-F`.
- BSD/macOS `tail` puede no soportar `--pid` o `--retry` exactamente con los mismos nombres; las opciones básicas `-n`, `-f`, `-c` sí están.
  Siempre verifica con `man tail` en tu sistema.

### Comportamiento de salida / códigos de salida

- `tail` devuelve **0** si tiene éxito, >0 si hay error (por ejemplo fichero inexistente si no usas `--retry`).
- Mensajes de error (archivo no encontrado, permisos) aparecen en `stderr`.

### Comandos relacionados

- `head` — ver el inicio de archivos.
- `less` / `more` — paginadores.
- `tac` — imprimir archivo en orden inverso.
- `multitail` / `lnav` — monitorización avanzada de logs.
- `awk` / `sed` / `grep` — procesar/filtrar la salida de `tail`.

### Mini cheat-sheet (rápido)

- `tail file` — últimas 10 líneas.
- `tail -n 100 file` — últimas 100 líneas.
- `tail -n +50 file` — desde la línea 50 en adelante.
- `tail -c 512 file` — últimos 512 bytes.
- `tail -f file` — seguir el archivo.
- `tail -F file` — seguir y reabrir tras rotación.
- `tail --pid=PID -f file` — seguir hasta que el PID termine.
- `tail -f file | grep --line-buffered foo` — buscar en tiempo real.

### Conclusión

La **man page de `tail`** documenta estas y más opciones; leerla (`man tail`) te muestra el comportamiento exacto en tu sistema. `tail` es imprescindible para administración y debugging: sencillo para ver las últimas líneas, pero con opciones (`-f`, `-F`, `--pid`, `--retry`) que lo hacen robusto para monitorizar logs en entornos reales.

---

[🔼](#índice)

---

## **425. Linux Tail Comandos**

### Linux — Comandos `tail` (explicación detallada con ejemplos)

`tail` es la herramienta estándar para ver **las últimas líneas** (o bytes) de uno o varios archivos y para **seguir** (monitorizar) archivos que crecen en tiempo real (logs, colas de salida, etc.). Abajo tienes una guía completa, con sintaxis, opciones importantes, ejemplos prácticos, trucos para scripts y solución de problemas.

### 1) Sintaxis básica

```
tail [OPCIONES] [ARCHIVO...]
```

Si no pasas `ARCHIVO`, `tail` lee de la entrada estándar (stdin).

### 2) Opciones importantes (resumen)

- `-n NUM`, `--lines=NUM` — mostrar las últimas `NUM` **líneas**. (por defecto `NUM=10`)
- `-n +NUM` — empezar a mostrar **desde** la línea `NUM` (incluida).
- `-c BYTES`, `--bytes=BYTES` — mostrar los últimos `BYTES` bytes.
- `-c +BYTES` — mostrar desde el byte `BYTES` en adelante.
- `-f`, `--follow[=OPTION]` — seguir el archivo (mantenerse leyendo nuevas líneas). `OPTION` puede ser `descriptor` o `name`.
- `-F` — equivalente a `--follow=name --retry` (muy útil con rotación de logs).
- `--retry` — reintentar abrir el archivo si no existe / desaparece.
- `--pid=PID` — con `-f`, terminar `tail` automáticamente cuando `PID` deje de existir.
- `-q`, `--quiet` — no imprimir encabezados de archivo múltiples.
- `-v`, `--verbose` — siempre imprimir encabezado con el nombre del archivo.
- `-s SECS`, `--sleep-interval=SECS` — intervalo (segundos) entre comprobaciones cuando se sigue (-f); por defecto \~1s.
- `--help`, `--version` — ayuda / versión.

> Nota: algunas opciones (`--pid`, `--retry`, `--follow=name`) son de GNU `tail` (Linux). En BSD/macOS la mayoría están, pero los nombres pueden variar; revisa `man tail` si trabajas en macOS.

### 3) Ejemplos básicos

Mostrar últimas 10 líneas (por defecto):

```bash
tail /var/log/syslog
```

Mostrar últimas 50 líneas:

```bash
tail -n 50 /var/log/syslog
```

Mostrar desde la línea 100 en adelante:

```bash
tail -n +100 archivo.txt
```

Mostrar últimos 1024 bytes:

```bash
tail -c 1024 archivo.bin
```

Mostrar desde el byte 100 (inclusive):

```bash
tail -c +100 archivo.bin
```

Leer desde stdin (ej. tomar la última línea de un pipe):

```bash
echo -e "uno\ndos\ntres" | tail -n 1
# salida: tres
```

### 4) Monitorizar (seguir) archivos en tiempo real

Seguir nuevas entradas del log en tiempo real:

```bash
tail -f /var/log/nginx/access.log
```

Seguir y **reconectarse** cuando el archivo es rotado (logrotate):

```bash
tail -F /var/log/nginx/access.log
# equivalente a: tail --follow=name --retry /var/log/nginx/access.log
```

Seguir sólo las **líneas nuevas** (útil si no quieres ver el contenido preexistente):

```bash
tail -n 0 -f /var/log/app.log
```

Seguir y detener automáticamente cuando un proceso muere:

```bash
tail --pid=12345 -f /var/log/app.log
```

Cambiar el intervalo de sondeo (reducir carga de I/O):

```bash
tail -f -s 5 /var/log/app.log
# comprobará cada 5 segundos
```

### 5) `-f` vs `-F` y rotación de logs — explicación práctica

- `tail -f` sigue el **descriptor** abierto: si el archivo es renombrado (rotado) y se crea uno nuevo con el mismo nombre, `tail -f` **seguirá el archivo antiguo** (ya renombrado).
- `tail -F` (o `--follow=name --retry`) sigue por **nombre** y reabrirá el nuevo archivo cuando aparezca — es la opción recomendada para monitorizar logs en producción.

Ejemplo:

```bash
# si logrotate renombra access.log -> access.log.1 y crea nuevo access.log:
tail -f access.log    # seguirá access.log.1 (archivo ya abierto)
tail -F access.log    # seguirá el nuevo access.log creado tras rotación
```

### 6) Trabajar `tail` en pipelines y con filtros (ejemplos útiles)

Buscar en tiempo real líneas que contengan `ERROR` (y que `grep` no bufferice):

```bash
tail -F /var/log/app.log | grep --line-buffered 'ERROR'
```

Sin `--line-buffered`, `grep` puede agrupar salida y no mostrar instantáneamente.

Agregar timestamp a cada línea nueva (con `awk` o `gawk`):

```bash
tail -F /var/log/app.log | awk '{ print strftime("%F %T"), $0 }'
# usar gawk para soporte strftime; imprime fecha actual a cada línea
```

Procesar cada línea en un script:

```bash
tail -n 0 -f /var/log/app.log | while IFS= read -r line; do
  # procesar $line
  echo "Procesando: $line"
done
```

Contar sólo las últimas N coincidencias de un patrón (ej.: últimas 10 líneas con "ERROR"):

```bash
grep -n 'ERROR' /var/log/app.log | tail -n 10
```

(o usar herramientas más avanzadas si necesitas eficiencia en archivos enormes).

### 7) `tail` con múltiples archivos

Puedes pedir varios archivos:

```bash
tail -n 20 /var/log/auth.log /var/log/syslog
```

Cuando pasas más de un archivo, `tail` imprime encabezados:

```
==> /var/log/auth.log <==
(lines...)

==> /var/log/syslog <==
(lines...)
```

Usa `-q` para suprimir esos encabezados si lo prefieres.

Puedes además seguir varios archivos a la vez:

```bash
tail -f file1 file2 file3
```

### 8) `tail` remoto (via SSH)

Para monitorizar logs en una máquina remota:

```bash
ssh user@server 'tail -F /var/log/nginx/access.log'
```

Si quieres procesar localmente:

```bash
ssh user@server 'tail -F /var/log/nginx/access.log' | grep --line-buffered ' 500 '
```

### 9) Usos prácticos y patrones comunes

- Ver errores recientes en un log:

```bash
tail -n 200 /var/log/nginx/error.log | grep -i 'error'
```

- Seguir logs del servicio mientras se reinicia:

```bash
# en una terminal:
tail --pid=$(pgrep -n myservice) -f /var/log/myservice.log
# reinicia myservice y tail terminará cuando myservice muera
```

- Solo ver líneas añadidas después de conectar:

```bash
tail -n 0 -f /var/log/mail.log
```

- Extraer las últimas N líneas y guardarlas:

```bash
tail -n 100 /var/log/syslog > ultimas_100.log
```

### 10) Problemas comunes y cómo solucionarlos

- **No se ve nueva salida tras rotación**: usa `tail -F` (o `--follow=name --retry`).
- **`grep` no muestra coincidencias en tiempo real**: usar `--line-buffered` o `stdbuf -oL grep ...`.
- **Necesitas pausa/retroceso**: usa `less +F archivo` — actúa como `tail -f` pero te permite retroceder (Ctrl+C para salir del modo seguimiento).
- **Muchos archivos grandes**: `tail -f` en cientos de archivos puede cargar IO; considera herramientas específicas (multitail/lnav) o filtrar en el servidor.
- **Permisos**: si `tail` no puede leer un log por permisos, usa `sudo` o ajusta permisos/roles (ej: `sudo tail -f /var/log/syslog`).

### 11) Alternativas y complementos

- `less +F` — seguimiento interactivo con posibilidad de retroceder.
- `multitail` — ver varios logs en ventanas con colores.
- `lnav` — visor de logs con indexado y búsqueda.
- `journalctl -f` — para sistemas con systemd, ver logs del journal.
- `tail -F` es recomendado para scripts que deben sobrevivir a rotación de logs.

### 12) Cheat-sheet rápido (comandos útiles)

```bash
tail file                       # últimas 10 líneas
tail -n 50 file                 # últimas 50 líneas
tail -n +1 file                 # desde la línea 1 (todo)
tail -c 1024 file               # últimos 1024 bytes
tail -c +100 file               # desde el byte 100
tail -n 0 -f file               # seguir sólo nuevas líneas
tail -f file                    # seguir y mostrar últimas + nuevas líneas
tail -F file                    # seguir y reabrir tras rotación (recomendado)
tail --pid=1234 -f file         # seguir hasta que el PID 1234 muera
tail -f file | grep --line-buffered PATTERN
ssh host 'tail -F /var/log/foo' # monitor remoto
tail file1 file2                # varias fuentes, con encabezados
tail -q file1 file2             # igual sin encabezados
```

### 13) Ejemplo final práctico (monitorización con anotación de tiempo)

Quiero monitorizar un log y que cada línea nueva tenga timestamp local:

```bash
tail -n 0 -F /var/log/app.log | while IFS= read -r line; do
  printf '%s %s\n' "$(date '+%F %T')" "$line"
done
```

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**        | **Siguiente 8**       |
| ------------------ | ------------------ | --------------------- |
| [🏠](../README.md) | [⏪](./13_6_dd.md) | [⏩](./13_8_hping.md) |
