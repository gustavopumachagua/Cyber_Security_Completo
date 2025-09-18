| **Inicio**         | **Siguiente 2**            |
| ------------------ | -------------------------- |
| [🏠](../README.md) | [⏩](./12_2_SSL_vs_TLS.md) |

---

## **Índice**

| Temario                                                              |
| -------------------------------------------------------------------- |
| [358. FTP definido y explicado](#358-ftp-definido-y-explicado)       |
| [359. Cómo utilizar comandos SFTP](#359-cómo-utilizar-comandos-sftp) |

# **File Transfer Protocol (FTP) vs Secure File Transfer Protol (SFTP)**

## **358. FTP definido y explicado**

![FTP vs SFTP](/img/12_Secure_vs_Unsecure_Protocols/FTP_vs_SFTP.png "FTP vs SFTP")

### 📌 ¿Qué es FTP?

FTP (File Transfer Protocol) es un protocolo de red que permite **transferir archivos entre un cliente y un servidor** a través de una red TCP/IP (como Internet o una red local). Fue desarrollado en los años 70 y es uno de los protocolos más antiguos de Internet.

### 📌 ¿Para qué sirve el FTP?

Se usa principalmente para:

- Subir archivos desde un ordenador a un servidor (ej. publicar una web).
- Descargar archivos desde un servidor a un ordenador.
- Administrar archivos en un servidor remoto (crear, borrar, renombrar, mover).

Ejemplo: si tienes un hosting, puedes conectarte vía FTP para subir el código de tu página web.

#### 📌 ¿Cuántos tipos de FTP existen?

Se clasifican según cómo manejan la seguridad:

1. **FTP estándar** → Sin cifrado (todo viaja en texto plano).
2. **FTPS (FTP Secure)** → Añade cifrado SSL/TLS.
3. **SFTP (SSH File Transfer Protocol)** → No es realmente FTP, sino parte de SSH. Es más seguro y moderno.

### 📌 Cómo usar FTP

Tienes dos formas:

- **Cliente FTP**: programas como **FileZilla**, **WinSCP** o **Cyberduck**.
- **Terminal/Consola**: comandos como `ftp servidor.com`.

Ejemplo en consola:

```bash
ftp ftp.ejemplo.com
Usuario: miusuario
Contraseña: ********
ftp> put archivo.txt   # Subir archivo
ftp> get archivo.txt   # Descargar archivo
ftp> bye               # Salir
```

### 📌 ¿Qué es un puerto FTP?

FTP usa por defecto:

- **Puerto 21** → Control de la conexión (usuario, contraseña, comandos).
- **Puerto 20** → Transferencia de datos (en modo activo).

Se puede cambiar para seguridad o necesidades específicas.

### 📌 FTP frente a SFTP

- **FTP**: Sin cifrado, usa puertos 20 y 21.
- **SFTP**: Seguro, usa **un solo puerto (22)** bajo SSH, cifrando datos y credenciales.

  👉 **SFTP es mucho más seguro.**

### 📌 FTP frente a HTTP

- **FTP**: Diseñado para transferir archivos grandes, permite listar carpetas y manejar directorios.
- **HTTP**: Diseñado para transmitir páginas web, pero también puede transferir archivos.

  👉 HTTP es más usado hoy en día, pero FTP sigue siendo útil para administración de servidores.

### 📌 FTP frente a MFT

- **MFT (Managed File Transfer)**: Es una solución empresarial que mejora FTP/SFTP, agregando:

  - Automatización de transferencias.
  - Auditoría y registros.
  - Seguridad avanzada.

    👉 MFT es una **evolución empresarial** del FTP/SFTP.

### 📌 Cómo cambiar los números de puerto FTP

Depende del servidor:

- En **vsftpd (Linux)**: editar `/etc/vsftpd.conf` → `listen_port=2121`.
- En **IIS (Windows Server)**: en la configuración del sitio FTP.
- En clientes (FileZilla, etc.) → al conectar puedes especificar el puerto.

### 📌 Desafíos de seguridad del FTP

- Credenciales viajan en **texto plano**.
- Vulnerable a sniffing, spoofing, ataques MITM.
- No garantiza integridad de datos.
- No cumple con regulaciones modernas (GDPR, HIPAA).

👉 Se recomienda **usar SFTP o FTPS**.

### 📌 Preguntas frecuentes sobre FTP

#### 🔹 ¿Cómo funciona FTP?

Funciona en un modelo **cliente-servidor**:

1. Cliente se conecta al servidor por el puerto 21.
2. El servidor solicita autenticación.
3. Se establece un canal de control y uno de datos.
4. El cliente envía comandos (`LIST`, `GET`, `PUT`).
5. El servidor responde y transfiere archivos.

#### 🔹 ¿Qué es más seguro FTP o SFTP?

👉 **SFTP**. Porque cifra todo el tráfico (datos, contraseñas, comandos).

#### 🔹 ¿Cuáles son los tipos de protocolo de transferencia de archivos?

- **FTP**: básico, inseguro.
- **FTPS**: FTP + SSL/TLS.
- **SFTP**: sobre SSH, seguro y moderno.
- **TFTP (Trivial FTP)**: ligero, sin autenticación ni cifrado.
- **HTTP/HTTPS**: también se usan para transferencias.
- **MFT**: transferencia gestionada empresarial.

---

[🔼](#índice)

---

## **359. Cómo utilizar comandos SFTP**

### 1) Conectarse por SFTP

**Con contraseña (puerto por defecto 22):**

```bash
sftp usuario@ejemplo.com
# Te pedirá la contraseña:
# usuario@ejemplo.com's password:
```

**Con puerto no estándar (ej. 2222):**

```bash
sftp -P 2222 usuario@ejemplo.com
```

**Usando un archivo de identidad (clave privada) sin pedir contraseña cada vez** (recomendado):

- Opción 1: configura `~/.ssh/config`

```text
Host mi-servidor
  HostName ejemplo.com
  User usuario
  Port 2222
  IdentityFile ~/.ssh/id_ed25519
```

Luego:

```bash
sftp mi-servidor
```

- Opción 2: pasar la identidad en línea:

```bash
sftp -oIdentityFile=~/.ssh/id_ed25519 -P 2222 usuario@ejemplo.com
```

### 2) Iniciar una sesión: ejemplo de transcript

```bash
$ sftp gustavo@ejemplo.com
gustavo@ejemplo.com's password:
Connected to ejemplo.com.
sftp> pwd
Remote working dir: /home/gustavo
sftp> lpwd
Local working dir: /home/gustavo/mi_pc
sftp> ls -l
-rw-r--r--    1 gustavo  group       1024 Jan 01 12:00 index.html
sftp> cd public_html
sftp> put index.html
Uploading index.html to /home/gustavo/public_html/index.html
index.html                                            100%   1KB   10.0KB/s   00:00
sftp> get logs/error.log
Fetching /home/gustavo/logs/error.log to error.log
sftp> chmod 644 index.html
sftp> bye
```

### 3) Comandos básicos (interactivo)

Dentro de `sftp>`:

- `help` o `?` — muestra ayuda.
- `ls` — lista archivos remotos.
- `lls` — lista archivos locales.
- `cd <dir>` — cambia directorio remoto.
- `lcd <dir>` — cambia directorio local.
- `pwd` / `lpwd` — muestra directorios actuales remoto / local.
- `get <archivo>` — descargar archivo remoto al local.
- `put <archivo>` — subir archivo local al remoto.
- `get -r <dir>` — descargar directorio recursivamente.
- `put -r <dir>` — subir directorio recursivamente.
- `reget <archivo>` — reanudar descarga interrumpida.
- `reput <archivo>` — reanudar subida interrumpida.
- `mkdir <dir>` — crear directorio remoto.
- `rmdir <dir>` / `rm <archivo>` — eliminar remoto.
- `rename <viejo> <nuevo>` — renombrar remoto.
- `chmod <modo> <archivo>` — cambiar permisos (si el servidor lo permite).
- `ln` / `symlink` — enlace simbólico (depende del servidor).
- `quit` / `bye` — salir.

Ejemplo: subir todo el sitio `build/`:

```bash
sftp> lcd ~/proyecto/build
sftp> cd public_html
sftp> put -r .
```

### 4) Reanudar transferencias

Si una transferencia se interrumpe:

- Para reanudar descarga:

```bash
sftp> reget archivo_grande.tar.gz
```

- Para reanudar subida:

```bash
sftp> reput archivo_grande.tar.gz
```

(Soportado por el cliente `sftp` de OpenSSH; si no funciona, usa `rsync` sobre SSH.)

### 5) Automatizar / Modo batch

Puedes ejecutar una serie de comandos sin entrar en modo interactivo con `-b` (batch):

**Archivo `batch.txt`:**

```
lcd /home/gustavo/build
cd /var/www/html
put -r .
bye
```

**Ejecutarlo:**

```bash
sftp -b batch.txt usuario@ejemplo.com
```

**Aquí-document (stdin):**

```bash
sftp -b - usuario@ejemplo.com <<'EOF'
lcd /home/gustavo/build
cd /var/www/html
put -r .
bye
EOF
```

### 6) Uso en scripts (sin contraseña)

Para automatizar sin interacción, lo ideal es **autenticación por clave SSH** (sin passphrase o con `ssh-agent`). Evita poner contraseñas en texto plano. Ejemplo crontab:

```bash
0 2 * * * sftp -b /home/gustavo/scripts/backup.batch backup@backup.ejemplo.com
```

### 7) Opciones útiles del comando `sftp`

- `-P <puerto>` — puerto SSH (mayúscula P).
- `-b <archivo>` — batch mode (lee comandos desde archivo).
- `-o <opción>` — pasa opciones de SSH, p. ej. `-oIdentityFile=~/.ssh/id_ed25519`.
- `-v`, `-vv` — modo verbose (depuración).

### 8) Consejos de integridad y atributos

- Para preservar **fechas y permisos** al transferir:

  ```bash
  sftp> get -p remoto.txt    # -p preserva timestamps y modes
  sftp> put -p local.txt
  ```

- Para verificar integridad, calcula checksums antes/después:

  ```bash
  # en local
  sha256sum archivo.tar.gz > archivo.sha256
  # subir ambos y comprobar en remoto (o descargar y comprobar localmente)
  ```

### 9) Problemas comunes y cómo resolverlos

- **Permission denied (publickey)** → servidor exige clave pública; añade tu `.pub` a `~/.ssh/authorized_keys` o usa `ssh-copy-id`.
- **Connection refused** → puerto SSH cerrado o cortafuegos; confirma puerto y que `sshd` esté corriendo.
- **Host key verification failed** → cambió la clave del servidor. Si es intencional, elimina la línea antigua en `~/.ssh/known_hosts` o usa `ssh-keygen -R ejemplo.com` y vuelve a conectar.
- **Transferencias lentas** → revisar ancho de banda, limitaciones del servidor, o usar `rsync -e ssh` para optimizar diferenciales.
- **Comandos no permitidos (p.ej. chown)** → puede que el servidor sftp esté restringido (chroot o sftp-server limitado).

### 10) Buenas prácticas de seguridad

- Usa **autenticación por clave SSH** (ed25519 o RSA 4096).
- Deshabilita autenticación por contraseña en `sshd_config` (`PasswordAuthentication no`) si es posible.
- Usa `sftp` dentro de usuarios chrooted para restringir acceso.
- Habilita registro/auditoría en servidor (auditd, syslog).
- No uses SFTP en servidores expuestos sin firewall/IDS/monitoring.
- Verifica integridad (hashes) para transferencias críticas.

### 11) Comparación rápida (cuando elegir SFTP)

- **SFTP vs FTP**: SFTP cifra todo (credenciales y datos) y va sobre SSH → **más seguro**.
- **SFTP vs SCP**: SFTP es interactivo, permite listar, reanudar, transferencias recursivas con mejor control. SCP es útil para copias simples.
- **SFTP vs rsync+ssh**: `rsync` es mejor cuando quieres sincronizar diferencias (eficiente en ancho de banda).

### 12) Cheat-sheet rápido (comandos más usados)

```text
Conexión:
  sftp usuario@host
  sftp -P 2222 usuario@host
  sftp -oIdentityFile=~/.ssh/id_ed25519 usuario@host

Navegación:
  ls, lls       # listar remoto / local
  cd <dir>, lcd <dir>
  pwd, lpwd

Transferencias:
  get remoto    # descargar
  get -r dir    # descargar recursivo
  put local     # subir
  put -r dir    # subir recursivo
  get -p, put -p  # preservar timestamps y permisos
  reget / reput # reanudar

Gestión:
  mkdir, rmdir, rm, rename, chmod

Batch / scripts:
  sftp -b archivo usuario@host
  sftp -b - usuario@host <<EOF ... EOF

Ayuda / salida:
  help  o  ?
  bye / quit
```

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**            |
| ------------------ | -------------------------- |
| [🏠](../README.md) | [⏩](./12_2_SSL_vs_TLS.md) |
