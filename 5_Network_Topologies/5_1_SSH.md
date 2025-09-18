| **Inicio**         | **Siguiente 2**    |
| ------------------ | ------------------ |
| [🏠](../README.md) | [⏩](./5_2_RDP.md) |

---

## **Índice**

| Temario                                                                                       |
| --------------------------------------------------------------------------------------------- |
| [132. ¿Qué es SSH? Protocolo Secure Shell (SSH)](#132-qué-es-ssh--protocolo-secure-shell-ssh) |
| [133. ¿Cómo funciona SSH?](#133-cómo-funciona-ssh)                                            |

# **SSH**

## **132. ¿Qué es SSH? Protocolo Secure Shell (SSH)**

![SSH](/img/5_Network_Topologies/SSH.webp "SSH")

### 🔹 1. ¿Qué es el protocolo SSH?

**SSH (Secure Shell)** es un **protocolo de red criptográfico** que permite conectarse de manera **segura** a dispositivos y servidores remotos.

- Fue creado para reemplazar protocolos inseguros como **Telnet** o **rlogin**, que transmitían datos en **texto plano** (incluyendo contraseñas).
- SSH cifra toda la comunicación entre cliente y servidor, evitando que alguien pueda leerla si intercepta el tráfico.

📌 Ejemplo:

Si eres administrador de un servidor en la nube (Linux en AWS, Azure, Google Cloud, etc.), usas SSH para conectarte desde tu PC con un comando como:

```bash
ssh usuario@192.168.1.10
```

y trabajar de forma segura como si estuvieras frente al servidor.

### 🔹 2. ¿Qué hace el SSH?

- Proporciona un **canal cifrado** para:

  - 🔹 Acceder a servidores remotos (administración de Linux/Unix).
  - 🔹 Transferir archivos de forma segura (SCP, SFTP).
  - 🔹 Crear **túneles seguros** para otras aplicaciones.

📌 Ejemplo: puedes administrar un router o un servidor web desde tu laptop, incluso si estás en otra ciudad.

### 🔹 3. ¿Cómo funciona el SSH?

El funcionamiento de SSH se da en varias etapas:

1. **Conexión inicial** 🔗

   - El cliente (tu PC) se conecta al servidor SSH (el servidor remoto).

2. **Intercambio de claves** 🔑

   - Cliente y servidor acuerdan un método de cifrado usando criptografía asimétrica (claves públicas y privadas).

3. **Autenticación** 👤

   - Puede ser con:

     - Usuario + contraseña.
     - O mejor aún: **claves SSH** (par de claves pública/privada).

4. **Sesión segura** 🔒

   - Una vez autenticado, todo el tráfico (comandos, respuestas, transferencias de archivos) va cifrado punto a punto.

📌 Ejemplo: si escribes `ls` para listar archivos en el servidor, ese comando viaja cifrado. El resultado (los nombres de los archivos) también vuelve cifrado a tu PC.

### 🔹 4. ¿Para qué se utiliza el SSH?

- **Administración remota segura** (ejemplo: gestionar servidores Linux en la nube).
- **Transferencia segura de archivos** con:

  - `scp` (Secure Copy).
  - `sftp` (SSH File Transfer Protocol).

- **Tunelización** → redirigir tráfico de otros servicios a través de un túnel cifrado.
- **Acceso a dispositivos de red** (routers, switches, firewalls).
- **Automatización** → scripts que se conectan por SSH para ejecutar tareas en servidores.

### 🔹 5. ¿Qué puerto usa SSH?

Por defecto, SSH usa el **puerto TCP 22**.

- Aunque por seguridad, muchos administradores cambian el puerto (ejemplo: **2222**) para reducir intentos de ataques automáticos.

### 🔹 6. ¿Existen riesgos de seguridad en SSH?

Aunque SSH es muy seguro, existen algunos riesgos:

⚠️ **Fuerza bruta**: atacantes prueban miles de contraseñas en el puerto 22.

⚠️ **Claves débiles**: usar claves privadas mal protegidas o contraseñas fáciles.

⚠️ **Configuración insegura**: permitir root login directo o no usar autenticación por clave.

✅ Soluciones:

- Usar **autenticación con clave pública** en lugar de contraseña.
- Desactivar acceso root directo.
- Usar **Fail2Ban** o firewalls para bloquear intentos masivos.
- Cambiar el puerto por defecto.

### 🔹 7. ¿Cómo contrasta el SSH con otros protocolos de tunelización?

- **SSH** → cifrado + autenticación, más simple, ideal para administración remota.
- **VPN (ej. IPsec, OpenVPN, WireGuard)** → cifran **todo el tráfico de red** (no solo terminal o archivos).
- **TLS/SSL** → usado en HTTPS para cifrar tráfico web.

📌 Ejemplo:

- SSH es perfecto si quieres **administrar un servidor remoto** o enviar archivos.
- VPN es mejor si quieres que **toda tu conexión de Internet esté cifrada** (como en teletrabajo).

### 🔹 8. ¿Qué hace Cloudflare para que el SSH sea más seguro?

Cloudflare ofrece varias herramientas para proteger SSH:

1. **Cloudflare Access** 🔒

   - Permite conectarte a servidores SSH sin exponer el puerto 22 a Internet.
   - Funciona con autenticación basada en identidad (Google, GitHub, Azure AD, etc.).

2. **Zero Trust Tunnels** 🌐

   - Usando `cloudflared`, puedes crear un túnel seguro entre tu servidor y Cloudflare, eliminando la necesidad de abrir puertos en el firewall.

3. **Protección contra ataques** 🚫

   - Bloquea intentos masivos de fuerza bruta en el puerto SSH.
   - Filtra conexiones sospechosas antes de que lleguen a tu servidor.

📌 Ejemplo:

En vez de abrir tu servidor en AWS al público en el puerto 22, usas Cloudflare Access → te conectas con autenticación multifactor → tu servidor nunca queda expuesto a ataques directos.

✅ **En resumen**:

- SSH es el **estándar para administración remota segura**.
- Usa el **puerto 22** (o uno alternativo).
- Es más ligero y específico que una VPN.
- Cloudflare añade una capa de **Zero Trust y protección contra ataques**.

---

[🔼](#índice)

---

## **133. ¿Cómo funciona SSH?**

SSH funciona como un **puente cifrado** entre un **cliente** (tu computadora) y un **servidor** (otro equipo o servicio remoto).
Su proceso se divide en **3 grandes fases**:

### 🔹 1. Establecimiento de la conexión

Cuando ejecutas en tu PC:

```bash
ssh usuario@192.168.1.10
```

👉 Aquí ocurre lo siguiente:

- El **cliente SSH** (tu computadora) intenta conectarse al **servidor SSH** (192.168.1.10).
- Ambos acuerdan el **algoritmo de cifrado** que usarán (AES, ChaCha20, etc.).
- Se valida la **huella digital del servidor** (clave pública del servidor).

📌 Ejemplo:

Es como cuando conoces a alguien por primera vez y le pides que muestre su **carné de identidad** para comprobar que es quien dice ser.

### 🔹 2. Intercambio de claves (Handshake)

Para que nadie pueda espiar la conexión, SSH usa criptografía:

- Se negocia una **clave de sesión temporal** que servirá para cifrar todos los datos.
- Esta clave se genera con **algoritmos de clave pública/privada** (como RSA o ED25519).
- Una vez creada, **todo el tráfico viaja cifrado**.

📌 Ejemplo:

Es como si ambos (cliente y servidor) crean un **idioma secreto único** que solo ellos entienden durante esa sesión.

### 🔹 3. Autenticación del usuario

Después de asegurar el canal, SSH verifica la identidad del **usuario**:

1. **Contraseña**

   - El usuario escribe la contraseña.
   - Va cifrada, no en texto plano.

2. **Claves SSH (más seguro)**

   - El usuario genera un **par de claves**:

     - 🔑 **Clave privada** → se guarda en tu PC.
     - 🔑 **Clave pública** → se guarda en el servidor.

   - Durante la conexión, el servidor lanza un "desafío" que solo puede resolver quien tenga la **clave privada correcta**.

📌 Ejemplo:

- Contraseña → como usar tu DNI y una clave secreta.
- Claves SSH → como tener una **llave física única** que abre la cerradura del servidor.

### 🔹 4. Establecimiento de la sesión segura

Si la autenticación es exitosa:

- El cliente obtiene acceso a la **terminal del servidor remoto**.
- Todo lo que escribes (comandos) viaja **cifrado**.
- Todo lo que el servidor responde (resultados) también vuelve cifrado.

📌 Ejemplo:

Si ejecutas en el servidor remoto:

```bash
ls -l
```

👉 El comando viaja cifrado.

👉 El servidor responde con la lista de archivos, también cifrada.

👉 Solo tu cliente puede descifrarlo y mostrarlo en pantalla.

### 🔹 5. Flujo de datos en SSH (resumen gráfico con ejemplo)

1. Cliente inicia conexión → `ssh usuario@servidor`.
2. Servidor muestra su identidad (clave pública).
3. Cliente y servidor acuerdan un método de cifrado.
4. Usuario se autentica (contraseña o clave SSH).
5. Se establece la **sesión segura** → ahora puedes administrar el servidor.

📌 Ejemplo real:

- Desde tu laptop en Lima te conectas a un **servidor Linux en AWS (Irlanda)**.
- Aunque viaja por miles de kilómetros y múltiples routers, **nadie puede leer ni modificar lo que transmites**, porque todo va cifrado con SSH.

### 🔹 6. Funciones adicionales de SSH

SSH no solo es terminal remota:

- **SCP** → copiar archivos de forma segura.

  ```bash
  scp archivo.txt usuario@192.168.1.10:/home/usuario/
  ```

- **SFTP** → transferir archivos como con FTP, pero cifrado.
- **Tunelización (port forwarding)** → usar SSH para proteger otros servicios.

  ```bash
  ssh -L 8080:localhost:80 usuario@servidor
  ```

  (redirige el puerto 80 del servidor a tu puerto 8080 local).

✅ **En resumen**:

SSH funciona con estos pasos:

1. Cliente → Servidor (conexión).
2. Intercambio de claves → cifrado del canal.
3. Autenticación (contraseña o claves).
4. Sesión segura → comandos y datos viajan cifrados.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**    |
| ------------------ | ------------------ |
| [🏠](../README.md) | [⏩](./5_2_RDP.md) |
