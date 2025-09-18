| **Inicio**         | **atrás 6**                                       | **Siguiente 8**                       |
| ------------------ | ------------------------------------------------- | ------------------------------------- |
| [🏠](../README.md) | [⏪](./2_6_Different_Versions_and_Differences.md) | [⏩](./2_8_Understand_Permissions.md) |

---

## **Índice**

| Temario                                                                          |
| -------------------------------------------------------------------------------- |
| [50. Interfaz gráfica de usuario (GUI)](#50-interfaz-gráfica-de-usuario-gui)     |
| [51. Interfaz de línea de comandos (CLI)](#51-interfaz-de-línea-de-comandos-cli) |

---

# **Navegación mediante GUI y CLI**

## **50. Interfaz gráfica de usuario (GUI)**

Una **Interfaz Gráfica de Usuario (GUI)** es la capa visual y manipulable que permite a las personas interactuar con un sistema (aplicación, SO, web app) mediante elementos gráficos —ventanas, botones, menús, formularios— en lugar de hacerlo por línea de comandos. Su objetivo es traducir operaciones complejas en acciones simples, comprensibles y accesibles para el usuario.

### Qué compone una GUI (elementos básicos)

- **Ventana (window)**: contenedor principal que puede tener título, barra de herramientas y bordes.
- **Controles / widgets**: botones, campos de texto, checkboxes, radio buttons, listas, menús, sliders, tooltips.
- **Diálogos**: modales (bloquean) o modeless (no bloquean) para interacción puntual.
- **Barra de menú / toolbar / status bar**: acciones globales y estado.
- **Canvas / área de dibujo**: para gráficos, mapas o canvases personalizados.
- **Layout managers**: sistemas que organizan widgets en filas, columnas o rejillas.
- **Eventos e input**: ratón, teclado, touch, gestos, voz.
- **Skin / theme**: estilo visual que define colores, iconos y tipografías.

### Cómo funciona por dentro (arquitectura básica)

1. **Event loop (bucle de eventos)**: la GUI corre un loop que recibe eventos (clicks, teclas) y los despacha a los widgets correspondientes.
2. **Message queue**: eventos entrantes se encolan; el loop los procesa uno a uno.
3. **Render / repaint**: cuando cambia el estado, el widget pide redraw; el compositor dibuja la escena (a veces con GPU).
4. **Compositor / GPU**: en GUIs modernas un compositor gestiona capas y acelera dibujos.
5. **Threading**: la UI suele vivir en el hilo principal; operaciones largas deben ejecutarse en hilos/async para no bloquear la interfaz.

### Patrones de diseño comunes

- **MVC (Model-View-Controller)**: separación de datos (Model), vista (View) y lógica de interacción (Controller).
- **MVVM (Model-View-ViewModel)**: muy usado en WPF/Qt/QML; facilita binding de datos.
- **Observer / Publish-Subscribe**: widgets escuchan cambios del modelo.
- **Command pattern**: encapsula acciones (útil para undo/redo, menús).

### Principios de usabilidad (UX) a seguir

- **Consistencia** (mismos comportamientos en toda la app).
- **Feedback** inmediato (animaciones, loaders, mensajes).
- **Minimizar carga cognitiva**: mostrar sólo lo necesario.
- **Accesibilidad**: teclado, lectores de pantalla, contraste, tamaño de targets.
- **Affordance**: los elementos deben “sugerir” cómo se usan.
- **Leyes útiles**: _Fitts_ (targets grandes y cercanos), _Hick_ (menos opciones → decisiones más rápidas).

### Toolkits y plataformas populares

- **Web (GUI en navegador):** HTML/CSS/JS (+ frameworks: React, Vue, Angular).
- **Windows:** Win32 / WinUI / WPF.
- **macOS / iOS:** Cocoa / AppKit / UIKit / SwiftUI.
- **Linux (escritorio):** GTK (GNOME), Qt (KDE).
- **Cross-platform:** Qt, Electron (web + Node), Flutter, JavaFX/Swing.
- **Mobile:** Android (Jetpack Compose / XML) y iOS (SwiftUI/UIKit).

### Ejemplos prácticos (código)

#### 1) GUI web simple (HTML + JavaScript)

Botón que incrementa un contador y muestra feedback.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Contador simple</title>
    <style>
      body {
        font-family: sans-serif;
        padding: 2rem;
      }
      button {
        font-size: 1rem;
        padding: 0.5rem 1rem;
      }
    </style>
  </head>
  <body>
    <h1>Contador</h1>
    <div>
      <button id="btn">Haz click</button>
      <span id="count">0</span>
    </div>

    <script>
      const btn = document.getElementById("btn");
      const countEl = document.getElementById("count");
      let count = 0;
      btn.addEventListener("click", () => {
        count++;
        countEl.textContent = count;
        // feedback visual rápido
        btn.animate(
          [
            { transform: "scale(1.0)" },
            { transform: "scale(0.95)" },
            { transform: "scale(1.0)" },
          ],
          { duration: 120 }
        );
      });
    </script>
  </body>
</html>
```

#### 2) GUI de escritorio con Python + Tkinter (muy básico)

Ventana con etiqueta y botón que actualiza texto.

```python
import tkinter as tk

def incrementar():
    global contador
    contador += 1
    label.config(text=f"Contador: {contador}")

root = tk.Tk()
root.title("Ejemplo Tkinter")
contador = 0

label = tk.Label(root, text="Contador: 0", font=("Arial", 16))
label.pack(padx=20, pady=10)

btn = tk.Button(root, text="Incrementar", command=incrementar)
btn.pack(padx=20, pady=10)

root.mainloop()
```

> Ambos ejemplos muestran el patrón básico: widgets + binding de eventos → lógica que actualiza modelo → actualización de la vista.

### Diseño, prototipado y testing

- **Prototipado:** wireframes → mockups → prototipo interactivo (Figma, Sketch, Adobe XD).
- **Pruebas de usabilidad:** pruebas con usuarios reales, sesiones moderadas, tareas concretas.
- **Testing automatizado:**

  - _Web_: Selenium, Playwright, Cypress (interacción y regresión visual).
  - _Desktop_: Squish, WinAppDriver, XCTest (macOS).
  - _Visual regression_: Percy, Chromatic.

- **Unit tests** para la lógica; **end-to-end** para flujos completos.

### Accesibilidad (A11y) — imprescindible

- **ARIA (web)**: roles, labels y estados para lectores de pantalla.
- **Focus management**: tab order coherente, visible focus.
- **Keyboard shortcuts**: proporcionar alternativas de teclado.
- **Contrast & font sizes**: respetar WCAG AA/AAA cuando aplique.
- **APIs OS**: UI Automation (Windows), Accessibility API (macOS), AT-SPI (Linux).

**Ejemplo ARIA básico (web):**

```html
<button aria-pressed="false" id="toggle">Toggle</button>
```

### Rendimiento y recursos

- **Evitar bloquear hilo UI**: mover cálculos intensos a workers / hilos.
- **Batching de renders**: agrupar cambios para evitar repaints constantes.
- **Lazy loading**: cargar componentes/recursos según necesidad.
- **DPI / High-DPI**: usar assets vectoriales o @2x para pantallas retina.
- **GPU acceleration**: aprovechar compositing y CSS transforms cuando sea posible.

### Seguridad en GUIs

- **Web:** proteger contra XSS (sanitizar inputs), CSRF, clickjacking (X-Frame-Options).
- **Desktop:** validar archivos que la app abra, restringir permisos, firmar code (macOS/Windows) y usar sandbox cuando sea posible.
- **Electron:** evitar `eval`, usar contextIsolation y Content Security Policy; no ejecutar código remoto sin validación.

### Buenas prácticas de desarrollo y diseño

- **Separar UI y lógica** (p. ej. ViewModel o controllers).
- **Diseñar para errores**: mensajes útiles y acciones de recuperación.
- **Soporte para internacionalización (i18n)**: no hardcodear strings, manejar formatos de fecha/número.
- **Atajos y discoverability**: mostrar atajos en menús, permitir ayuda contextual.
- **Consistencia visual**: usar design system (component library) y tokens de diseño.

### Checklist rápido para evaluar una GUI

- ¿Las acciones principales están visibles y accesibles?
- ¿La navegación es clara y consistente?
- ¿La app responde (<100–300 ms) a interacciones básicas?
- ¿Se puede usar todo con teclado?
- ¿Los colores cumplen contraste mínimo?
- ¿Los errores muestran soluciones, no solo códigos?
- ¿Se pueden probar automáticamente los flujos críticos?
- ¿Se consideró rendimiento en móviles/tabletas/retina?

### Cuándo elegir cada tipo de GUI / plataforma

- **Web**: máxima portabilidad y despliegue rápido; excelente para apps de negocio, contenido y SaaS.
- **Nativo (Win/mac/Linux)**: integración profunda con OS, mejor performance (UX nativo), acceso a APIs específicas.
- **Cross-platform (Electron/Flutter/Qt)**: balance entre desarrollo único y experiencia nativa; cuidado con peso y consumo.
- **Mobile**: touch-first, gestos y consideraciones de energía/conectividad.

---

[🔼](#índice)

---

## **51. Interfaz de línea de comandos (CLI)**

La **Interfaz de línea de comandos (CLI)** es la forma de interactuar con un sistema (SO, herramientas, servidores) mediante texto: escribes órdenes, el sistema las ejecuta y devuelve salida. Es extremadamente poderosa para administración, automatización, diagnóstico y desarrollo porque permite encadenar herramientas pequeñas (philosophy: _do one thing and do it well_), automatizar tareas y trabajar en entornos sin GUI (servidores, contenedores, SSH).

### ¿Qué compone una CLI y cómo funciona?

- **Shell**: programa que interpreta lo que escribes (ej.: `bash`, `zsh`, `sh`, `fish`, `powershell`, `cmd.exe`).
- **Comandos**: binarios o funciones integradas del shell (`ls`, `grep`, `Get-Process`).
- **Argumentos y opciones**: forman la interfaz del comando (`ls -la /etc`).
- **STDIN / STDOUT / STDERR**: tres canales básicos — entrada, salida y errores.
- **Pipes & redirecciones**: permiten conectar comandos (`|`, `>`, `2>`, `&>`).
- **Variables de entorno**: configuran comportamiento (`PATH`, `HOME`, `EDITOR`).
- **Scripts**: ficheros con secuencia de comandos ejecutables (automatización).
- **Bucle de eventos**: el shell lee, parsea y ejecuta.

### Shells populares (qué elegir y por qué)

- **Bash**: estándar en Linux; gran compatibilidad y abundante documentación.
- **Zsh**: interactivo más agradable (plugins, autocompletado, oh-my-zsh).
- **Fish**: enfoque en experiencia interactiva (sin POSIX completo).
- **PowerShell**: objeto-pipeline (Windows + multiplataforma), más seguro para administración Windows.
- **sh / dash**: POSIX shell minimalista (scripts portables).

### Conceptos clave con ejemplos

#### 1) Navegar y listar

```bash
pwd                # print working directory
ls -la /etc        # listar con permisos y archivos ocultos
cd /var/log        # cambiar directorio
```

#### 2) Redirecciones básicas

```bash
echo "hola" > archivo.txt      # sobrescribe archivo
echo "más" >> archivo.txt      # añade al final
command 2> error.log           # STDERR a archivo
command &> salida_y_error.log  # STDOUT+STDERR juntos (bash)
```

#### 3) Pipes (encadenar comandos)

```bash
cat /var/log/syslog | grep -i error | awk '{print $1,$2,$3,$0}' | less
# lee syslog → filtra "error" → formatea → pagina
```

#### 4) Variables y uso

```bash
export MYDIR="/srv/backups"
mkdir -p "$MYDIR/$(date +%F)"
echo "backup hecho el $(date)" > "$MYDIR/$(date +%F)/log.txt"
```

#### 5) Estado de salida y control de flujo

- `$?` contiene el exit code del último comando (0 = éxito).
- `&&` ejecuta el segundo solo si el primero tuvo éxito.
- `||` ejecuta el segundo si el primero falló.

```bash
make && sudo make install   # instala sólo si make tuvo éxito
test -f file || echo "No existe"
```

### Filtrado y procesamiento de texto (herramientas indispensables)

- `grep` (buscar texto), `sed` (edición en flujo), `awk` (procesamiento por columnas), `cut`, `sort`, `uniq`, `tr`.

Ejemplo: contar 20 IPs más frecuentes en un log web:

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -n 20
```

Reemplazo inline con `sed`:

```bash
sed -i 's/old_domain/new_domain/g' /etc/nginx/sites-available/*.conf
```

`jq` para JSON:

```bash
cat data.json | jq '.items[] | {id: .id, name: .name}'
```

### Scripting: buenas prácticas y ejemplos

#### Shebang y robustez

Comienza scripts con `shebang` y opciones seguras:

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'
```

- `set -e` → sale si un comando falla.
- `set -u` → error si usas variable no definida.
- `set -o pipefail` → fallo si cualquiera en la pipe falla.

#### Ejemplo: script de backup simple (`backup.sh`)

```bash
#!/usr/bin/env bash
set -euo pipefail
IFS=$'\n\t'

DEST="/backups/$(date +%F)"
SRC="/var/www"
mkdir -p "$DEST"
tar -czf "$DEST/www_$(date +%FT%T).tar.gz" -C "$SRC" .
find /backups -type f -mtime +30 -delete   # borra backups >30d
echo "Backup completado: $DEST"
```

Haz ejecutable `chmod +x backup.sh` y programa con `crontab` o `systemd timer`.

#### Argumentos y `getopts` (parsing)

```bash
#!/usr/bin/env bash
while getopts "s:d:h" opt; do
  case $opt in
    s) SRC="$OPTARG";;
    d) DEST="$OPTARG";;
    h) echo "usage: $0 -s src -d dest"; exit 0;;
    *) echo "Invalid"; exit 1;;
  esac
done
echo "Fuente: $SRC, Destino: $DEST"
```

### Trabajo remoto y SSH (uso cotidiano)

Ejecutar comando remoto:

```bash
ssh user@host 'sudo systemctl restart nginx && tail -n 200 /var/log/nginx/error.log'
```

Copiar archivos:

```bash
scp archivo user@host:/tmp/
rsync -avz ./folder/ user@host:/srv/www/     # más eficiente y resume
```

Túnel SSH (acceso a DB local desde tu laptop):

```bash
ssh -L 5432:127.0.0.1:5432 user@db-host
# ahora en localhost:5432 accedes a DB remota de forma segura
```

### Herramientas interactivas y TUI (text user interfaces)

- `tmux` / `screen` → multiplexar terminales, persistencia de sesiones.
- `htop` → monitor interactivo de procesos.
- `ncdu` → visualizar uso de disco en consola.
- `fzf` → selector fuzzy (integrable con historial, git, find).
- `tig` → interfaz curses para git.
- `ranger` → file manager en terminal.

Ejemplo rápido `fzf` con historial:

```bash
history | fzf
```

### Comandos de gestión de procesos y recursos

- `ps aux | grep myapp`
- `top` / `htop`
- `nice` / `renice` para prioridades
- `kill PID`, `pkill -f pattern`
- `nohup command &` para descolar procesos
- `disown` para desligarlos de shell

Obtener top procesos por memoria:

```bash
ps aux --sort=-%mem | head -n 10
```

### Redirecciones avanzadas, subshells y process substitution

- **Here-doc** (multilínea):

```bash
cat <<'EOF' > /tmp/config.conf
line1
line2
EOF
```

- **Here-string**:

```bash
grep 'pattern' <<< "$variable"
```

- **Process substitution** (bash/zsh):

```bash
diff <(sort file1) <(sort file2)
```

- **Subshell**:

```bash
( cd /tmp && tar -czf /tmp/pack.tgz . )
# cambios de directorio no afectan al shell principal
```

### Paralelismo y `xargs` / GNU parallel

`xargs` es útil para transformar entrada en argumentos y ejecutar comandos en paralelo:

```bash
# comprimir archivos .log en paralelo (evita problemas con espacios)
find . -name '*.log' -print0 | xargs -0 -P 4 -I{} gzip "{}"
```

`-P` = paralelismo. Para trabajos más complejos, usar `parallel`.

### PowerShell (Windows / multiplataforma) — diferencias clave

- PowerShell pasa **objetos**, no texto; pipeline transporta objetos rich.
- Cmdlets tienen verb-noun: `Get-Process`, `Get-ChildItem` (equivalente a `ls`).
- Ejemplo PowerShell:

```powershell
Get-Process | Where-Object {$_.CPU -gt 100} | Sort-Object CPU -Descending
Get-ChildItem -Path C:\Logs -Recurse | Select-String "ERROR" | Group-Object Path | Sort-Object Count -Descending
```

PowerShell scripting usa `param(...)` y es muy potente para administración de Windows y Azure.

### Depuración y pruebas de scripts

- `bash -n script.sh` → sintaxis check.
- `set -x` o ejecutar con `bash -x script.sh` → muestra comandos conforme se ejecutan.
- `shellcheck` → linter para scripts shell (muy recomendado).
- Logs: escribe salidas en fichero y rota logs (`logger`, `syslog`).
- Tests: escribe pruebas unitarias o mocks (para bash, `bats`).

### Seguridad y buenas prácticas (imperativas)

1. **No pegar** sin leer `curl ... | sh`.
2. **Probar comandos destructivos** en entorno local/VM antes de prod (especialmente `rm`, `dd`).
3. **Quoting**: usa `"$var"` para evitar word splitting/expansión inesperada.
4. **Principio least privilege**: evita trabajar como root si no es necesario.
5. **Manejo de secretos**: no guardes passwords en scripts sin protección; usa vaults o variables de entorno en runners seguros.
6. **SSH keys** con passphrase y agente (`ssh-agent`).
7. **Registro y monitoreo** de scripts críticos y runs (auditoría).

Ejemplo de trap para limpiar en fallo:

```bash
trap 'echo "Error en la línea $LINENO"; cleanup_function' ERR
```

### Diseño de CLIs (si desarrollas una herramienta)

- Ofrece `--help` y `--version`.
- Usa subcommands claros: `mycli deploy start|stop|status`.
- Error codes significativos (0 OK, >0 error).
- Soporta `--dry-run` y logs verbosos `--verbose`/`-v`.
- Usa librerías de argumentos (Python: `argparse` / `click`; Go: `cobra`; Node: `yargs`).

Ejemplo básico en Python con `argparse`:

```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument('--port', type=int, default=8080)
args = parser.parse_args()
print("Server port:", args.port)
```

### Automatización: cron y systemd timers

**Cron (ejemplo diario a las 2am):**

```cron
0 2 * * * /usr/local/bin/backup.sh >> /var/log/backup.log 2>&1
```

**systemd timer** (más moderno; persists and handles missed runs):

- `mi-job.service` + `mi-job.timer` — puedes usar `OnCalendar` y `Persistent=true`.

### Ejemplos prácticos finales — mini-recetas

#### A) Buscar y reemplazar en muchos archivos (seguros)

```bash
# preview
grep -RIl "OLD_DOMAIN" ./ | xargs -I{} sed -n '1,10p' {}
# replace (después de comprobar)
grep -RIl "OLD_DOMAIN" ./ | xargs sed -i 's/OLD_DOMAIN/NEW_DOMAIN/g'
```

#### B) Deploy simple con rsync + restart service

```bash
rsync -avz --delete ./build/ user@web:/var/www/myapp/
ssh user@web 'sudo systemctl restart myapp.service && sudo journalctl -u myapp.service -n 200'
```

#### C) Reporte de errores recientes en syslog

```bash
grep -i 'error' /var/log/syslog | tail -n 100 > /tmp/errors_$(date +%F).log
```

### Cheatsheet rápido (comandos esenciales)

`ls, cd, pwd, cp, mv, rm, mkdir, chmod, chown, cat, less, head, tail, grep, find, awk, sed, sort, uniq, xargs, tar, gzip, rsync, ssh, scp, systemctl, journalctl, crontab, top, htop, ps, kill, tmux, screen, jq, fzf, curl, wget, docker, git`

### Resumen — por qué dominar la CLI

- **Eficiencia**: muchas tareas se hacen mucho más rápido que por GUI.
- **Automatización**: repetición fiable sin error humano.
- **Acceso remoto**: administración de servidores sin interfaz gráfica.
- **Composición**: pequeñas herramientas encadenadas logran tareas complejas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**                                       | **Siguiente 8**                       |
| ------------------ | ------------------------------------------------- | ------------------------------------- |
| [🏠](../README.md) | [⏪](./2_6_Different_Versions_and_Differences.md) | [⏩](./2_8_Understand_Permissions.md) |
