| **Inicio**         | **atrás 8**           | **Siguiente 10**      |
| ------------------ | --------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./13_8_hping.md) | [⏩](./13_10_grep.md) |

---

## **Índice**

| Temario                                                                          |
| -------------------------------------------------------------------------------- |
| [428. Los comandos Head y Tail en Linux](#428-los-comandos-head-y-tail-en-linux) |
| [429. Head and Tail comandos](#429-head-and-tail-comandos)                       |

# **head**

## **428. Los comandos Head y Tail en Linux**

![head](/img/13_Tools_for_Incident_Response_and_Discovery/head.png "head")

### 1. **Descripción general**

En Linux, los comandos **`head`** y **`tail`** se utilizan para **visualizar partes específicas del contenido de archivos de texto** directamente en la terminal.

- **`head`** → muestra las **primeras líneas** de un archivo.
- **`tail`** → muestra las **últimas líneas** de un archivo.

Son muy útiles cuando trabajas con archivos largos (por ejemplo, registros/logs del sistema, configuraciones o datos grandes), ya que te permiten inspeccionar rápidamente solo el inicio o el final sin abrir todo el archivo.

### 2. **Introducción a los comandos de cabeza y cola**

Ambos comandos siguen la filosofía de Unix: _hacer una cosa y hacerla bien_.

- Funcionan por defecto mostrando **10 líneas** (puedes cambiar esa cantidad con `-n`).
- Admiten opciones como mostrar cierto número de bytes (`-c`), seguir en tiempo real (`tail -f`) o combinarse con otros comandos (`grep`, `cat`, etc.) en **tuberías (`|`)**.

Ejemplo simple:

```bash
head archivo.txt     # Muestra las primeras 10 líneas
tail archivo.txt     # Muestra las últimas 10 líneas
```

### 3. **El comando de la cabeza (`head`)**

#### Sintaxis:

```bash
head [opciones] [archivo]
```

#### Ejemplos:

- **Mostrar las primeras 10 líneas (por defecto):**

```bash
head notas.txt
```

- **Mostrar las primeras 5 líneas:**

```bash
head -n 5 notas.txt
```

- **Mostrar los primeros 100 bytes:**

```bash
head -c 100 notas.txt
```

- **Usar múltiples archivos:**

```bash
head archivo1.txt archivo2.txt
```

👉 `head` indica el nombre de cada archivo antes del contenido para diferenciarlos.

### 4. **El comando de la cola (`tail`)**

#### Sintaxis:

```bash
tail [opciones] [archivo]
```

#### Ejemplos:

- **Mostrar las últimas 10 líneas (por defecto):**

```bash
tail notas.txt
```

- **Mostrar las últimas 20 líneas:**

```bash
tail -n 20 notas.txt
```

- **Mostrar los últimos 200 bytes:**

```bash
tail -c 200 notas.txt
```

- **Seguir un archivo en tiempo real (muy usado en logs):**

```bash
tail -f /var/log/syslog
```

👉 Aquí, `tail` se queda "escuchando" y muestra nuevas líneas conforme se agregan al archivo.

- **Seguir pero mostrando las últimas 50 líneas:**

```bash
tail -n 50 -f /var/log/auth.log
```

### 5. **Usa la cabeza y la cola juntas**

A veces quieres mostrar un rango específico de líneas (no solo el inicio o el final). Puedes lograrlo combinando `head` y `tail`.

#### Ejemplo:

- Mostrar de la **línea 11 a la 20**:

```bash
head -n 20 archivo.txt | tail -n 10
```

👉 Explicación:

- `head -n 20` → muestra las primeras 20 líneas.
- `tail -n 10` → de esas, muestra solo las últimas 10 (es decir, de la 11 a la 20).

Otro ejemplo:

- Mostrar líneas de la 51 a la 60:

```bash
head -n 60 archivo.txt | tail -n 10
```

### 6. **Conclusión**

- **`head`** y **`tail`** son comandos sencillos pero muy poderosos para inspeccionar rápidamente archivos de texto grandes.
- `head` = primeras líneas, `tail` = últimas líneas.
- `tail -f` es especialmente útil en **monitorización de logs en tiempo real**.
- Al combinarlos (`head | tail`), puedes extraer un rango de líneas específicas.
- Son esenciales para administradores de sistemas, desarrolladores y analistas que trabajan con grandes volúmenes de datos.

---

[🔼](#índice)

---

## **429. Head and Tail comandos**

`head` y `tail` son dos comandos básicos y extremadamente útiles en Unix/Linux (y disponibles en macOS). Permiten inspeccionar **el principio** (`head`) y **el final** (`tail`) de archivos de texto, y combinados son muy potentes para depuración, monitorización y procesamiento rápido de logs o datos.

### 1) Concepto rápido

- `head` → muestra las primeras líneas (o bytes) de uno o más archivos.
- `tail` → muestra las últimas líneas (o bytes) y puede **seguir** un archivo en tiempo real (`-f`) para ver nuevas entradas (ideal para logs).

Por defecto **ambos** muestran las últimas/primeras **10 líneas** si no indicas otra cantidad.

### 2) Sintaxis básica

```
head [OPCIONES] [ARCHIVO...]
tail [OPCIONES] [ARCHIVO...]
```

Si no das archivos, leen desde la entrada estándar (stdin), por ejemplo `somecommand | head`.

### 3) Opciones comunes y qué hacen

#### `head`

- `-n N` o `--lines=N` → mostrar primeras `N` líneas.

  ```bash
  head -n 5 archivo.txt   # primeras 5 líneas
  ```

- `-c N` o `--bytes=N` → mostrar los primeros `N` bytes.

  ```bash
  head -c 100 archivo.bin  # primeros 100 bytes
  ```

- `-q` → suprime encabezados cuando hay varios archivos.
- `-v` → fuerza impresión del encabezado con el nombre del archivo.

#### `tail`

- `-n N` → mostrar últimas `N` líneas.

  ```bash
  tail -n 20 archivo.log   # últimas 20 líneas
  ```

- `-n +N` → mostrar desde la línea `N` en adelante (útil para “desde la línea X”).

  ```bash
  tail -n +100 archivo.txt  # desde la línea 100 hasta el final
  ```

- `-c N` → últimos N bytes.
- `-f` (follow) → seguir el archivo en tiempo real; muestra nuevas líneas a medida que se agregan.

  ```bash
  tail -f /var/log/syslog
  ```

- `-F` → `--follow=name --retry`, reabre el archivo si es rotado (útil con logrotate).
- `--pid=PID` → con `-f`, termina cuando muere el proceso `PID` (GNU tail).
- `-s SECS` → intervalo de sondeo entre comprobaciones (cuando se usa `-f`).

> Nota: opciones disponibles pueden variar ligeramente entre GNU (Linux) y BSD/macOS. En Windows PowerShell: `Get-Content -Tail 10` y `Get-Content -Wait` equivalen a `tail`.

### 4) Ejemplos prácticos con salida simulada

**Mostrar primeras 6 líneas**

```bash
head -n 6 archivo.txt
```

Salida (ejemplo):

```
Línea 1
Línea 2
Línea 3
Línea 4
Línea 5
Línea 6
```

**Mostrar últimas 5 líneas**

```bash
tail -n 5 archivo.log
```

Salida:

```
2025-09-15 10:02:34 ERROR Connection timeout
2025-09-15 10:03:01 INFO Restarting service
2025-09-15 10:03:05 INFO Service started
2025-09-15 10:03:10 WARN Slow response
2025-09-15 10:03:20 INFO OK
```

**Seguir un log en tiempo real**

```bash
tail -n 0 -f /var/log/nginx/access.log
```

`-n 0` evita imprimir líneas antiguas y solo muestra las nuevas. Detener con `Ctrl+C`.

**Reabrir tras rotación (logrotate)**

```bash
tail -F /var/log/nginx/access.log
```

### 5) Usarlos juntos / extraer rangos

`head` + `tail` se usan para extraer un rango de líneas:

- Obtener **líneas 11–20**:

```bash
head -n 20 archivo.txt | tail -n 10
```

Explicación: `head -n 20` deja las primeras 20 líneas; `tail -n 10` toma las últimas 10 de esas → líneas 11..20.

Alternativas con `sed` o `awk` (más directas para rangos grandes):

```bash
sed -n '11,20p' archivo.txt
awk 'NR>=11 && NR<=20' archivo.txt
```

### 6) Uso en pipes y con otras herramientas

- Contar ocurrencias de un patrón en las últimas 200 líneas:

```bash
tail -n 200 archivo.log | grep -c 'ERROR'
```

- Monitorizar y colorear coincidencias en tiempo real:

```bash
tail -F /var/log/app.log | grep --line-buffered --color=always 'ERROR'
```

(`--line-buffered` evita que `grep` acumule salida; útil en pipelines)

- Añadir timestamp a cada nueva línea (en tiempo real):

```bash
tail -n 0 -F /var/log/app.log | while IFS= read -r line; do
  printf '%s %s\n' "$(date '+%F %T')" "$line"
done
```

### 7) Diferencias con herramientas relacionadas

- `less +F` — modo seguimiento interactivo (puedes retroceder).
- `tac` — imprime el archivo en orden inverso (contrario a `cat`).
- `head`/`tail` muestran por líneas o bytes; para selección compleja usa `sed`/`awk`.
- En systemd: `journalctl -f` es la alternativa para seguir logs del journal.

### 8) Windows (PowerShell) — equivalentes rápidos

- Últimas 10 líneas:

```powershell
Get-Content -Path .\archivo.log -Tail 10
```

- Seguir (esperar nuevas líneas):

```powershell
Get-Content -Path .\archivo.log -Wait
```

### 9) Buenas prácticas y consejos

- Para monitorización en producción usa `tail -F` (maneja rotación).
- Si vas a filtrar en tiempo real, añade `--line-buffered` a `grep` o usa `stdbuf -oL`.
- No uses `cat archivo | head` innecesariamente; ejecuta `head archivo` directamente (evita proceso extra).
- Para extraer rangos muy grandes o cuando el archivo no cabe en memoria, `sed`/`awk` son más eficientes que combinaciones de `head`+`tail`.
- Para scripts, usa `--pid` (GNU) con `tail -f` si quieres que termine junto con el proceso que produces.

### 10) Mini-cheat-sheet (rápido)

- `head file` — primeras 10 líneas.
- `head -n 5 file` — primeras 5 líneas.
- `head -c 100 file` — primeros 100 bytes.
- `tail file` — últimas 10 líneas.
- `tail -n 50 file` — últimas 50 líneas.
- `tail -n +100 file` — desde línea 100 hasta el final.
- `tail -f file` — seguir archivo en tiempo real.
- `tail -F file` — seguir y reabrir si rota.
- `head -n 20 file | tail -n 10` — líneas 11..20.
- `tac file` — archivo en orden inverso.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 8**           | **Siguiente 10**      |
| ------------------ | --------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./13_8_hping.md) | [⏩](./13_10_grep.md) |
