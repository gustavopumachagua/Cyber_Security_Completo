| **Inicio**         | **atrás 3**        | **Siguiente 5**           |
| ------------------ | ------------------ | ------------------------- |
| [🏠](../README.md) | [⏪](./5_3_FTP.md) | [⏩](./5_5_HTTP_HTTPS.md) |

---

## **Índice**

| Temario                                                                                                                                                |
| ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [139. ¿Qué es SFTP?](#139-qué-es-sftp)                                                                                                                 |
| [140. Cómo usar comandos SFTP para copiar archivos hacia desde un servidor](#140-cómo-usar-comandos-sftp-para-copiar-archivos-hacia-desde-un-servidor) |

# **SFTP**

## **139. ¿Qué es SFTP?**

![SFTP](/img/5_Network_Topologies/SFTP.webp "SFTP")

### 🔹 ¿Qué es **SFTP**?

**SFTP (SSH File Transfer Protocol o Secure File Transfer Protocol)** es un protocolo de red que permite **transferir archivos de forma segura** entre un cliente y un servidor.

👉 Aunque su nombre se parece a “FTP”, **no es FTP**.

- FTP funciona con puertos 21/20 y no cifra nada (salvo con FTPS).
- **SFTP funciona sobre SSH (Secure Shell)**, normalmente en el **puerto 22**, y cifra tanto la autenticación como los datos transferidos.

Es decir, con SFTP:

- El usuario se autentica de forma segura (usuario/contraseña o claves SSH).
- Toda la sesión (comandos y archivos) va **cifrada** punto a punto.

### 🔹 Protocolo seguro de transferencia de archivos

#### Características de seguridad:

- 🔒 **Cifrado extremo a extremo** → evita que alguien intercepte credenciales o archivos.
- 👤 **Autenticación fuerte** → puede usar usuario+contraseña o claves SSH con passphrase.
- ✅ **Integridad de datos** → detecta manipulaciones durante la transmisión.
- 🔐 **Gestión de permisos** → aprovecha la seguridad de SSH y el sistema de archivos del servidor.

👉 Ejemplo: si subes un archivo con **FTP**, cualquiera en la red podría espiar tu contraseña. Con **SFTP**, toda la comunicación está cifrada, como si viajaran en un “túnel seguro”.

### 🔹 Gestión de transferencias seguras de archivos

SFTP se usa mucho en **empresas, servidores web, sistemas cloud y automatización de backups**.

Permite no solo subir y descargar archivos, sino también:

- Crear/eliminar directorios.
- Listar ficheros con permisos y fechas.
- Reanudar transferencias interrumpidas.
- Sincronizar carpetas (con clientes avanzados).

#### Ejemplos de uso:

1. **Conexión básica desde terminal**:

```bash
sftp usuario@servidor.com
Password: *****
sftp> ls
sftp> cd /var/www/html
sftp> put index.html
sftp> get reporte.pdf
sftp> exit
```

2. **Con autenticación por clave SSH (más seguro que contraseña)**:

```bash
sftp -i ~/.ssh/id_rsa usuario@servidor.com
```

3. **Automatización con `lftp` (Linux)**:

```bash
lftp -u usuario,contraseña sftp://servidor.com
lftp> mirror -R /local/backup /remote/backup
```

4. **Clientes gráficos**:

- **WinSCP** (Windows) → subir/descargar con arrastrar y soltar.
- **FileZilla** (Windows/Linux/macOS) → soporta SFTP además de FTP/FTPS.
- **Cyberduck** (macOS/Windows).

### 🔹 Beneficios de usar SFTP

✅ Seguridad: cifrado completo de extremo a extremo.

✅ Simplicidad: un único puerto (22), no hay líos de puertos activos/pasivos como en FTP.

✅ Compatibilidad: soportado por casi todos los servidores y clientes modernos.

✅ Automatización: fácil de usar en scripts para sincronizar y hacer backups.

✅ Integración con SSH: se aprovecha la infraestructura de claves y permisos ya existente.

### 🔹 Ejemplo real

Imagina que tienes un servidor en la nube para tu sitio web.
Con SFTP puedes:

- Conectarte desde tu laptop con `FileZilla`.
- Arrastrar y soltar tus archivos HTML y CSS.
- Saber que la conexión está cifrada y nadie puede interceptar tus credenciales ni tus archivos.

✅ En resumen:

- **SFTP = protocolo seguro de transferencia de archivos sobre SSH.**
- Garantiza confidencialidad, integridad y autenticación fuerte.
- Es la opción recomendada frente a FTP/FTPS en la mayoría de escenarios actuales.

---

[🔼](#índice)

---

## **140. Cómo usar comandos SFTP para copiar archivos hacia desde un servidor**

### 1) Qué es SFTP (rápido)

**SFTP** (SSH File Transfer Protocol) es el protocolo de transferencia de archivos que corre sobre SSH: cifra la conexión (autenticación + datos) y, por defecto, usa el puerto **22**. Es la forma recomendada para mover archivos de forma segura.

### 2) Conectar: formas básicas

#### Con contraseña (modo interactivo)

```bash
sftp usuario@servidor.example.com
# luego en el prompt:
sftp> ls
sftp> cd /ruta/remota
sftp> lcd ~/Descargas
sftp> get archivo_remoto.zip
sftp> put archivo_local.txt
sftp> bye
```

> `sftp usuario@host` abre una sesión interactiva parecida a `ftp` pero cifrada.

#### Con clave SSH (sin pedir contraseña cada vez)

1. Genera clave en cliente:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519
```

2. Copia la pública al servidor:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub usuario@servidor.example.com
```

3. Conecta:

```bash
sftp usuario@servidor.example.com
```

También puedes indicar la clave en la línea de comando (o mejor: en `~/.ssh/config`):

```bash
sftp -oIdentityFile=~/.ssh/id_ed25519 usuario@servidor.example.com
```

Usar `ssh-agent` para cargar la clave evita tener que escribir el passphrase cada vez.

### 3) Comandos SFTP esenciales (interactivo)

- `ls` — listar directorio remoto
- `lls` — listar directorio local
- `cd <ruta>` — cambiar directorio remoto
- `lcd <ruta>` — cambiar directorio local
- `pwd` / `lpwd` — ver ruta remota / local
- `get <remoto> [local]` — descargar archivo remoto a local.
- `put <local> [remoto]` — subir archivo local al remoto.
- `get -r <dir>` / `put -r <dir>` — **recursivo** (copiar directorios).
- `reget <archivo>` / `reput <archivo>` — **reanudar** una descarga/subida interrumpida.
- `chmod <modo> <archivo>` — cambiar permisos remotos.
- `bye` / `exit` — cerrar sesión.

Estos comandos están soportados por el cliente `sftp` (OpenSSH); las opciones `-r`, `reget`/`reput` y preservación (`-p`) permiten copiar directorios y reanudar transferencias.

### 4) Ejemplos concretos paso a paso

#### 4.1 Descargar un solo archivo (interactivo)

```bash
sftp [email protected]
# en el prompt sftp>
sftp> cd /backups/dia
sftp> lcd ~/Downloads
sftp> get copia.sql.gz
sftp> bye
```

#### 4.2 Subir un archivo

```bash
sftp [email protected]
sftp> lcd ~/proyecto/dist
sftp> cd /var/www/misitio
sftp> put bundle.zip
sftp> bye
```

#### 4.3 Descargar recursivamente un directorio

```bash
sftp [email protected]
sftp> get -r /var/logs/miaplicacion ./miaplicacion-logs
```

(`get -r` copia carpetas y ficheros dentro de ellas).

#### 4.4 Reanudar una transferencia grande (cuando se corta)

Si `get` se interrumpe, puedes reanudar:

```bash
sftp> reget huge_backup.tar.gz
# para subir reanudar
sftp> reput huge_backup.tar.gz
```

Estas órdenes reanudan desde el tamaño local ya descargado/subido.

#### 4.5 Ejecutar órdenes desde un script (batch mode)

Crea `comandos.sftp` con contenido:

```
lcd /home/gustavo/descargas
cd /incoming
get reporte_$(date +%Y%m%d).zip
bye
```

Y ejecútalo:

```bash
sftp -b comandos.sftp [email protected]
```

O usando here-doc:

```bash
sftp [email protected] << 'EOF'
lcd ~/Descargas
cd /incoming
get latest.zip
bye
EOF
```

El modo batch (`-b`) permite automatizar sin interacción (ideal con autenticación por clave).

### 5) Ejemplos «one-liners» y alternativas

- **Usar `scp`** (a veces más simple para un solo archivo):

```bash
scp -P 2222 [email protected]:/ruta/remota/archivo.tar.gz ./
```

- **Usar `rsync` sobre SSH** (sincronizaciones eficientes y reanudables — recomendado para mucha data):

```bash
rsync -avz -e "ssh -p 2222" [email protected]:/ruta/remota/ /ruta/local/
```

`rsync` solo transfiere diferencias (ahorra ancho de banda) y es ideal para copias periódicas.

### 6) Trabajar con puertos no estándar / alias de hosts

En vez de pasar opciones en la línea de comando, configura `~/.ssh/config`:

```text
Host mi-servidor
    HostName servidor.example.com
    User usuario
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
```

Luego solo:

```bash
sftp mi-servidor
```

Esto simplifica automatización y evita errores de sintaxis. (usar `ssh_config`/`~/.ssh/config` es buena práctica).

### 7) Automatización segura (por cron, Jenkins, etc.)

- Usa **autenticación por clave** (no contraseña).
- Añade la clave al `ssh-agent` o usa claves sin passphrase en máquinas controladas (con cuidado).
- Usa `sftp -b` o here-docs dentro de scripts shell.
- Para transferencias fiables / diferenciales usa `rsync -e ssh`.
  Consejo: **no** pongan contraseñas en texto plano en scripts: mejor claves + `ssh-agent` o vaults.

### 8) GUI / Windows

Si prefieres GUI: **WinSCP**, **FileZilla** o **Cyberduck** son opciones populares; en Windows moderno también está disponible el cliente `sftp`/OpenSSH en PowerShell (Windows 10/11).

### 9) Errores comunes y soluciones rápidas

- **Permission denied** → las claves no están en `~/.ssh/authorized_keys` o permisos incorrectos.
- **Connection refused** → puerto/servicio SSH no accesible o firewall.
- **Host key verification failed** → la huella del servidor cambió; revisa `~/.ssh/known_hosts`.
- **No such file or directory** → ruta remota equivocada; usa `ls`/`pwd` para confirmar.
- **Transferencias lentas / interrumpidas** → probar `reget`/`reput`, `rsync` o aumentar `ssh` `ServerAliveInterval`.

### 10) Resumen práctico (todo en 1 vista)

- Abrir sesión: `sftp usuario@host` (o `sftp mi-servidor` con `~/.ssh/config`).
- Descargar: `get archivo` — subir: `put archivo`.
- Directorios: `get -r dir` / `put -r dir` (recursivo).
- Reanudar: `reget` / `reput`.
- Automatizar: `sftp -b batchfile` o here-doc + autenticación por clave.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**        | **Siguiente 5**           |
| ------------------ | ------------------ | ------------------------- |
| [🏠](../README.md) | [⏪](./5_3_FTP.md) | [⏩](./5_5_HTTP_HTTPS.md) |
