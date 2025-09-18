| **Inicio**         | **atrás 1**        | **Siguiente 3**    |
| ------------------ | ------------------ | ------------------ |
| [🏠](../README.md) | [⏪](./5_1_SSH.md) | [⏩](./5_3_FTP.md) |

---

## **Índice**

| Temario                                                                                                                                         |
| ----------------------------------------------------------------------------------------------------------------------------------------------- |
| [134. ¿Qué es el Protocolo de Escritorio Remoto (RDP)?](#79-comprensión-de-la-dirección-de-bucle-invertido-y-las-interfaces-de-bucle-invertido) |
| [135. ¿Qué es RDP y cómo utilizarlo?](#79-comprensión-de-la-dirección-de-bucle-invertido-y-las-interfaces-de-bucle-invertido)                   |

# **RDP**

## **134. ¿Qué es el Protocolo de Escritorio Remoto (RDP)?**

![RDP](/img/5_Network_Topologies/RDP.png "RDP")

### 🔹 ¿Qué es el **Protocolo de escritorio remoto (RDP)?**

El **RDP (Remote Desktop Protocol)** es un protocolo desarrollado por **Microsoft** que permite conectarse de forma remota a otra computadora con Windows (aunque también hay clientes para Linux y macOS).
En otras palabras: es como “abrir” la pantalla, teclado y mouse de otra PC desde tu propia máquina, como si estuvieras sentado frente a ella.

### 🔹 ¿Qué significa **"escritorio remoto"?**

El término **escritorio remoto** se refiere a la capacidad de **controlar y usar una computadora desde otro lugar**, accediendo a su escritorio, aplicaciones, archivos y recursos.

Ejemplo: estás en tu casa y te conectas al PC de tu oficina para trabajar en un programa que solo está instalado ahí.

### 🔹 ¿Cómo funciona el **RDP**?

1. El **cliente RDP** (tu computadora) envía solicitudes al **servidor RDP** (la computadora remota con Windows habilitado para conexiones).
2. El servidor **procesa las acciones** (movimientos de mouse, teclas, apertura de aplicaciones) y envía de vuelta al cliente la “imagen” del escritorio en tiempo real.
3. Todo viaja **encriptado** a través del puerto TCP **3389** (aunque puede configurarse otro).

👉 Ejemplo:

- Tú haces clic en Excel en tu laptop.
- El clic viaja al servidor RDP.
- El servidor abre Excel y te manda de vuelta la pantalla actualizada.

### 🔹 Ventajas y desventajas del RDP

✅ **Ventajas**

- Permite **acceder desde cualquier lugar** a una PC o servidor.
- Facilita la administración de servidores y escritorios en empresas.
- Ahorra en licencias, ya que no necesitas instalar software en todas las máquinas.
- Soporta **impresión remota y portapapeles compartido**.

❌ **Desventajas**

- **Expuesto a ataques** si se deja abierto a internet (fuerza bruta, ransomware, exploits).
- Puede consumir mucho ancho de banda si la conexión es lenta.
- Problemas de latencia en redes inestables.
- No está diseñado originalmente para múltiples usuarios concurrentes (aunque existen Remote Desktop Services).

### 🔹 ¿Cómo ayuda **Cloudflare a proteger el acceso RDP**?

RDP, al usar el puerto 3389, es un blanco muy común de ataques. Aquí entra **Cloudflare Zero Trust / Cloudflare Access**:

1. **Cierra el puerto 3389 al público** → En vez de exponer el RDP a internet, se conecta solo a través de Cloudflare.
2. **Autenticación multifactor (MFA)** → Antes de acceder, el usuario debe validarse con su identidad (Google, Azure AD, GitHub, etc.).
3. **Túneles seguros (Cloudflare Tunnel)** → El tráfico RDP viaja cifrado por HTTPS en lugar de dejar el puerto abierto.
4. **Políticas de acceso** → Se puede definir quién accede, desde qué país o dispositivo.
5. **Protección contra bots y ataques de fuerza bruta** → Cloudflare filtra intentos masivos de login.

👉 En resumen: Cloudflare actúa como **puerta de seguridad** que filtra, autentica y cifra antes de que alguien llegue a tu RDP.

---

[🔼](#índice)

---

## **135. ¿Qué es RDP y cómo utilizarlo?**

### ¿Qué es RDP?

**RDP (Remote Desktop Protocol)** es un protocolo desarrollado por Microsoft que permite controlar de forma remota otra computadora: ver su pantalla, manejar el teclado y el mouse, abrir aplicaciones y acceder a archivos como si estuvieras delante de esa máquina. Es muy usado para administración remota, soporte técnico y acceso a escritorios/servidores desde fuera de la oficina.

### ¿Cómo funciona (resumen técnico)?

- **Modelo cliente — servidor**: la máquina remota ejecuta el _servicio RDP_ (servidor) y la máquina que se conecta ejecuta un _cliente RDP_.
- **Transporte**: tradicionalmente usa **TCP puerto 3389**. Las versiones modernas pueden usar también **UDP** para mejorar la latencia.
- **Autenticación y cifrado**: RDP puede usar **NLA (Network Level Authentication)**, CredSSP y TLS para autenticar y cifrar la sesión.
- **Canales virtuales**: además de la pantalla, RDP permite redirección de portapapeles, sonido, impresoras, unidades (drives), smartcards y otros dispositivos mediante “virtual channels”.

### ¿Qué necesitas para usarlo?

- Un **equipo host** (que recibiría la conexión) con RDP habilitado — normalmente Windows Pro/Enterprise/Server.
- Un **cliente RDP**: en Windows viene `mstsc` (Conexión a Escritorio Remoto). Hay clientes para macOS, Linux (Remmina, `xfreerdp`), iOS/Android y clientes de terceros.
- **Conectividad de red** entre cliente y host (LAN, VPN, túnel, o acceso público — este último **no** es recomendable sin protecciones).

### Ejemplos prácticos — paso a paso

#### Ejemplo A — Conexión Windows → Windows en la misma LAN

##### 1) En el equipo remoto (host)

- Habilita Escritorio remoto: **Configuración > Sistema > Escritorio remoto** → activar “Habilitar Escritorio remoto”.
- Asegúrate de que el usuario esté permitido (Users list) y anota la **IP local** del host (`ipconfig` → IPv4).
- Permite la regla de Firewall (normalmente Windows lo hace al activar RDP).

_Comandos PowerShell (ejecutar como Administrador) para habilitar y abrir firewall:_

```powershell
# Habilitar RDP
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections" -Value 0

# Permitir RDP en firewall
Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
```

##### 2) En el equipo cliente

- Ejecuta `mstsc.exe` (Conexión a Escritorio Remoto) y escribe la IP del host (por ejemplo `192.168.1.20`) → Conectar → ingresa usuario y contraseña.
- Otra forma (línea de comandos):

```bat
mstsc /v:192.168.1.20
mstsc /v:192.168.1.20:3389 /f    # /f = fullscreen
mstsc /v:192.168.1.20 /admin    # conectar en modo de administración
```

#### Ejemplo B — Usar un archivo .rdp

Crea un archivo `mi-sesion.rdp` con contenido como:

```
full address:s:192.168.1.20:3389
username:s:MI_DOMINIO\gustavo
desktopwidth:i:1920
desktopheight:i:1080
session bpp:i:32
redirectclipboard:i:1
redirectprinters:i:1
```

Guárdalo y haz doble clic para abrir la sesión con esas configuraciones.

#### Ejemplo C — Conexión desde Linux (xfreerdp)

Instala `freerdp2-x11` o `xfreerdp` y ejecuta:

```bash
xfreerdp /u:gustavo /p:MiClaveSegura /v:192.168.1.20:3389 /dynamic-resolution /clipboard
```

> **Nota**: por seguridad evita pasar contraseñas en la línea de comandos en entornos reales (usa prompt o archivos seguros).

#### Ejemplo D — Conexión segura a un host RDP a través de un SSH tunnel (cuando no quieres abrir 3389 públicamente)

Si tienes un servidor intermedio `jump.example.com` al que puedes SSH y desde ahí se accede al host RDP `192.168.1.20`:

En el cliente:

```bash
ssh -L 33389:192.168.1.20:3389 usuario@jump.example.com -N
# Ahora en otra terminal o con mstsc:
mstsc /v:localhost:33389
```

Con PuTTY en Windows configuras `Connection > SSH > Tunnels` : source `33389`, destination `192.168.1.20:3389`, abrir la sesión SSH y luego conectas en mstsc a `localhost:33389`.

#### Ejemplo E — Conexión a VM en la nube (ejemplo general)

- En proveedores cloud (Azure, AWS, GCP) normalmente se crea una VM Windows y el proveedor te ofrece un **archivo .rdp** o acceso a la contraseña. Descargas el `.rdp` y lo abres con mstsc.
- **IMPORTANTE**: la nube suele exigir reglas de seguridad (Security group / NSG) — evita exponer 3389 a todo internet; usa bastion host, VPN o soluciones de acceso seguro.

### Buenas prácticas de seguridad (imprescindible)

RDP es un blanco común para atacantes si queda expuesto. Reglas básicas:

1. **No exponer 3389 directamente a Internet.**
2. **Usar VPN o RD Gateway** (puerta de enlace RDP) o soluciones Zero Trust (p. ej. Cloudflare Access/ Tunnel) para poner una capa de autenticación y filtrado.
3. **Habilitar NLA (Network Level Authentication)** — obliga a autenticarse antes de crear la sesión completa.
4. **Forzar MFA** donde sea posible (a través de gateway o soluciones Zero Trust).
5. **Contraseñas fuertes + bloqueo de cuenta** tras intentos fallidos.
6. **Actualizar Windows y RDP** (parches) regularmente.
7. **Limitar a qué usuarios se les permite RDP** (grupo «Remote Desktop Users»).
8. **Registrar / monitorizar** intentos de acceso y alertas (SIEM/logging).
9. **Evitar usar cuentas administrativas para tareas diarias**; usar cuentas con permisos mínimos.
10. **Usar certificados válidos** para evitar warnings y ataques de MITM.

_Ejemplo: permitir RDP solo desde la IP pública de la oficina (PowerShell):_

```powershell
New-NetFirewallRule -DisplayName "Allow RDP from Office" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 3389 -RemoteAddress 200.51.100.5
```

### Problemas comunes y cómo resolverlos

- **Error “No se puede conectar al equipo remoto”**: verificar IP, que el servicio `Remote Desktop Services` esté en ejecución (`Get-Service -Name TermService`), y reglas de firewall.
- **CredSSP / “authentication error”**: puede deberse a parches/compatibilidad de CredSSP entre cliente y servidor; actualizar ambos sistemas o ajustar políticas temporales (no recomendado sin entender riesgos).
- **Usuario no autorizado**: agrega el usuario a _Remote Desktop Users_:

```powershell
Add-LocalGroupMember -Group "Remote Desktop Users" -Member "MI-PC\gustavo"
```

- **Advertencias de certificado**: RDP usa certificados auto-firmados por defecto; para seguridad usa certificados TLS válidos o un gateway.

### Optimizar rendimiento (si la conexión es lenta)

- Ajusta la calidad de color (16 bits en vez de 32).
- Desactiva wallpaper, animaciones y fuentes suaves desde la pestaña _Experience_ en `mstsc`.
- Activa compresión y bitmap caching si es posible.
- Usa RDP sobre UDP si está disponible (reduce latencia intermitente).

### Resumen rápido / Checklist para conectar por primera vez

1. Habilitar RDP en el host (System → Remote Desktop).
2. Añadir usuario al grupo Remote Desktop Users.
3. Abrir/permitir firewall en puerto 3389 (o regla específica para tu IP).
4. Obtener la IP o nombre DNS del host.
5. Desde el cliente abrir `mstsc`, escribir `IP[:puerto]` y conectar.
6. Si acceso fuera de la red, usar **VPN / RD Gateway / Tunnel** — nunca dejar 3389 abierto sin protección.
7. Aplicar NLA, MFA y reglas de acceso.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**        | **Siguiente 3**    |
| ------------------ | ------------------ | ------------------ |
| [🏠](../README.md) | [⏪](./5_1_SSH.md) | [⏩](./5_3_FTP.md) |
