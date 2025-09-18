| **Inicio**         | **atrás 11**                    |
| ------------------ | ------------------------------- |
| [🏠](../README.md) | [⏪](./2_11_Troubleshooting.md) |

---

## **Índice**

| Temario                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------- |
| [60. Comandos esenciales de Unix](#60-comandos-esenciales-de-unix)                                                           |
| [61. 60 comandos de Linux que debes conocer](#61-60-comandos-de-linux-que-debes-conocer)                                     |
| [62. Los 40 principales comandos de Windows que debes conocer](#62-los-40-principales-comandos-de-windows-que-debes-conocer) |

---

# **Comandos comunes**

## **60. Comandos esenciales de Unix**

### 1. Comandos Unix de **navegación del sistema de archivos**

Estos comandos permiten movernos dentro del sistema de archivos, similar a "Explorador de Archivos" en Windows o "Finder" en macOS.

- **`pwd`** → muestra el directorio actual (print working directory).

```bash
pwd
# /home/usuario
```

👉 Ejemplo: saber en qué carpeta estás trabajando.

- **`ls`** → lista archivos y directorios.

```bash
ls -l
```

👉 Ejemplo: muestra archivos con detalles (permisos, fecha, tamaño).

- **`cd`** → cambiar de directorio.

```bash
cd /etc
```

👉 Ejemplo: moverte a la carpeta `/etc`.

### 2. Comandos Unix de **manipulación de archivos**

Permiten crear, copiar, mover y eliminar archivos o directorios.

- **`touch`** → crear un archivo vacío.

```bash
touch notas.txt
```

👉 Ejemplo: crear un archivo de texto llamado `notas.txt`.

- **`cp`** → copiar archivos.

```bash
cp notas.txt copia_notas.txt
```

👉 Ejemplo: hacer una copia de seguridad de `notas.txt`.

- **`mv`** → mover o renombrar archivos.

```bash
mv notas.txt documentos/
mv copia_notas.txt resumen.txt
```

👉 Ejemplo: mover `notas.txt` a la carpeta `documentos` o renombrar `copia_notas.txt` a `resumen.txt`.

- **`rm`** → borrar archivos.

```bash
rm resumen.txt
```

👉 Ejemplo: eliminar el archivo `resumen.txt`.

- **`mkdir`** y **`rmdir`** → crear y borrar directorios.

```bash
mkdir proyectos
rmdir proyectos
```

### 3. Comandos Unix de **gestión de procesos**

Permiten controlar programas en ejecución (procesos).

- **`ps`** → lista procesos en ejecución.

```bash
ps aux
```

👉 Ejemplo: ver todos los procesos del sistema con detalles.

- **`top`** o **`htop`** → monitoreo en tiempo real de procesos y uso de recursos.

```bash
top
```

- **`kill`** → terminar procesos por su PID (Process ID).

```bash
kill 1234
```

👉 Ejemplo: matar el proceso con ID `1234`.

- **`jobs`** y **`fg/bg`** → manejar procesos en segundo plano.

```bash
sleep 100 &
jobs
fg %1
```

👉 Ejemplo: enviar un proceso al segundo plano (`&`), listar trabajos (`jobs`) y traerlo al frente (`fg`).

### 4. Comandos Unix de **procesamiento de texto**

Unix se destaca por el manejo de texto en archivos.

- **`cat`** → mostrar contenido de un archivo.

```bash
cat notas.txt
```

- **`more` / `less`** → ver contenido largo página por página.

```bash
less /etc/passwd
```

- **`grep`** → buscar texto dentro de archivos.

```bash
grep "root" /etc/passwd
```

👉 Ejemplo: encontrar todas las líneas que contienen la palabra _root_.

- **`wc`** → contar líneas, palabras y caracteres.

```bash
wc -l notas.txt
```

- **`head`** y **`tail`** → mostrar primeras o últimas líneas de un archivo.

```bash
head -n 5 notas.txt
tail -n 10 notas.txt
```

### 5. Comandos Unix de **comunicación en red**

Permiten conectarse y diagnosticar redes.

- **`ping`** → comprobar conectividad.

```bash
ping google.com
```

- **`ifconfig`** o **`ip addr`** → ver configuración de red.

```bash
ip addr show
```

- **`netstat`** o **`ss`** → ver conexiones de red activas.

```bash
ss -tuln
```

- **`scp`** → copiar archivos entre equipos vía SSH.

```bash
scp notas.txt usuario@192.168.1.10:/home/usuario/
```

- **`wget`** o **`curl`** → descargar archivos de Internet.

```bash
wget https://ejemplo.com/archivo.zip
```

### 6. **Editores de texto en Unix**

Son herramientas para crear o modificar archivos de texto.

- **`nano`** → editor simple e intuitivo.

```bash
nano notas.txt
```

👉 Ejemplo: abrir el archivo `notas.txt` y editarlo.

- **`vim`** o **`vi`** → editor avanzado, muy poderoso.

```bash
vim notas.txt
```

- **`emacs`** → otro editor avanzado, usado por programadores.

👉 Caso práctico:

Si quieres modificar la configuración de la red en Linux, puedes abrir el archivo de configuración:

```bash
sudo nano /etc/network/interfaces
```

✅ **En resumen**:

- Navegación → `pwd`, `ls`, `cd`
- Archivos → `touch`, `cp`, `mv`, `rm`, `mkdir`
- Procesos → `ps`, `top`, `kill`, `jobs`
- Texto → `cat`, `grep`, `wc`, `less`, `head`
- Red → `ping`, `ip addr`, `ss`, `scp`, `wget`
- Editores → `nano`, `vim`, `emacs`

---

[🔼](#índice)

---

## **61. 60 comandos de Linux que debes conocer**

### Navegación y gestión de archivos (1–12)

1. `ls` — listar archivos y carpetas.

   - Opciones comunes: `-l` (long), `-a` (oculta), `-h` (humano).
   - Ejemplo:

     ```bash
     ls -lah /home/usuario
     ```

     Muestra lista detallada, tamaños legibles y archivos ocultos.

2. `cd` — cambiar de directorio.

   - Ejemplos:

     ```bash
     cd /var/log
     cd ~        # va al home
     cd -        # vuelve al último directorio
     ```

3. `pwd` — muestra el directorio actual (print working directory).

   - Ejemplo:

     ```bash
     pwd
     # /home/usuario/proyecto
     ```

4. `mkdir` — crear directorios.

   - `-p` crea padres si no existen.
   - Ejemplo:

     ```bash
     mkdir -p proyecto/src/components
     ```

5. `rmdir` — eliminar directorios vacíos.

   - Para eliminar directorios no vacíos usa `rm -r` (ver abajo).
   - Ejemplo:

     ```bash
     rmdir carpeta_vacia
     ```

6. `touch` — crear archivo vacío o actualizar timestamp.

   - Ejemplo:

     ```bash
     touch nota.txt
     ```

7. `cp` — copiar archivos/directorios.

   - Opciones: `-r` (recursivo), `-i` (pregunta), `-v` (verbose), `-a` (archivos + atributos).
   - Ejemplo:

     ```bash
     cp -av carpeta_origen/ carpeta_destino/
     ```

8. `mv` — mover o renombrar.

   - Ejemplo:

     ```bash
     mv viejo_nombre.txt nuevo_nombre.txt
     mv archivo.txt /ruta/destino/
     ```

9. `rm` — eliminar archivos (¡peligroso!).

   - `-r` recursivo, `-f` forzar. **Cuidado** con `rm -rf`.
   - Ejemplo seguro:

     ```bash
     rm -i archivo.txt   # pregunta antes
     rm -rf carpeta_a_eliminar   # borra todo sin preguntar (peligroso)
     ```

10. `tree` — muestra árbol de directorios (no siempre instalado).

    - Ejemplo:

      ```bash
      tree -L 2
      ```

11. `find` — buscar archivos por nombre, tipo, fecha, etc. Muy potente.

    - Ejemplo: buscar archivos `.log` modificados en últimos 7 días y listarlos:

      ```bash
      find /var/log -type f -name "*.log" -mtime -7
      ```

    - Usando `-exec`:

      ```bash
      find . -name "*.tmp" -exec rm -v {} \;
      ```

12. `locate` — búsqueda rápida por base de datos (usa `updatedb`).

    - Ejemplo:

      ```bash
      sudo updatedb        # actualizar base de datos (se necesita)
      locate passwd | head
      ```

### Ver y leer archivos (13–20)

13. `cat` — concatenar y mostrar archivos.

    - Ejemplo:

      ```bash
      cat archivo.txt
      cat a.txt b.txt > combinado.txt
      ```

14. `tac` — `cat` al revés (muestra líneas en orden inverso).

    - Ejemplo:

      ```bash
      tac archivo.log | head
      ```

15. `less` — paginador interactivo (mejor que `more`).

    - Navegación: `q` salir, `/texto` buscar, `F` follow (como `tail -f`).
    - Ejemplo:

      ```bash
      less /var/log/syslog
      ```

16. `more` — paginador simple (antiguo).

    - Ejemplo:

      ```bash
      more archivo_largo.txt
      ```

17. `head` — ver primeras líneas.

    - `-n` número de líneas.
    - Ejemplo:

      ```bash
      head -n 20 archivo.txt
      ```

18. `tail` — ver últimas líneas.

    - `-f` seguir en tiempo real (logs).
    - Ejemplo:

      ```bash
      tail -f /var/log/nginx/access.log
      ```

19. `wc` — contar líneas, palabras, bytes.

    - `wc -l` (líneas), `-w` (palabras), `-c` (bytes).
    - Ejemplo:

      ```bash
      wc -l archivo.txt
      ```

20. `file` — identifica tipo de archivo (binario, texto, script).

    - Ejemplo:

      ```bash
      file /bin/ls
      ```

### Buscar y procesar texto (21–28)

21. `grep` — buscar texto/expresiones regulares en archivos.

    - Opciones: `-R` recursivo, `-i` sin case, `-n` mostrar línea, `-E` regex extendido.
    - Ejemplo:

      ```bash
      grep -Rin "error" /var/log
      ```

22. `sed` — editor de streams (sustituciones rápidas).

    - Ejemplo (sustituir "foo" por "bar"):

      ```bash
      sed 's/foo/bar/g' archivo.txt > nuevo.txt
      sed -i 's/foo/bar/g' archivo.txt   # in-place (modifica archivo)
      ```

23. `awk` — procesador de texto/columnas muy poderoso.

    - Ejemplo (imprimir primer y tercer campo):

      ```bash
      awk '{print $1, $3}' archivo.txt
      ```

    - Ejemplo con `:` como separador (por ej. `/etc/passwd`):

      ```bash
      awk -F: '{print $1}' /etc/passwd
      ```

24. `cut` — extraer columnas.

    - Ejemplo:

      ```bash
      cut -d: -f1 /etc/passwd   # usuario (delimitador :)
      ```

25. `tr` — transformar caracteres (mayúsculas/minúsculas, espacios a saltos).

    - Ejemplo: palabras separadas por espacios a líneas:

      ```bash
      echo "uno dos tres" | tr ' ' '\n'
      ```

26. `sort` — ordenar líneas.

    - `-n` numérico, `-r` inverso, `-k` clave.
    - Ejemplo:

      ```bash
      sort -n -k2 archivo.txt
      ```

27. `uniq` — eliminar duplicados consecutivos (usar con `sort`).

    - Ejemplo (contar ocurrencias):

      ```bash
      sort lista.txt | uniq -c | sort -nr
      ```

28. `xargs` — construir y ejecutar argumentos desde STDIN (útil con `find`).

    - Ejemplo seguro con `-print0` y `-0`:

      ```bash
      find . -name "*.tmp" -print0 | xargs -0 rm -v
      ```

### Permisos, enlaces y usuarios (29–36)

29. `chmod` — cambiar permisos.

    - Formas: simbólica (`u+rwx`) o numérica (`755`, `644`).
    - Ejemplos:

      ```bash
      chmod 644 archivo.txt     # rw-r--r--
      chmod -R 755 carpeta/     # recursivo
      chmod u+x script.sh       # darle permiso de ejecución al usuario
      ```

30. `chown` — cambiar propietario y grupo.

    - Ejemplo:

      ```bash
      sudo chown usuario:grupo archivo.txt
      ```

31. `chgrp` — cambiar solo el grupo.

    - Ejemplo:

      ```bash
      chgrp staff archivo.txt
      ```

32. `ln` — crear enlaces (hardlink o symlink con `-s`).

    - Ejemplo symlink:

      ```bash
      ln -s /ruta/origen enlace_simbolico
      ```

33. `sudo` — ejecutar comandos como root (seguridad/registro).

    - Ejemplo:

      ```bash
      sudo apt update
      ```

34. `su` — cambiar de usuario (a root con `su -`).

    - Ejemplo:

      ```bash
      su -        # cambiar a root (pide contraseña de root)
      su usuario
      ```

35. `passwd` — cambiar contraseña (propia o de otro usuario con sudo).

    - Ejemplo:

      ```bash
      passwd           # cambia tu contraseña
      sudo passwd pepe # cambia contraseña del usuario pepe
      ```

36. `whoami` / `id` — mostrar nombre de usuario actual y datos de identidad.

    - Ejemplos:

      ```bash
      whoami
      id usuario
      ```

### Procesos y servicios (37–45)

37. `ps` — muestra procesos en el sistema.

    - Ejemplo común:

      ```bash
      ps aux | grep nginx
      ```

      `ps aux` muestra todos los procesos con usuario y consumo.

38. `top` — monitor interactivo de procesos (CPU/memoria).

    - Dentro: pulsa `P` (ordenar por CPU), `M` por memoria, `q` sale.
    - Ejemplo:

      ```bash
      top
      ```

39. `kill` — enviar señal a PID (por defecto SIGTERM).

    - Ejemplo:

      ```bash
      kill 12345
      kill -9 12345   # SIGKILL (forzar), usar como último recurso
      ```

40. `killall` — matar procesos por nombre.

    - Ejemplo:

      ```bash
      killall nginx
      ```

41. `nice` — ejecutar con prioridad ajustada (menor prioridad = número mayor).

    - Ejemplo:

      ```bash
      nice -n 10 long_run_script.sh
      ```

42. `renice` — cambiar prioridad de procesos en ejecución.

    - Ejemplo:

      ```bash
      sudo renice -n 5 -p 12345
      ```

43. `systemctl` — gestionar servicios systemd (start/stop/status/enable).

    - Ejemplos:

      ```bash
      systemctl status sshd
      sudo systemctl restart nginx
      sudo systemctl enable --now apache2
      ```

44. `journalctl` — ver logs del journal (systemd).

    - Ejemplos:

      ```bash
      journalctl -u ssh.service         # logs de ssh
      journalctl -f                     # seguir en tiempo real
      journalctl -r                     # orden invertido (recientes primero)
      ```

45. `uptime` — tiempo que lleva activo el sistema y carga promedio.

    - Ejemplo:

      ```bash
      uptime
      # 21:34:12 up 10 days,  3:12,  2 users,  load average: 0.15, 0.10, 0.05
      ```

### Red y transferencia de archivos (46–54)

46. `ip` — gestor moderno de red (reemplaza a `ifconfig` / `route`).

    - Ejemplos:

      ```bash
      ip addr show
      ip link set dev eth0 up
      ip route show
      ```

47. `ss` — ver sockets/puertos (reemplaza a `netstat`).

    - Ejemplo:

      ```bash
      ss -tuln   # tcp/udp listening ports
      ```

48. `ping` — comprobar conectividad ICMP.

    - Ejemplo:

      ```bash
      ping -c 4 8.8.8.8
      ```

49. `traceroute` (o `tracepath`) — ruta hacia un host (muestra saltos).

    - Ejemplo:

      ```bash
      traceroute example.com
      # o tracepath example.com (sin necesidad de permisos)
      ```

50. `ssh` — acceso remoto seguro.

    - Ejemplos:

      ```bash
      ssh usuario@servidor.com
      ssh -p 2222 -i ~/.ssh/id_rsa usuario@servidor
      ```

51. `scp` — copiar archivos vía SSH (simple).

    - Ejemplo:

      ```bash
      scp archivo.txt usuario@host:/ruta/destino/
      scp -r carpeta/ usuario@host:/ruta/
      ```

52. `rsync` — sincronizar carpetas (eficiente, resumible).

    - Muy útil para backups. `--dry-run` para probar.
    - Ejemplo:

      ```bash
      rsync -avz --progress /local/dir/ usuario@host:/remote/dir/
      rsync -av --delete --dry-run src/ dest/   # prueba antes de ejecutar
      ```

53. `curl` — transferencias y pruebas HTTP (muy versátil).

    - Ejemplos:

      ```bash
      curl -I https://example.com    # sólo cabeceras
      curl -o archivo.tar.gz https://example.com/file.tar.gz
      curl -sS https://api.example.com | jq .
      ```

54. `wget` — descargar archivos desde la web (básico).

    - Ejemplo con reanudación:

      ```bash
      wget -c https://example.com/archivo.iso
      ```

### Gestión de paquetes, tareas y ayuda (55–60)

55. `apt` — gestor de paquetes en Debian/Ubuntu (ejemplo).

    - Ejemplos:

      ```bash
      sudo apt update
      sudo apt install nginx
      sudo apt upgrade
      ```

    - Nota: en otras distros usa `dnf` (Fedora), `pacman` (Arch), `zypper`, etc.

56. `dnf` — gestor de paquetes en Fedora/RedHat (ejemplo).

    - Ejemplo:

      ```bash
      sudo dnf install vim
      ```

57. `crontab` — programar tareas en crontab.

    - Editar crontab: `crontab -e`
    - Ejemplo: ejecutar script cada 5 minutos:

      ```
      */5 * * * * /home/usuario/backup.sh
      ```

58. `man` — manual de páginas (ayuda detallada).

    - Ejemplo:

      ```bash
      man ls
      ```

    - Consejo: si `man` no existe para un comando, prueba `--help` (ej. `ls --help`).

59. `history` — historial de comandos (útil con `!n` para ejecutar).

    - Ejemplos:

      ```bash
      history | grep rsync
      !123    # vuelve a ejecutar el comando número 123 del history
      ```

60. `dd` — copiar/construir imágenes a bajo nivel (muy potente y peligroso).

    - Usos: crear imágenes, clonar discos, pruebas de rendimiento. **Extrema precaución**: `dd` puede borrar discos si apuntas al dispositivo errado.
    - Ejemplo seguro (crear archivo de 100 MB):

      ```bash
      dd if=/dev/zero of=/tmp/test.img bs=1M count=100 status=progress
      ```

    - Ejemplo clonado (NO ejecutar sin verificar `if` y `of`):

      ```bash
      sudo dd if=/dev/sda of=/dev/sdb bs=4M status=progress
      ```

### Consejos finales y buenas prácticas

- Siempre prueba con `--help` o `man` antes de ejecutar comandos peligrosos: `command --help` o `man command`.
- Para operaciones de borrado/recursivas, usa primero una versión **no destructiva** (ej. `ls` o `--dry-run` si está disponible).
  Ej.: `rsync --dry-run ...` antes de sincronizar en serio.
- Usa redirección y tuberías para combinar comandos poderosos:

  ```bash
  journalctl -u nginx | grep -i error | less
  ```

- Aprende a combinar `find`, `xargs`, `grep`, `awk`, y `sed`: son la base de administración avanzada en shell.
- Practica en un entorno seguro (máquina virtual, contenedor, o directorio de pruebas) antes de ejecutar comandos como `rm -rf`, `dd`, `chmod -R`, `chown -R`.

---

[🔼](#índice)

---

## **62. Los 40 principales comandos de Windows que debes conocer**

### Navegación y gestión de archivos (1–12)

1. **`dir`** — Lista archivos y carpetas del directorio actual.

   - Opciones útiles: `/A` (atributos), `/O` (orden), `/S` (subdirectorios), `/B` (formato simple).
   - Ejemplo:

     ```bat
     dir C:\Users\Gustavo /A /O:-D /S
     ```

     Lista todo en `C:\Users\Gustavo`, incluyendo archivos ocultos, ordenado por fecha descendente, recursivo.

2. **`cd` / `chdir`** — Cambiar directorio.

   - `cd ..` sube un nivel. `cd \` va a la raíz. Para cambiar de unidad usa `/d`.
   - Ejemplo:

     ```bat
     cd /d D:\Proyectos\MiApp
     ```

3. **`md` / `mkdir`** — Crear directorio(s).

   - Ejemplo:

     ```bat
     mkdir "C:\Proyectos\Mi App\Temp"
     ```

4. **`rd` / `rmdir`** — Eliminar directorio.

   - Opciones: `/S` eliminar todo el árbol, `/Q` modo silencioso (sin confirmación).
   - Ejemplo (peligroso si te equivocas):

     ```bat
     rmdir /S /Q "C:\Proyectos\Old"
     ```

5. **`del` / `erase`** — Eliminar archivos.

   - Opciones: `/F` forzar, `/Q` silencioso, `/S` borrar en subcarpetas.
   - Ejemplo:

     ```bat
     del /F /Q C:\Temp\*.log
     ```

6. **`copy`** — Copiar archivos simples.

   - `/Y` sobrescribir sin preguntar. `copy con archivo.txt` es una forma obsoleta de crear un archivo desde consola.
   - Ejemplo:

     ```bat
     copy /Y C:\docs\informe.docx D:\backup\
     ```

7. **`xcopy`** — Copias recursivas (antiguo pero aún útil).

   - Opciones: `/E` (todas las carpetas incluyendo vacías), `/H` (archivos ocultos), `/C` (continúa en error), `/I` (asume destino carpeta).
   - Ejemplo:

     ```bat
     xcopy "C:\Web\site" "D:\backup\site" /E /H /C /I
     ```

8. **`robocopy`** — Copias robustas y sincronización (recomendado para backups).

   - Opciones: `/MIR` (mirror), `/Z` (modo reiniciable), `/R:3` (reintentos), `/W:5` (espera), `/LOG:archivo.log`.
   - Ejemplo:

     ```bat
     robocopy "C:\Datos" "E:\Backup\Datos" /MIR /Z /R:3 /W:5 /LOG:C:\logs\robocopy.log
     ```

9. **`move`** — Mover o renombrar archivos y carpetas.

   - Ejemplo:

     ```bat
     move "C:\temp\reporte.txt" "D:\archivos\reporte_archivado.txt"
     ```

10. **`ren`** — Renombrar archivos.

    - Ejemplo (renombrar extensión):

      ```bat
      ren *.txt *.bak
      ```

11. **`type`** — Muestra el contenido de un archivo de texto.

    - Ejemplo combinado:

      ```bat
      type notas.txt | more
      ```

12. **`more`** — Paginador simple para leer archivos largos.

    - Ejemplo:

      ```bat
      more longlog.txt
      ```

### Buscar y procesar texto / comparación (13–16)

13. **`find` / `findstr`** — Buscar texto en archivos. `findstr` es más potente (regex, recursivo).

    - Ejemplo `findstr`:

      ```bat
      findstr /S /I /N "ERROR" C:\Logs\*.log
      ```

      Busca "ERROR" sin distinguir mayúsculas, muestra número de línea y recorre subcarpetas.

14. **`attrib`** — Ver/establecer atributos (Read-only, Hidden, System).

    - Ejemplo:

      ```bat
      attrib +h +s "C:\Users\Gustavo\secret.txt"
      attrib -h -s "C:\Users\Gustavo\secret.txt"
      ```

15. **`fc`** — Comparar archivos (texto/binario).

    - Ejemplo:

      ```bat
      fc /L archivo_viejo.txt archivo_nuevo.txt
      ```

      `/L` para comparación línea a línea.

16. **`where`** — Localiza ejecutables en `PATH`.

    - Ejemplo:

      ```bat
      where notepad
      where /R C:\Python *.py
      ```

### Procesos y administración de tareas (17–21)

17. **`tasklist`** — Lista procesos en ejecución (equivalente a `ps`).

    - Ejemplo:

      ```bat
      tasklist /svc /v
      ```

18. **`taskkill`** — Terminar procesos por PID o imagen.

    - Opciones: `/PID`, `/IM` (imagen), `/F` (forzar).
    - Ejemplo:

      ```bat
      taskkill /IM notepad.exe /F
      taskkill /PID 4321 /F
      ```

19. **`taskmgr`** — Abre el Administrador de tareas (GUI).

    - Ejemplo:

      ```bat
      taskmgr
      ```

20. **`sc`** — Control de servicios (query/start/stop/config).

    - Ejemplo:

      ```bat
      sc query wuauserv
      sc stop Spooler
      sc start Spooler
      sc config Spooler start= auto
      ```

      _Nota_: en `sc config` el espacio después del `=` es obligatorio.

21. **`net` (subcomandos)** — Utilidad multiuso (usuarios, uso de recursos, servicios simples).

    - Ejemplos:

      ```bat
      net user nuevoUser MiPass123 /add           # crear usuario local
      net user nombreUsuario                      # ver info
      net use Z: \\servidor\recurso /persistent:yes  # mapear unidad de red
      net share MiShare=C:\Carpeta /GRANT:USUARIO,FULL
      ```

### Red y conectividad (22–28)

22. **`ipconfig`** — Muestra y gestiona configuración IP.

    - Ejemplos:

      ```bat
      ipconfig /all
      ipconfig /release
      ipconfig /renew
      ipconfig /flushdns
      ```

23. **`ping`** — Comprobar conectividad ICMP.

    - Ejemplo:

      ```bat
      ping -n 4 8.8.8.8
      ```

24. **`tracert`** — Traza la ruta hasta un host (muestra saltos).

    - Ejemplo:

      ```bat
      tracert example.com
      ```

25. **`nslookup`** — Consultas DNS interactivas.

    - Ejemplos:

      ```bat
      nslookup example.com
      nslookup
      > server 8.8.8.8
      > set type=mx
      > example.com
      ```

26. **`netstat`** — Mostrar conexiones de red y puertos.

    - Ejemplo:

      ```bat
      netstat -ano | findstr :80
      netstat -abn   # muestra binario (requiere elevación)
      ```

27. **`route`** — Ver/editar tabla de rutas IP.

    - Ejemplo ver tabla:

      ```bat
      route print
      ```

    - Añadir ruta (ejemplo):

      ```bat
      route add 10.0.0.0 mask 255.255.255.0 192.168.1.1
      ```

28. **`telnet`** — (cliente telnet; puede no estar instalado) prueba de puertos TCP simple.

    - Ejemplo:

      ```bat
      telnet mail.example.com 25
      ```

      Útil para verificar conectividad a servicios (SMTP/IMAP/HTTP etc).

### Diagnóstico y sistema (29–35)

29. **`systeminfo`** — Información del sistema (SO, CPU, RAM, hotfixes).

    - Ejemplo:

      ```bat
      systeminfo
      ```

30. **`sfc`** — System File Checker: repara archivos de sistema. (Ejecutar como admin).

    - Ejemplo:

      ```bat
      sfc /scannow
      ```

31. **`chkdsk`** — Comprobar y reparar sistema de archivos en disco.

    - Opciones: `/F` (repara errores), `/R` (recupera sectores), `/X` (desmonta volumen).
    - Ejemplo (puede pedir reinicio):

      ```bat
      chkdsk C: /F /R
      ```

32. **`diskpart`** — Administración de discos (particiones). **Muy poderoso y peligroso.**

    - Flujo interactivo típico:

      ```bat
      diskpart
      DISKPART> list disk
      DISKPART> select disk 1
      DISKPART> list partition
      ```

    - Para scripts:

      ```bat
      diskpart /s script.txt
      ```

      _ADVERTENCIA:_ `diskpart` puede borrar discos si se usa mal.

33. **`format`** — Formatear volúmenes (borra datos).

    - Ejemplo:

      ```bat
      format E: /FS:NTFS /Q /V:USB_DISK
      ```

      _Peligro:_ destruye todo en la unidad.

34. **`driverquery`** — Lista drivers instalados.

    - Ejemplo:

      ```bat
      driverquery /v /fo list
      ```

35. **`powercfg`** — Gestión de energía (perfiles, hibernación, diagnóstico consumo).

    - Ejemplos:

      ```bat
      powercfg /list
      powercfg /hibernate off
      powercfg /energy C:\reports\energy.html
      ```

### Usuarios, políticas y registro (36–40)

36. **`gpupdate`** / **`gpresult`** — Políticas de grupo (Group Policy).

    - Forzar actualización:

      ```bat
      gpupdate /force
      ```

    - Ver resultados:

      ```bat
      gpresult /R
      ```

37. **`reg`** — Manipular el Registro desde consola (`reg query`, `reg add`, `reg delete`).

    - Ejemplo:

      ```bat
      reg query "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion" /v ProgramFilesDir
      reg add "HKCU\Software\MiApp" /v Modo /t REG_SZ /d "Produccion" /f
      ```

38. **`set` / `setx`** — Ver/establecer variables de entorno.

    - `set` afecta la sesión actual; `setx` es persistente (para futuras sesiones).
    - Ejemplos:

      ```bat
      set PATH
      setx PATH "%PATH%;C:\Herramientas\bin"
      ```

      _Nota:_ `setx` no actualiza la variable en la consola actual.

39. **`certutil`** — Herramienta para certificados y manejo de archivos (hashes, etc.).

    - Ejemplo hash:

      ```bat
      certutil -hashfile C:\iso\imagen.iso SHA256
      ```

    - También puede descargar/cargar caché de URL, manipular certificados.

40. **`wmic`** (Windows Management Instrumentation Command) / **`powershell`** — Consultas WMI y shell avanzado.

    - `wmic` (en versiones antiguas):

      ```bat
      wmic bios get serialnumber
      wmic product get name,version
      ```

      _Nota:_ `wmic` está en desuso en favor de PowerShell (`Get-CimInstance`, `Get-WmiObject`).

    - **`powershell`** — Invoca PowerShell desde CMD o ejecuta scripts:

      ```bat
      powershell -Command "Get-Process | Sort-Object CPU -Descending | Select-Object -First 10"
      powershell -ExecutionPolicy Bypass -File C:\scripts\mi_script.ps1
      ```

### Consejos rápidos y buenas prácticas

- **Siempre ejecuta CMD/PowerShell como Administrador** cuando uses `sfc`, `chkdsk`, `diskpart`, `sc config`, `reg add`, etc. (botón derecho → _Run as administrator_).
- Antes de borrar o formatear, **verifica rutas**. Comandos como `del /S /Q`, `rmdir /S /Q`, `format` y `diskpart` son potencialmente destructivos.
- Para tareas programadas usa `schtasks` (ej.: `schtasks /create /sc daily /tn "Backup" /tr "C:\backup.bat" /st 02:00`) o el Programador de tareas GUI.
- Si necesitas automatizar o hacer consultas avanzadas, **PowerShell** ofrece cmdlets más potentes y seguros que muchos comandos legacy.
- Si no estás seguro de los parámetros, usa `/h`, `/?` o `help` (ej.: `robocopy /?`, `net help user`).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 11**                    |
| ------------------ | ------------------------------- |
| [🏠](../README.md) | [⏪](./2_11_Troubleshooting.md) |
