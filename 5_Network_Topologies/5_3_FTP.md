| **Inicio**         | **atrás 2**        | **Siguiente 4**     |
| ------------------ | ------------------ | ------------------- |
| [🏠](../README.md) | [⏪](./5_2_RDP.md) | [⏩](./5_4_SFTP.md) |

---

## **Índice**

| Temario                                                                                    |
| ------------------------------------------------------------------------------------------ |
| [136. Protocolo de transferencia de archivos](#136-protocolo-de-transferencia-de-archivos) |
| [137. Significado y usos de FTP](#137-significado-y-usos-de-ftp)                           |
| [138. ¿Qué es FTP?](#138-qué-es-ftp)                                                       |

# **FTP**

## **136. Protocolo de transferencia de archivos**

![FTP](/img/5_Network_Topologies/FTP.png "FTP")

### ¿Qué es FTP?

**FTP (File Transfer Protocol)** es un protocolo clásico para transferir archivos entre un cliente y un servidor a través de una red TCP/IP. Fue diseñado para subir, bajar y listar archivos/directorios en servidores remotos.

Es muy simple y ampliamente soportado, pero **por defecto no cifra** ni la autenticación ni los datos, por lo que no es seguro para usar directamente sobre Internet sin medidas adicionales.

### Conceptos clave

- **Control channel**: conexión TCP que gestiona comandos (por defecto puerto **21**).
- **Data channel**: conexión TCP usada para transferir los archivos o listados. En FTP “tradicional” puede usar puerto **20** (modo activo) o un puerto efímero (modo pasivo).
- **Modos:** **Activo** vs **Pasivo** — afectan quién abre la conexión de datos (servidor o cliente).
- **Tipos de transferencia:** `ASCII` (con conversión de finales de línea) y `binary` (sin conversión).
- **FTPS** = FTP sobre TLS/SSL (añade cifrado).
- **SFTP** = Subprotocolo de SSH para transferencias (no es FTP, usa SSH y es la opción más segura y moderna).

### ¿Cómo funciona (resumen técnico)?

#### Modo **Activo**

1. Cliente abre control a `servidor:21`.
2. Cliente envía `PORT a,b,c,d,p1,p2` indicando la IP y puerto donde escucha.
3. Servidor abre conexión desde su puerto **20** al puerto informado por el cliente para el **data channel**.

   Problema: no funciona bien cuando el cliente está detrás de NAT/firewall (servidor no puede conectar al cliente).

#### Modo **Pasivo**

1. Cliente abre control a `servidor:21`.
2. Cliente envía `PASV`.
3. Servidor responde con la IP y puerto efímero: `(h1,h2,h3,h4,p1,p2)` (p = p1\*256 + p2).
4. Cliente conecta desde su lado al puerto informado del servidor para el **data channel**.

   Ventaja: funciona detrás de NAT en el cliente; es el modo más usado hoy en día.

> Nota: el formato `(h1,h2,h3,h4,p1,p2)` es cómo el servidor devuelve la IP y puerto en la respuesta PASV.

### Comandos básicos de un cliente FTP (interactivo)

- `open host` — conectar.
- `user <usuario>` — iniciar sesión (o `open host usuario` depende del cliente).
- `ls` / `dir` — listar.
- `cd <dir>` — cambiar directorio remoto.
- `lcd <dir>` — cambiar directorio local.
- `get archivo` — descargar.
- `put archivo` — subir.
- `mget *.txt` / `mput` — múltiples archivos.
- `binary` — pasar a modo binario.
- `ascii` — modo texto.
- `quit` — salir.

### Ejemplos prácticos (cliente)

#### 1) Cliente interactivo (Linux/macOS/Windows `ftp`)

```bash
$ ftp ftp.ejemplo.com
Connected to ftp.ejemplo.com.
220 (vsFTPd 3.0)
Name (ftp.ejemplo.com:gustavo): miusuario
Password: ********
ftp> binary
ftp> cd /pub/
ftp> get manual.pdf
ftp> put informe.pdf
ftp> mget *.txt
ftp> quit
```

#### 2) `curl` (útil en scripts)

- Subir:

```bash
curl -T localfile.txt -u usuario:contraseña ftp://ftp.ejemplo.com/remote/dir/
```

- Descargar:

```bash
curl -u usuario:contraseña -O ftp://ftp.ejemplo.com/remote/archivo.zip
```

- FTPS (explicito TLS):

```bash
curl --ftp-ssl -u usuario:contraseña ftp://ftp.ejemplo.com/remote/archivo.zip
```

#### 3) `lftp` (muy potente y recomendable para automatizar y sincronizar)

- Conectar:

```bash
lftp -u usuario,contraseña ftp.ejemplo.com
```

- Descargar un directorio recursivamente:

```bash
lftp> mirror /remote/dir /local/dir
```

- Subir recursivamente:

```bash
lftp> mirror -R /local/dir /remote/dir
```

- Forzar modo pasivo:

```bash
set ftp:passive-mode true
```

#### 4) SFTP (NO es FTP, pero se usa como alternativa segura)

```bash
sftp usuario@ejemplo.com
sftp> get remoto.txt
sftp> put local.doc
sftp> exit
```

SFTP va sobre SSH (puerto 22) y cifra todo.

#### 5) Python (ftplib / FTP_TLS)

```python
# FTP simple (no cifrado)
from ftplib import FTP
ftp = FTP('ftp.ejemplo.com')
ftp.login('usuario','contraseña')
ftp.cwd('/pub')
with open('descarga.bin','wb') as f:
    ftp.retrbinary('RETR remoto.bin', f.write)
ftp.quit()

# FTPS (FTP sobre TLS)
from ftplib import FTP_TLS
ftps = FTP_TLS('ftp.ejemplo.com')
ftps.login('usuario','contraseña')
ftps.prot_p()  # proteger la conexión de datos
ftps.retrbinary('RETR remoto_seguro.bin', open('local.bin','wb').write)
ftps.quit()
```

### Ejemplo: configurar un servidor FTP simple con **vsftpd** (Ubuntu)

1. Instalar:

```bash
sudo apt update
sudo apt install vsftpd
```

2. Configuración mínima (`/etc/vsftpd.conf`):

```text
listen=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
chroot_local_user=YES
allow_writeable_chroot=YES

# Passive mode (importante si estás detrás de NAT)
pasv_enable=YES
pasv_min_port=40000
pasv_max_port=50000
pasv_address=203.0.113.10   # la IP pública si el servidor está tras NAT

# TLS (FTPS) - ejemplo básico
ssl_enable=YES
rsa_cert_file=/etc/ssl/private/vsftpd.pem
```

3. Abrir puertos en el firewall (ej. `ufw`):

```bash
sudo ufw allow 21/tcp
sudo ufw allow 40000:50000/tcp
sudo ufw reload
```

4. Reiniciar:

```bash
sudo systemctl restart vsftpd
```

5. Crear usuario y directorio:

```bash
sudo adduser ftpgustavo --home /srv/ftp/ftpgustavo --shell /usr/sbin/nologin
sudo chown ftpgustavo:ftpgustavo /srv/ftp/ftpgustavo
```

### Seguridad — riesgos de FTP y cómo mitigarlos

**Riesgos de usar FTP “plain”:**

- Credenciales y datos viajan en texto claro → **interceptables** (sniffing).
- Exposición de puertos y servicios puede permitir fuerza bruta o exploits.
- Problemas con NAT/Firewalls por el data channel.

**Mitigaciones y recomendaciones:**

1. **Evita FTP simple sobre Internet.** Para conexiones públicas, usar **SFTP (SSH)** o **FTPS (FTP+TLS)**.
2. **Usa FTPS explícito** (AUTH TLS) o FTPS implícito (puerto 990). Explícito (AUTH TLS) es más común hoy.
3. **Si usas FTP/FTPS, restringe la gama de puertos pasivos** y abre solo esos en el firewall.
4. **Chroot** a usuarios para que queden limitados a su directorio.
5. **MFA** si el servicio lo permite (en SFTP puedes añadir autenticación por claves + passphrase).
6. **Monitoreo**: logs, fail2ban para bloquear intentos repetidos, alertas.
7. **Certificados válidos** para FTPS para evitar warnings y MITM.
8. **No usar credenciales en URLs** en scripts visibles (p. ej. `ftp://user:pass@host`) sino usar mecanismos seguros o variables de entorno.
9. **Preferir SFTP** para la mayoría de casos: simple, funciona con NAT, cifra todo, administra usuarios SSH o pares de claves.

### Diferencias rápidas: FTP vs FTPS vs SFTP

- **FTP (plain)**: sin cifrado, puerto 21 (control).
- **FTPS (FTP+TLS)**: añade TLS. Puede ser **explícito** (STARTTLS / AUTH TLS en puerto 21) o **implícito** (puerto 990). Todavía mantiene control/data channels como FTP.
- **SFTP (SSH File Transfer Protocol)**: no tiene relación con FTP; es parte del subsystem de SSH (puerto 22). Cifra todo y es más fácil de usar en entornos modernos.

### Problemas comunes y soluciones rápidas

- **“425 Can't open data connection”** → firewall o modo activo/pasivo mal configurado → usa modo PASV y abre rango pasivo en servidor/firewall.
- **“530 Login incorrect”** → credenciales o usuario no autorizado; revisar grupo `ftpusers`/`chroot` y permisos.
- **Transferencias corruptas** → asegúrate de `binary` para archivos binarios (imágenes, zip, exe). ASCII puede corromper binarios.
- **Servidor detrás de NAT** → configurar `pasv_address` con IP pública y rango de puertos pasivos.

### Buenas prácticas (resumen)

- No uses FTP sin cifrado en redes no confiables.
- Usa **SFTP** o **FTPS**.
- Limita los puertos pasivos y documenta la configuración del firewall.
- Usa chroot y permisos mínimos.
- Habilita logging y bloqueos automáticos (fail2ban).
- Automatiza con `lftp`, `curl` o herramientas SFTP (WinSCP, rsync sobre SSH).

---

[🔼](#índice)

---

## **137. Significado y usos de FTP**

### 🔹 ¿Qué es el **Protocolo de Transferencia de Archivos (FTP)?**

El **FTP (File Transfer Protocol)** es un protocolo de red estándar que permite **transferir archivos entre un cliente y un servidor** sobre una red TCP/IP (como Internet o una LAN).
Fue diseñado en los años 70 y es uno de los protocolos más antiguos aún en uso.

👉 Ejemplo: con FTP puedes **subir archivos** de tu PC a un servidor web o **descargar documentos** de un servidor remoto.

### 🔹 ¿Cómo funciona el Protocolo de Transferencia de Archivos (FTP)?

FTP se basa en una **arquitectura cliente-servidor**:

- **Cliente FTP** → Envía comandos (ej. subir, descargar, listar directorios).
- **Servidor FTP** → Procesa los comandos y responde con archivos o resultados.

### Conexiones en FTP:

- **Canal de control (puerto 21 TCP)**: se usa para enviar comandos (`USER`, `PASS`, `LIST`, `RETR`, `STOR`).
- **Canal de datos**: se usa para transferir los archivos o listados. Puede funcionar en dos modos:

  - **Activo**: el servidor abre la conexión de datos hacia el cliente (puerto 20).
  - **Pasivo**: el cliente abre la conexión hacia un puerto que el servidor indica (mejor para NAT/firewalls).

👉 Ejemplo:

1. Cliente se conecta a `ftp.ejemplo.com:21`.
2. Envía `USER gustavo` y `PASS contraseña`.
3. Pide `LIST` → servidor abre canal de datos y devuelve listado de archivos.
4. Pide `RETR informe.pdf` → archivo se transfiere por el canal de datos.

### 🔹 Proceso de FTP

1. **Inicio de sesión** → cliente se conecta al servidor y se autentica (usuario/contraseña o anónimo).
2. **Navegación** → cliente lista carpetas, cambia de directorio.
3. **Transferencia** → cliente sube (`STOR`) o baja (`RETR`) archivos.
4. **Cierre de sesión** → se termina la conexión (`QUIT`).

### 🔹 Historia del FTP

- **1971**: Se publica la primera especificación en el RFC 114.
- **1980**: Se actualiza en RFC 765, mejorando comandos.
- **1985**: RFC 959 define el FTP moderno (el que aún usamos).
- **1990s**: Se añaden extensiones como FTPS (FTP + SSL/TLS).
- **Hoy**: Aunque FTP todavía se usa, ha sido desplazado en gran parte por **SFTP (SSH File Transfer Protocol)** porque cifra todo el tráfico.

### 🔹 Tipos de FTP

1. **FTP simple (sin cifrar)** → todo viaja en texto claro (inseguro).
2. **FTPS (FTP Secure)** → FTP con cifrado SSL/TLS (puerto 990 o 21 con AUTH TLS).
3. **SFTP (SSH File Transfer Protocol)** → no es FTP real, usa SSH (puerto 22), cifra todo y es más seguro.

### 🔹 Beneficios y usos del FTP

✅ **Beneficios**:

- Transferencia de archivos grandes entre equipos.
- Soporta reanudación de descargas interrumpidas.
- Permite múltiples usuarios y permisos.
- Amplio soporte en sistemas operativos y aplicaciones.

📌 **Usos típicos**:

- Subir páginas web a un servidor.
- Descargar software o actualizaciones de servidores públicos.
- Transferencia de archivos en empresas.
- Copias de seguridad remotas.

### 🔹 Ejemplo de clientes FTP

- **CLI (consola)**:

  - `ftp` (Windows/Linux/macOS)
  - `lftp` (Linux, muy avanzado)
  - `curl` con soporte FTP

- **Gráficos**:

  - **FileZilla** (multiplataforma, popular).
  - **WinSCP** (Windows, soporta FTP, FTPS y SFTP).
  - **Cyberduck** (Windows/macOS).

👉 Ejemplo usando `ftp` en Linux:

```bash
ftp ftp.ejemplo.com
Name: usuario
Password: ****
ftp> ls
ftp> get documento.pdf
ftp> put informe.txt
ftp> quit
```

### 🔹 ¿Qué buscar en un cliente FTP?

- Soporte para **FTPS y SFTP** (seguridad).
- Capacidad de **reanudar descargas/subidas**.
- Manejo de **transferencias múltiples** y en segundo plano.
- **Interfaz fácil de usar** (gráfica o scripting).
- Compatibilidad con tu sistema operativo.
- Funciones extra: sincronización, edición remota, automatización.

### 🔹 ¿FTP usa TCP o UDP?

FTP **usa TCP** (no UDP).

- **Puerto 21 TCP** → control.
- **Puerto 20 TCP o puertos efímeros** → datos (según modo activo/pasivo).
  Se usa TCP porque FTP necesita **confiabilidad** (no perder datos en la transferencia).

### 🔹 Ejemplo de FTP

Imagina que tienes un sitio web en un servidor. Para actualizarlo:

1. Abres **FileZilla**.
2. Conectas a `ftp.midominio.com` con tu usuario y contraseña.
3. Arrastras los nuevos archivos HTML y CSS desde tu PC al servidor.
4. El sitio se actualiza en línea automáticamente.

### 🔹 Resultado final

El **Protocolo de Transferencia de Archivos (FTP)** es un método clásico y ampliamente usado para mover archivos entre computadoras a través de una red. Funciona con un modelo cliente-servidor, usando el **puerto 21 TCP para control** y otro canal para datos (activo o pasivo).
Aunque es muy útil y todavía está presente en hosting, backups y entornos corporativos, **su versión sin cifrado es insegura**. Hoy en día se recomienda usar **FTPS o SFTP** como alternativas más seguras.

---

[🔼](#índice)

---

## **138. ¿Qué es FTP?**

FTP — **File Transfer Protocol** — es un protocolo de red estándar diseñado para **transferir archivos** entre un cliente y un servidor sobre una red TCP/IP (por ejemplo Internet o una LAN). Es uno de los protocolos más antiguos todavía en uso y su función principal es permitir **subir**, **descargar**, **listar** y **administrar** archivos remotos.

### ¿Para qué sirve?

- Subir páginas web, imágenes o binarios a un servidor remoto.
- Descargar ficheros desde un repositorio público o privado.
- Sincronizar carpetas entre máquinas.
- Automatizar backups y despliegues (con herramientas que soporten FTP).

### ¿Cómo funciona (a grandes rasgos)?

FTP usa una arquitectura **cliente — servidor** y **dos conexiones TCP**:

1. **Canal de control (TCP puerto 21)**

   - Se utiliza para enviar comandos y recibir respuestas (LOGIN, LIST, RETR, STOR, etc.).

2. **Canal de datos (puertos variables — 20 o efímeros)**

   - Se utiliza para transferir los archivos o los listados.

Por eso cuando usas FTP ves dos tipos de conexiones: una para control y otra para los datos reales.

### Modos: **Activo** vs **Pasivo** (por qué importa)

- **Activo**: el cliente escucha en un puerto y le dice al servidor “conéctate a este puerto para enviar datos”. El servidor inicia la conexión de datos desde su puerto 20.

  - Problema: el cliente suele estar detrás de NAT/firewall y el servidor no puede conectar al cliente.

- **Pasivo (PASV)**: el servidor abre un puerto efímero y le dice al cliente “conéctate aquí para la transferencia de datos”. El cliente inicia ambas conexiones (control y datos) — funciona mejor detrás de NAT/firewalls.

  - Respuesta típica del servidor al comando `PASV`:

    ```
    227 Entering Passive Mode (h1,h2,h3,h4,p1,p2)
    ```

    donde la IP es h1.h2.h3.h4 y el puerto = p1\*256 + p2.

### Tipos / variantes

- **FTP (plain)**: sin cifrado — credenciales y datos en texto claro (inseguro para Internet).
- **FTPS (FTP sobre TLS/SSL)**: añade cifrado mediante TLS (puede ser implícito en 990 o explícito via `AUTH TLS` en 21).
- **SFTP (SSH File Transfer Protocol)**: NO es FTP, es un protocolo distinto que funciona sobre SSH (puerto 22) y cifra todo — hoy es la opción recomendada frente a FTP plain.

### Comandos básicos (cliente interactivo)

- `open host` / `ftp host` — conectar
- `user <usuario>` — iniciar sesión
- `ls` / `dir` — listar directorio remoto
- `cd <dir>` — cambiar directorio remoto
- `lcd <dir>` — cambiar directorio local
- `get archivo` — descargar (RETR)
- `put archivo` — subir (STOR)
- `binary` / `ascii` — cambiar modo de transferencia
- `quit` — desconectar

### Ejemplo interactivo (CLI)

```bash
$ ftp ftp.ejemplo.com
Connected to ftp.ejemplo.com.
Name (ftp.ejemplo.com:gustavo): miusuario
Password: ********
ftp> binary
ftp> cd /publico
ftp> get manual.pdf
ftp> put informe.txt
ftp> quit
```

### Ejemplos prácticos (herramientas reales)

#### `curl`

- Descargar:

```bash
curl -u usuario:contraseña -O ftp://ftp.ejemplo.com/remote.zip
```

- Subir:

```bash
curl -T localfile.zip -u usuario:contraseña ftp://ftp.ejemplo.com/remote/
```

#### `lftp` (ideal para scripts y sincronización)

```bash
lftp -u user,pass ftp.ejemplo.com
lftp> set ftp:passive-mode yes
lftp> mirror /remote/dir /local/dir
```

#### Python (`ftplib`)

```python
from ftplib import FTP
ftp = FTP('ftp.ejemplo.com')
ftp.login('user','pass')
ftp.cwd('/pub')
with open('local.bin','wb') as f:
    ftp.retrbinary('RETR remote.bin', f.write)
ftp.quit()
```

#### Clientes GUI populares

- **FileZilla**, **WinSCP**, **Cyberduck** — facilitan arrastrar y soltar, gestionar múltiples conexiones y soportan FTPS/SFTP.

### ASCII vs Binary

- **ASCII**: para archivos de texto (convierte finales de línea según plataforma).
- **Binary**: para imágenes, zip, ejecutables — **siempre** usar binary para archivos no textuales para evitar corrupción.

### Seguridad — riesgos y recomendaciones

**Riesgos**: FTP plain transmite credenciales y datos en texto claro → susceptible a sniffing, ataques de fuerza bruta y exposición de datos. Además, el manejo de puertos pasivos puede abrir múltiples puertos en el firewall.

**Mitigaciones**:

- NO usar FTP plain sobre Internet.
- Preferir **SFTP** o **FTPS** (TLS).
- Usar rango de puertos pasivos fijos y abrir solo esos en firewall.
- Deshabilitar acceso anónimo si no es necesario.
- Chroot (encerrar) usuarios en su directorio.
- Monitoreo + bloqueo automático (ej. **fail2ban**).
- Usar VPN/Bastion o túneles para acceso seguro cuando sea posible.
- Usar claves (SFTP) o certificados (FTPS) en lugar de contraseñas simples.

### ¿Cuándo usar FTP hoy?

- FTP aún tiene sentido dentro de redes internas o sistemas heredados donde no se requieren cifrado externo.
- Para conexiones seguras y públicas, usar **SFTP** o **FTPS**.
- Para sincronizaciones robustas y automatizadas, herramientas como `lftp`, `rsync` sobre SSH o soluciones cloud suelen ser mejores.

### Resumen rápido (TL;DR)

FTP es un protocolo clásico para transferir archivos entre cliente y servidor usando TCP (puerto 21 para control y puertos para datos). Funciona en modos activo/pasivo, tiene variantes seguras (FTPS) y alternativas modernas más seguras (SFTP). Útil y omnipresente, pero **no seguro por defecto**, así que en internet siempre hay que usar una versión cifrada o un túnel/VPN.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**        | **Siguiente 4**     |
| ------------------ | ------------------ | ------------------- |
| [🏠](../README.md) | [⏪](./5_2_RDP.md) | [⏩](./5_4_SFTP.md) |
