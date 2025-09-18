| **Inicio**         | **atrás 7**                                 | **Siguiente 9**                                     |
| ------------------ | ------------------------------------------- | --------------------------------------------------- |
| [🏠](../README.md) | [⏪](./2_7_Navigating_using_GUI_and_CLI.md) | [⏩](./2_9_Installing_Software_and_Applications.md) |

---

## **Índice**

| Temario                                                                                                    |
| ---------------------------------------------------------------------------------------------------------- |
| [52. Permisos de archivos de Linux](#52-permisos-de-archivos-de-linux)                                     |
| [53. Curso intensivo de Linux: Permisos de archivos](#53-curso-intensivo-de-linux-permisos-de-archivos)    |
| [54. Administrar permisos de Windows con CLI (Icacls)](#54-administrar-permisos-de-windows-con-cli-icacls) |

---

# **Comprender los permisos**

## **52. Permisos de archivos de Linux**

### 1. **Permisos de archivos**

En Linux, cada archivo/directorio tiene **permisos** que controlan quién puede **leer (r), escribir (w) o ejecutar (x)**.
Los permisos se definen para **tres grupos**:

- **Usuario (u):** el dueño del archivo.
- **Grupo (g):** otros usuarios que pertenecen al mismo grupo que el dueño.
- **Otros (o):** todos los demás usuarios.

Ejemplo:

```bash
ls -l archivo.txt
-rw-r--r-- 1 gustavo users 1200 ago 29 12:00 archivo.txt
```

👉 Significa:

- **rw-** → el usuario puede leer y escribir.
- **r--** → el grupo solo puede leer.
- **r--** → otros solo pueden leer.

### 2. **Modificación de permisos**

Se usa el comando `chmod`.

#### Ejemplo simbólico:

```bash
chmod u+x script.sh   # añadir permiso de ejecución al usuario
chmod g-w archivo.txt # quitar permiso de escritura al grupo
chmod o+r informe.pdf # dar permiso de lectura a otros
```

#### Ejemplo numérico:

Cada permiso tiene un valor:

- **r = 4, w = 2, x = 1**.
  Se suman:
- `chmod 755 script.sh` → usuario: rwx (7), grupo: r-x (5), otros: r-x (5).

### 3. **Permisos de propiedad**

Cada archivo tiene un **usuario propietario** y un **grupo propietario**.

- Cambiar propietario:

```bash
sudo chown juan archivo.txt
```

- Cambiar grupo:

```bash
sudo chgrp profesores archivo.txt
```

- Cambiar ambos:

```bash
sudo chown juan:profesores archivo.txt
```

### 4. **Umask**

Define los **permisos por defecto** al crear un archivo o directorio.

- Por defecto, los archivos se crean con `666` (rw-rw-rw-) y directorios con `777` (rwxrwxrwx).
- La **umask** resta permisos.

Ejemplo:

```bash
umask 022
touch nuevo.txt
ls -l nuevo.txt
-rw-r--r-- 1 gustavo users 0 ago 29 12:30 nuevo.txt
```

👉 `022` quita escritura al grupo y a otros.

### 5. **Setuid**

Permite que un programa se ejecute con los **privilegios del propietario**, no del usuario que lo ejecuta.

Ejemplo clásico:

```bash
ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 54256 ago 29 12:40 /usr/bin/passwd
```

👉 La `s` en lugar de la `x` en el permiso de usuario significa **Setuid**.
Esto permite que cualquier usuario ejecute `passwd` y pueda modificar su propia contraseña (que requiere privilegios root en `/etc/shadow`).

### 6. **Setgid**

Funciona parecido al **Setuid**, pero aplicado a **grupos**.

- En archivos ejecutables: el programa corre con el **grupo del archivo**.
- En directorios: los **archivos creados dentro** heredan el grupo del directorio.

Ejemplo con directorio de proyectos compartidos:

```bash
mkdir /proyecto
chgrp devs /proyecto
chmod g+s /proyecto
```

👉 Ahora cualquier archivo creado en `/proyecto` tendrá como grupo `devs`.

### 7. **Permisos de proceso**

Se refiere a cómo los procesos heredan permisos del usuario que los ejecuta.

- Un proceso iniciado por un usuario normal solo puede acceder a archivos que tenga permiso.
- Procesos con **Setuid/Setgid** pueden tener permisos adicionales.
- El administrador puede usar `sudo` para ejecutar procesos con privilegios elevados.

Ejemplo:

```bash
ps -u gustavo   # muestra procesos del usuario gustavo
```

### 8. **The Sticky Bit**

Un permiso especial aplicado a directorios compartidos.

- Evita que los usuarios borren archivos que **no les pertenecen**, incluso si el directorio tiene permisos de escritura para todos.

Ejemplo clásico:

```bash
ls -ld /tmp
drwxrwxrwt 10 root root 4096 ago 29 13:00 /tmp
```

👉 La `t` al final significa **Sticky Bit**.
Esto asegura que en `/tmp` (directorio compartido por todos), solo el dueño puede borrar su propio archivo.

### ✅ Resumen gráfico

| Permiso especial   | Función                                                                              | Ejemplo práctico                       |
| ------------------ | ------------------------------------------------------------------------------------ | -------------------------------------- |
| **Setuid (s)**     | Programa se ejecuta con permisos del dueño                                           | `passwd` se ejecuta como root          |
| **Setgid (s)**     | En archivos: hereda grupo del archivo<br>En directorios: hereda grupo del directorio | Carpeta compartida de trabajo en grupo |
| **Sticky Bit (t)** | Solo el dueño puede borrar su archivo en un directorio compartido                    | `/tmp` en Linux                        |

---

[🔼](#índice)

---

## **53. Curso intensivo de Linux: Permisos de archivos**

### 1) Fundamentos — quién puede hacer qué

En Unix/Linux cada archivo/directorio tiene permisos para **tres categorías**:

- **u (owner)** — el propietario (usuario).
- **g (group)** — miembros del grupo propietario.
- **o (others)** — todos los demás usuarios.

Y tres **acciones** posibles:

- **r** = read (lectura)
- **w** = write (escritura)
- **x** = execute (ejecución) — para directorios significa _traverse/search_ (permite entrar y acceder a inodos dentro).

Ejemplo típico:

```bash
$ ls -l archivo.txt
-rw-r--r-- 1 gustavo users 1200 Aug 29 12:00 archivo.txt
```

Interpretación: owner `gustavo` tiene `rw-`, grupo `users` tiene `r--`, otros `r--`.

### 2) Representación: simbólica y numérica

- Simbólica: `rwxr-xr--`
- Numérica (octal): cada trio es la suma r(4)+w(2)+x(1). Ej.: `rwx` = 7, `r-x` = 5 → `chmod 755`.

Comandos básicos:

```bash
chmod 644 archivo.txt       # owner rw, group r, others r
chmod u+x script.sh         # añade ejecución al owner (simbólico)
chmod g-w archivo.txt       # quita escritura al grupo
```

### 3) Propietario y grupo

- `chown usuario archivo` — cambia propietario
- `chgrp grupo archivo` — cambia grupo
- `chown usuario:grupo archivo` — cambia ambos

Ejemplo:

```bash
sudo chown alice:devs /srv/proyecto/readme.md
```

Recursivo:

```bash
sudo chown -R root:root /opt/miapp
```

> Atención: `-R` puede seguir enlaces simbólicos en algunos sistemas — usa con cuidado.

### 4) Umask — permisos por defecto al crear

Umask “quita” permisos por defecto. Si el kernel ofrece `666` para archivos y `777` para directorios, umask resta esos bits.

Ejemplos:

```bash
umask 022           # valor común: archivos 644, dirs 755
umask 002           # en entornos colaborativos: archivos 664, dirs 775
umask 077           # máximo privado: archivos 600, dirs 700
```

Demostración práctica:

```bash
umask 022
touch f1; mkdir d1
ls -l f1 d1
# f1 -> -rw-r--r-- ; d1 -> drwxr-xr-x
```

Dónde configurarlo: `~/.profile`, `~/.bashrc`, o global `/etc/profile`, `/etc/login.defs` o PAM (`pam_umask`).

### 5) Bits especiales: setuid, setgid, sticky

#### Setuid (bit `u+s`, octal `4000`)

Cuando un archivo ejecutable tiene setuid, **se ejecuta con el EUID (effective UID)** del propietario del archivo.

Ejemplo clásico:

```bash
ls -l /usr/bin/passwd
-rwsr-xr-x 1 root root 54256 ... /usr/bin/passwd
```

`passwd` corre con EUID=root para poder modificar `/etc/shadow`.
**Nota importante:** setuid en scripts (shell/perl/python) suele ser ignorado por seguridad en la mayoría de kernels.

Para establecer:

```bash
sudo chown root:root mi_programa
sudo chmod 4755 mi_programa   # 4xxx = setuid + permisos
# o simbólico:
sudo chmod u+s mi_programa
```

#### Setgid (bit `g+s`, octal `2000`)

- En **archivos ejecutables**: el programa corre con el GID del archivo.
- En **directorios**: los ficheros creados heredan el _grupo del directorio_ en lugar del grupo primario del creador — muy útil en carpetas de equipo.

Ejemplo:

```bash
sudo chgrp devs /srv/proyecto
sudo chmod 2775 /srv/proyecto    # g+s en directorio
# Ahora archivos creados tendrán grupo 'devs'
```

#### Sticky bit (bit `o+t`, octal `1000`)

Se aplica en directorios compartidos para evitar que alguien borre archivos que no le pertenecen.
Ejemplo:

```bash
sudo chmod 1777 /tmp
ls -ld /tmp
drwxrwxrwt 10 root root ... /tmp
# 't' final = sticky; todos pueden escribir, pero solo el dueño puede borrar sus archivos.
```

### 6) Permisos en directorios — el bit `x` significa _traversal_

- `r` permite listar nombres en el directorio (ls).
- `x` permite acceder al inodo (entrar con `cd`, abrir archivos si conoces el nombre).
- `w` permite crear/borrar entradas (pero para borrar un archivo también necesitas que tú seas el owner del archivo o que el directorio permita la operación y sticky bit no lo impida).

Ejemplo: un directorio con `r--` y sin `x`:

```bash
mkdir dtest; chmod 400 dtest
# No podrás cd dtest (falta x); ni ls correctamente.
```

### 7) Permisos de proceso: real UID / effective UID / saved UID

Cuando un ejecutable setuid se ejecuta:

- **real UID (RUID)** = quien lanzó el proceso
- **effective UID (EUID)** = si setuid aplicado, será el propietario (p. ej. root) — determina permisos de acceso a archivos.
  Puedes comprobar dentro de un intérprete Python:

```bash
python3 -c 'import os; print("ruid:", os.getuid(), "euid:", os.geteuid())'
```

Esto explica por qué `passwd` puede escribir en `/etc/shadow`: su proceso tiene EUID=root.

### 8) Evitar setuid inseguro — alternativas: sudo, capabilities

Evita usar setuid en binarios no auditados. Alternativas:

- **sudoers** (`/etc/sudoers`) para permitir comandos concretos sin contraseña o con restricción.
- **POSIX capabilities** (`setcap`) para dar capacidades específicas (p. ej. bind a puertos <1024) sin hacer binario root.

Ejemplo: permitir a un binario ligar puertos privilegiados sin setuid:

```bash
sudo setcap 'cap_net_bind_service=+ep' /usr/bin/myserver
getcap /usr/bin/myserver
```

### 9) ACLs (Access Control Lists) — cuando permisos u/g/o no bastan

ACL permite dar permisos finos a usuarios/grupos adicionales.

Instala utilidades (`getfacl`, `setfacl`), luego:

```bash
# Dar a alice rwx en archivo
setfacl -m u:alice:rwx archivo.txt

# Ver ACL
getfacl archivo.txt

# Default ACL para directorio (heredable)
setfacl -d -m g:devs:rwx /srv/proyecto
```

Importante: ACLs introducen una **mask** que puede limitar permisos efectivos. Usa `getfacl` para interpretar.

### 10) Auditar/Buscar permisos inseguros

Ejemplos útiles para hardening:

- Encontrar archivos **setuid**:

```bash
sudo find / -xdev -type f -perm /4000 -ls
```

- Encontrar archivos **setgid**:

```bash
sudo find / -xdev -type f -perm /2000 -ls
```

- Archivos **world-writable**:

```bash
sudo find / -xdev -type f -perm -0002 -ls
```

- Directorios world-writable **sin sticky bit** (riesgo):

```bash
sudo find / -xdev -type d -perm -0002 ! -perm -1000 -ls
```

- Archivos con permisos 777 (muy peligroso):

```bash
sudo find / -xdev -type f -perm 0777 -ls
```

Crea un reporte:

```bash
sudo bash -c 'echo -e "SUID files:\n"; find / -xdev -type f -perm /4000 -ls >/tmp/suid.txt; cat /tmp/suid.txt'
```

### 11) Buenas prácticas y políticas recomendadas

- **Principio de mínimo privilegio**: owner y group mínimos; no dar 777 ni 666 salvo casos controlados.
- **/tmp debe tener sticky bit** (`1777`).
- **Home dirs** en multiusuario: `chmod 700 /home/usuario` para privacidad (o 750 en caso de compartir).
- **/etc/passwd**: normalmente `644`; **/etc/shadow**: `600` o `640` con group `shadow`.
- No uses **setuid** a menos que sea estrictamente necesario y el binario esté auditado. Prefiere `sudo` o `capabilities`.
- Usa **grupos** para colaboración y `setgid` en directorios de equipo.
- Mantén **umask** apropiada: `022` para servidores públicos, `007` o `002` para entornos colaborativos, `077` para máxima privacidad.
- Añade **auditoría** (scripts) que corran periódicamente y reporten world-writable, SUID, etc.

### 12) Ejercicios prácticos (manos a la obra)

Realiza estos ejercicios en tu laboratorio:

#### Ej. A — crear carpeta de equipo

1. Crear grupo `devs` y usuarios `alice`, `bob`:

```bash
sudo groupadd devs
sudo useradd -m alice; sudo passwd alice
sudo useradd -m bob; sudo passwd bob
sudo usermod -aG devs alice
sudo usermod -aG devs bob
```

2. Crear `/srv/proyecto`, asignar grupo y setgid:

```bash
sudo mkdir -p /srv/proyecto
sudo chown :devs /srv/proyecto
sudo chmod 2775 /srv/proyecto   # drwxrwsr-x
```

3. Conéctate como `alice`, crea archivo y verifica que `bob` puede editarlo (respecto a umask). Si `alice` crea con `umask 022`, bob no podrá escribir. Ajusta `umask 002` para colaboración.

#### Ej. B — auditar SUID/World-writable

```bash
sudo find / -xdev -type f \( -perm -4000 -o -perm -2000 -o -perm -002 \) -ls | tee /tmp/perm_audit.txt
```

Revisa `/tmp/perm_audit.txt` y corrige archivos innecesarios:

```bash
sudo chmod u-s /ruta/insegura   # quitar setuid
sudo chmod 644 /ruta/archivo_inseguro
```

#### Ej. C — ACLs heredables

```bash
sudo mkdir /srv/shared
sudo chown root:devs /srv/shared
sudo setfacl -m g:devs:rwx /srv/shared
sudo setfacl -d -m g:devs:rwx /srv/shared   # default ACL (hereda)
```

Prueba creando archivos como distintos usuarios y comprueba permisos.

### 13) Atajos y comandos de referencia (cheat-sheet)

```text
ls -l file            # ver permisos
stat file             # ver detalles inode/perm en octal
chmod 755 file        # permisos numéricos
chmod u+s file        # setuid (simbólico)
chmod g+s dir         # setgid en dir
chmod o+t dir         # sticky bit
chown user:group file
chgrp group file
umask 022
getfacl file
setfacl -m u:alice:rwx file
find / -perm -4000 -ls   # buscar setuid
find / -perm -002 -ls    # buscar world-writable
setcap 'cap_net_bind_service=+ep' /usr/bin/myapp  # capabilities
```

### 14) Escenario real de hardening (puntos de control)

- `/tmp` → `1777` (sticky).
- `/var/www` (webroot) → no files 777; archivos 644, dirs 755.
- Binarios sensibles → revisar SUID y preferir `CAP_*`.
- `/home` → 700 o 750 según política.
- `/etc/shadow` → 600.
- Logs → permisos que sólo root pueda leer.
- Montaje seguro: `/tmp` con `nosuid,nodev,noexec` si no necesitas ejecutar binarios; `/home` con `nodev` en algunos casos.

Ejemplo fstab entry:

```
tmpfs /tmp tmpfs rw,nosuid,nodev,noexec,mode=1777 0 0
```

(Nota: `noexec` puede romper algunas apps que dependen de ejecuciones en /tmp — probar antes.)

### 15) Resumen y recomendaciones finales

- Entiende la **estructura** (u/g/o + r/w/x).
- Usa **umask** y grupos para colaboración eficiente.
- Aplica **setgid** en directorios de equipo, evita setuid salvo autorizaciones.
- Usa **ACLs** cuando necesites permisos finos; documenta las ACLs.
- Implementa auditorías automáticas (find + report).
- Prefiere **sudo** y **capabilities** sobre setuid root cuando sea posible.
- Aplica mount options y revisa periodicamente `find` para detectar problemas.

---

[🔼](#índice)

---

## **54. Administrar permisos de Windows con CLI (Icacls)**

`icacls` es la herramienta de línea de comandos estándar en Windows para **ver, cambiar, exportar e importar** ACLs (Access Control Lists) NTFS. En esta guía te explico la sintaxis, los permisos más usados, cómo trabajar con herencia, cómo hacer cambios recursivos, cómo hacer backup/restore de ACLs, ejemplos prácticos y recomendaciones de seguridad.

> Nota: para muchas operaciones necesitas una **Símbolo del sistema (cmd) o PowerShell ejecutado como Administrador**.

### 1) Conceptos rápidos que necesitas recordar

- **NTFS ACL** está formada por ACEs (Access Control Entries) que dicen _qué_ (permiso) _a quién_ (usuario/grupo) _sobre qué_ (archivo/directorio).
- `icacls` trabaja con **permisos abreviados**:

  - `F` = Full control
  - `M` = Modify
  - `RX` = Read & execute
  - `R` = Read
  - `W` = Write
  - `D` = Delete

- **Herencia**: `(OI)` = Object Inherit (archivos), `(CI)` = Container Inherit (subdirectorios), `(IO)` = Inherit Only (solo para herencia).
  Ejemplo combinado: `(OI)(CI)M` → aplica Modify a archivos y carpetas dentro (heredable).
- ACE explícitas vs heredadas: **explícitas** se agregan al objeto en sí; **heredadas** vienen del contenedor padre.

### 2) Sintaxis básica / ver ACLs

```cmd
icacls <ruta>
```

Ejemplo:

```cmd
C:\> icacls "C:\Proyectos\MiApp"
C:\Proyectos\MiApp BUILTIN\Administrators:(I)(F)
                     NT AUTHORITY\SYSTEM:(I)(F)
                     BUILTIN\Users:(I)(RX)
```

`(I)` significa que la ACE es **inhered** (heredada).

### 3) Conceder permisos (grant)

#### Sintaxis general:

```cmd
icacls <ruta> /grant[:r] "Usuario|Grupo":(<flags>) [opciones]
```

- `:r` después de `/grant` = **reemplazar** ACEs del usuario (en lugar de agregar).
- `(<flags>)` puede incluir `(OI)(CI)` y el permiso (M, F, RX, R, W, etc).

##### Ejemplo: dar Modify recursivo a un grupo de desarrollo

```cmd
icacls "C:\Proyectos\MiApp" /grant "MYDOMAIN\devs":(OI)(CI)M /T /C
```

- `/T` = recursivo (todas las subcarpetas y archivos)
- `/C` = continuar en caso de errores

##### Ejemplo: dar Full control solo al usuario local `juan` sin herencia

```cmd
icacls "C:\Proyectos\MiApp\confidential.txt" /grant "JUAN":F
```

### 4) Denegar permisos (deny)

`/deny` agrega una ACE de denegación (útil pero **peligroso**: las ACE de Deny suelen tener prioridad).

```cmd
icacls "C:\Proyectos\MiApp\secret" /deny "MYDOMAIN\templ_user":(CI)(OI)W /T /C
```

**Advertencia:** usa `/deny` solo cuando sea necesario, porque puede crear efectos inesperados (niega antes de permitir).

### 5) Quitar permisos (remove)

- `/remove Sid` → elimina todas las ACEs asociadas a ese SID (usuario/grupo).
- `/remove:g Sid` → elimina sólo las ACEs Grant (concedidas).
- `/remove:d Sid` → elimina sólo las ACEs Deny.

Ejemplo:

```cmd
icacls "C:\Proyectos\MiApp" /remove "MYDOMAIN\ex_usuario" /T /C
```

### 6) Trabajar con herencia (inheritance)

- `/inheritance:e` → **enable** inheritance (habilitar herencia desde el padre).
- `/inheritance:d` → **disable** inheritance **pero copiar** las ACE heredadas como explícitas (se preservan).
- `/inheritance:r` → **remove** todas las ACE heredadas (quitar herencia y borrar las ACE heredadas).

Ejemplos:

```cmd
icacls "C:\Proyectos\MiApp" /inheritance:d
icacls "C:\Proyectos\MiApp" /inheritance:r
icacls "C:\Proyectos\MiApp" /inheritance:e
```

- Uso típico: si quieres que una carpeta deje de recibir cambios desde el padre, usa `/inheritance:d` (se copiaron las ACE actuales y se volverán explícitas).

### 7) Establecer dueño (owner) — `icacls` y `takeown`

- `icacls <ruta> /setowner "Usuario"` — cambia el dueño.
- `takeown /f <ruta> /r /d y` — toma posesión (útil si no eres el dueño). Suele usarse antes de `/setowner` si es necesario.

Ejemplo:

```cmd
takeown /f "C:\Proyectos\MiApp" /r /d y
icacls "C:\Proyectos\MiApp" /setowner "Administrators" /T
```

### 8) Back up y restore de ACLs (muy útil en migraciones)

#### Guardar ACLs (exportar)

```cmd
icacls "C:\Proyectos" /save "C:\Backups\acl_backup.txt" /T
```

- Genera un archivo con la estructura `ruta` + SDDL/ACE por cada entrada.

#### Restaurar ACLs

```cmd
icacls /restore "C:\Backups\acl_backup.txt"
```

- `icacls /restore` aplica las ACLs exactamente como estaban cuando se guardaron (útil después de mover/copiar datos o restaurar archivos).

**Flujo típico**: `icacls /save` → copia/restore de datos → `icacls /restore`.

### 9) Otras opciones importantes

- `/T` → recorrer subdirectorios (recursivo).
- `/C` → continuar tras errores (no se detiene).
- `/Q` → quiet/silenciar mensajes de éxito.
- `/L` → trabaja sobre el enlace simbólico en vez del objetivo (si estás en un sistema con links NTFS).
- `/verify` — (no siempre necesaria): comprobar integridad de ACL? (Usa `icacls path` + inspección a GUI/PowerShell).
  (Nota: usa `/T /C /Q` frecuentemente en scripts para robustez.)

### 10) Ejemplos prácticos comunes

#### 10.1 Crear carpeta y dar permisos de colaboración

```cmd
md "C:\Team\ProyectoX"
icacls "C:\Team\ProyectoX" /grant "MYDOMAIN\devs":(OI)(CI)M
icacls "C:\Team\ProyectoX" /grant "MYDOMAIN\lead":(OI)(CI)F
icacls "C:\Team\ProyectoX" /inheritance:d
```

- `devs` recibe Modify en archivos y carpetas creadas dentro.
- `lead` tiene Full Control.
- Se desactiva herencia futura copiando ACEs actuales.

#### 10.2 Quitar todos los permisos heredados y empezar limpio

```cmd
icacls "C:\Folder" /inheritance:r
icacls "C:\Folder" /remove:g "Authenticated Users" /C
icacls "C:\Folder" /grant "DOMAIN\Administrators":F /T
```

#### 10.3 Resetear permisos a los predeterminados (reset)

```cmd
icacls "C:\Carpeta" /reset /T /C
```

`/reset` reemplaza las ACEs actuales por las heredadas del padre (útil para limpiar permisos "rotos").

#### 10.4 Copiar permisos de una carpeta a otra (por robocopy)

Si copias archivos con `robocopy`:

```cmd
robocopy "C:\Origen" "D:\Destino" /MIR /COPYALL /SEC /R:1 /W:1
```

- `/COPYALL` copia datos + atributos + timestamps + permisos (ACLs) + owner + auditing.
- Alternativa: usar `icacls /save` + copiar datos + `icacls /restore`.

#### 10.5 Conceder permiso de solo lectura a Everyone

```cmd
icacls "C:\Public\ReadMe.txt" /grant "Everyone":R
```

#### 10.6 Denegar eliminar (deny delete) a un grupo en una carpeta

```cmd
icacls "C:\Important" /deny "MYDOMAIN\contractors":(CI)(OI)D /T
```

- `D` es Delete; denegar delete evita que borren archivos, aunque puedan leer/escribir según otras ACEs.

### 11) Cómo ver permisos efectivos (Effective Access)

`icacls` no calcula el _effective access_ en contexto de un usuario y múltiples grupos; para eso hay herramientas:

- **GUI**: Propiedades → Seguridad → Avanzado → _Effective Access_ (Introduce usuario y calcula).
- **PowerShell** (más avanzado): existen módulos y scripts que calculan permisos efectivos; `Get-Acl` te muestra ACL, pero para calculado necesitas evaluar tokens y grupos del usuario.

Ejemplo básico PowerShell para ver ACL:

```powershell
Get-Acl "C:\Proyectos\MiApp" | Format-List
```

### 12) Automatización: scripts de ejemplo

#### Script (cmd) — conceder Modify recursivo y hacer backup de ACLs antes

```cmd
@echo off
set FOLDER=C:\Proyectos\MiApp
set BACKUP=C:\Backups\MiApp_acls.txt

echo Guardando ACLs...
icacls "%FOLDER%" /save "%BACKUP%" /T

echo Concediendo permisos a MYDOMAIN\devs...
icacls "%FOLDER%" /grant "MYDOMAIN\devs":(OI)(CI)M /T /C

echo Hecho.
```

#### Script (PowerShell) — restaurar ACLs en varios servidores via remoting

```powershell
$servers = "srv01","srv02"
$aclFile = "\\fileserver\backups\miapp_acls.txt"
foreach ($s in $servers) {
  Invoke-Command -ComputerName $s -ScriptBlock {
    param($file)
    icacls /restore $file
  } -ArgumentList $aclFile
}
```

### 13) Buenas prácticas y consejos de seguridad

- **Haz backup de ACLs** (`icacls /save`) antes de cambios masivos.
- Evita ACEs `/deny` salvo cuando sea imprescindible; prioriza ajustar grants explícitas.
- **No uses `icacls` como root si no necesitas** — seguir principio de menor privilegio.
- Para tareas de administración, **documenta** cambios (guardar logs de icacls).
- Para replicar permisos entre entornos, usa `/save` + `/restore` en conjunto con copia de datos.
- Prefiere **grupos** (no usuarios individuales) para dar permisos — facilita cambios futuros.
- Considera **auditing** y alertas si se cambian owners o se agregan ACEs peligrosas.
- Cuando copies datos entre volúmenes, usa `robocopy /COPYALL` o `icacls /save` antes y `/restore` después para no perder ACLs.

### 14) Errores comunes y cómo resolverlos

- **"Access is denied"** al intentar cambiar permisos: abre cmd/PowerShell como Administrator.
- **Permisos que no se aplican a archivos nuevos:** asegúrate que la carpeta tenga `(CI)(OI)` y que la umask/creación respete herencia.
- **Herencia "sorprendente"**: revisa si la carpeta tiene herencia habilitada; usa `/inheritance:d` o `/r` según necesidad.
- **Permisos contradictorios**: recuerda que Deny suele tener prioridad; evalúa orden de ACEs y evita Deny en lo posible.
- **Problemas al restaurar ACL**: el archivo generado por `/save` tiene rutas absolutas; restaura en la misma estructura o edítalo cuidadosamente.

### 15) Cheat-sheet rápido (comandos más útiles)

```text
icacls "C:\ruta"                           # mostrar ACL
icacls "C:\ruta" /grant "User":(OI)(CI)M   # grant Modify recursivo
icacls "C:\ruta" /grant:r "User":F         # replace grants
icacls "C:\ruta" /deny "User":D            # deny delete
icacls "C:\ruta" /remove "User"            # remove user ACEs
icacls "C:\ruta" /inheritance:d            # disable inheritance (copy ACEs)
icacls "C:\ruta" /inheritance:r            # remove inherited ACEs
icacls "C:\ruta" /setowner "Administrators" /T
icacls "C:\ruta" /save "C:\tmp\acls.txt" /T
icacls /restore "C:\tmp\acls.txt"
icacls "C:\ruta" /reset /T /C              # reset to inherited defaults
robocopy "C:\origen" "D:\dest" /MIR /COPYALL  # copia archivos + ACLs + owner
```

### 16) Recursos adicionales (herramientas complementarias)

- **GUI**: Propiedades → Seguridad → Avanzado → para edición y ver efectivas.
- **PowerShell**: `Get-Acl` / `Set-Acl` para manipular ACL desde scripts .NET.
- **robocopy** para copiar archivos conservando ACLs/owner.
- **Audit / Security event logs** para monitorear cambios de seguridad.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**                                 | **Siguiente 9**                                     |
| ------------------ | ------------------------------------------- | --------------------------------------------------- |
| [🏠](../README.md) | [⏪](./2_7_Navigating_using_GUI_and_CLI.md) | [⏩](./2_9_Installing_Software_and_Applications.md) |
