| **Inicio**         | **Siguiente 2**         |
| ------------------ | ----------------------- |
| [🏠](../README.md) | [⏩](./4_2_loopback.md) |

---

## **Índice**

| Temario                                        |
| ---------------------------------------------- |
| [78. ¿Qué es localhost?](#78-qué-es-localhost) |

---

# **Localhost**

## **78. ¿Qué es localhost?**

![Localhost](/img/4_IP_Terminology/Localhost.png "Localhost")

### Resumen rápido

- **`localhost`** es un **nombre especial** que apunta al **loopback** del propio equipo: “yo mismo”.
- **`127.0.0.1`** es la IP **loopback** más usada en IPv4 (parte del rango `127.0.0.0/8`). Ambos permiten que un equipo se comunique consigo mismo sin pasar por la tarjeta de red física.
- En **IPv6** el equivalente es `::1`.

### ¿Qué significa “loopback” y cómo funciona?

- El _loopback_ es una interfaz virtual que proporciona una ruta de red que siempre “vuelve” al mismo host.
- Cuando envías paquetes a 127.0.0.1 (o `localhost`), el kernel de la máquina **no los coloca en la tarjeta de red**: los procesa internamente y los entrega a las aplicaciones locales.
- La interfaz suele llamarse `lo` en Linux (ver `ip addr show lo`), y existe por diseño en todos los sistemas operativos modernos.

### ¿127.0.0.1 es la única dirección loopback?

No — **todo el bloque `127.0.0.0/8` (127.0.0.0–127.255.255.255)** está reservado para loopback.

- `127.0.0.1` es la más común por convención.
- También son válidas `127.0.0.2`, `127.1.1.1`, etc.
- En **IPv6**, el loopback es `::1`.

### `localhost` vs `127.0.0.1`: ¿hay diferencia?

- En **la práctica para IPv4** son equivalentes si `localhost` resuelve a `127.0.0.1`.
- **Pero**:

  - `localhost` es un **nombre** que se resuelve normalmente mediante el fichero **hosts** (`/etc/hosts` en Linux/macOS o `C:\Windows\System32\drivers\etc\hosts` en Windows).
  - En sistemas con IPv6, `localhost` frecuentemente se resuelve a `::1` (IPv6) — por eso `ping localhost` puede usar IPv6.
  - Puedes cambiar `localhost` en `hosts` (no recomendado), con lo que `localhost` podría apuntar a otra IP local (cuidado con esto).
  - En contenedores (Docker, LXC) el `localhost` de un contenedor es el contenedor mismo, no el host físico.

### ¿Para qué sirve `localhost` / `127.0.0.1`? (casos de uso)

- **Desarrollo web**: pruebas de servidores web localmente (`http://localhost:3000`).
- **Bases de datos**: conectar clientes a la DB local (`psql -h 127.0.0.1`).
- **Servicios administrativos**: interfaces de administración que sólo deben ser accesibles localmente.
- **Túneles SSH / forwarding**: `ssh -L 8080:127.0.0.1:80 user@remote` para exponer un servicio remoto como si fuera local.
- **Bloqueo de dominios**: mapear un dominio a `127.0.0.1` en `hosts` para bloquearlo (ej. bloqueo de publicidad).

### Ejemplos prácticos (comandos y snippets)

#### 1) Ver la interfaz loopback (Linux/macOS)

```bash
ip addr show lo
# o (antiguo)
ifconfig lo
```

#### 2) Comprobar conectividad básica

```bash
# IPv4
ping 127.0.0.1
ping localhost   # puede usar IPv6 si localhost->::1

# IPv6
ping6 ::1
```

#### 3) Levantar un servidor web simple y probar

**Python 3 (binding por defecto en 0.0.0.0 en algunas versiones — ojo):**

```bash
# servidor escuchando en 127.0.0.1:8000
python -m http.server 8000 --bind 127.0.0.1

# probar desde la misma máquina
curl http://127.0.0.1:8000
curl http://localhost:8000
```

**Node.js (ejemplo de binding):**

```js
// server.js
const http = require("http");
const hostname = "127.0.0.1"; // o '0.0.0.0'
const port = 3000;
http
  .createServer((req, res) => {
    res.end("Hola desde localhost\n");
  })
  .listen(port, hostname, () => console.log(`Listening ${hostname}:${port}`));
```

- Si pones `127.0.0.1` → sólo accesible desde la misma máquina.
- Si pones `0.0.0.0` → accesible desde cualquier interfaz (LAN) si el firewall lo permite.

#### 4) Comprobar puertos en escucha

```bash
# Linux
ss -tuln | grep 127.0.0.1
# o
sudo lsof -iTCP -sTCP:LISTEN -P -n
```

#### 5) Hosts file (mapear dominios a localhost)

Editar `/etc/hosts` (Linux/macOS) o `C:\Windows\System32\drivers\etc\hosts` (Windows):

```
127.0.0.1   local.test myapp.local
::1         local.test myapp.local
```

Así `http://myapp.local` apuntará a tu servidor local — muy útil para pruebas.

#### 6) Docker: cuidado con `localhost`

- Dentro de un contenedor, `localhost` = contenedor.
- Para acceder al servicio del host desde un contenedor:

  - En Linux con Docker, puedes usar la IP del host en la red bridge, o `--network host` (no en Docker Desktop Windows/Mac).
  - En Docker Desktop, `host.docker.internal` resuelve al host desde el contenedor.

#### 7) SSH tunneling (ejemplo)

Exponer el puerto 80 remoto como `http://localhost:8080` local:

```bash
ssh -L 8080:127.0.0.1:80 usuario@remoteserver.example.com
# luego en tu navegador: http://localhost:8080
```

### Seguridad: ¿por qué bindear a `127.0.0.1` ayuda?

- Si una aplicación solo escucha en `127.0.0.1` **no es accesible** desde la red, disminuyendo la superficie de ataque.
- Buen patrón: en servidores, termina TLS/terminación de proxy público en el front (NGINX/ELB) y deja el backend escuchando en `127.0.0.1` para que solo el proxy lo alcance.

### Preguntas frecuentes rápidas

- **¿Puedo acceder a 127.0.0.1 desde otra máquina?** No. 127.0.0.1 siempre refiere a _la_ máquina local que hace la petición.
- **¿`localhost` siempre es 127.0.0.1?** Normalmente sí (para IPv4), pero `localhost` puede resolverse a `::1` en IPv6; depende del `hosts` y del sistema.
- **¿Puedo usar 127.0.0.2?** Sí: cualquier IP del rango `127.0.0.0/8` es válida y loopback.
- **¿Los firewalls afectan al loopback?** Generalmente no, porque el tráfico no sale de la máquina; pero algunas reglas locales o software de seguridad pueden interferir.
- **¿Es localhost seguro por defecto?** Es seguro en cuanto a exposición externa, pero una aplicación maliciosa local todavía puede conectarse a puertos en `127.0.0.1`. Controlar usuarios locales y aplicaciones instaladas sigue siendo importante.

### Resumen (en 3 líneas)

- `localhost` = nombre que apunta al **loopback** (tu misma máquina).
- `127.0.0.1` = dirección IPv4 loopback más usada (rango `127.0.0.0/8`).
- Usarlos para desarrollo y servicios locales es práctico y —si se configura correctamente— reduce la exposición externa.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**         |
| ------------------ | ----------------------- |
| [🏠](../README.md) | [⏩](./4_2_loopback.md) |
