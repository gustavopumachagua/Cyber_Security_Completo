| **Inicio**         | **atrás 8**            | **Siguiente 10**         |
| ------------------ | ---------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./7_8_Tracert.md) | [⏩](./7_10_Iptables.md) |

---

## **Índice**

| Temario                                        |
| ---------------------------------------------- |
| [176. Nslookup](#176-nslookup)                 |
| [177. ¿Qué es Nslookup?](#177-qué-es-nslookup) |

# **Nslookup**

## **176. Nslookup**

![Nslookup](/img/7_Troubleshooting_Tools/Nslookup.avif "Nslookup")

### 🔹 ¿Qué es?

`nslookup` (Name Server Lookup) es una utilidad de red usada para consultar **servidores DNS** y obtener información sobre nombres de dominio y direcciones IP. Sirve para:

- Traducir un dominio en IP (**forward lookup**).
- Traducir una IP en dominio (**reverse lookup**).
- Consultar registros DNS específicos (A, AAAA, MX, NS, TXT, etc.).
- Usar un servidor DNS específico para la consulta.

### 🔹 Sintaxis

```bash
nslookup [-opciones] [nombre] [servidor]
```

- **nombre** → el dominio o dirección IP a consultar.
- **servidor** → (opcional) el servidor DNS que quieres usar en lugar del predeterminado.

### 🔹 Parámetros principales

| Parámetro              | Descripción                                                          |
| ---------------------- | -------------------------------------------------------------------- |
| `nombre`               | Nombre de dominio (ej: `google.com`) o dirección IP (ej: `8.8.8.8`). |
| `servidor`             | DNS específico a usar (ej: `8.8.8.8`).                               |
| `set type=A`           | Consulta registros A (IPv4).                                         |
| `set type=AAAA`        | Consulta registros AAAA (IPv6).                                      |
| `set type=MX`          | Consulta servidores de correo.                                       |
| `set type=NS`          | Consulta servidores de nombres.                                      |
| `set type=TXT`         | Consulta registros TXT.                                              |
| `set timeout=segundos` | Cambia el tiempo de espera de la consulta.                           |
| `set retry=n`          | Número de intentos de reintento.                                     |

### 🔹 Observaciones importantes

- En **Windows**, `nslookup` está incluido por defecto.
- En **Linux**, suele estar disponible en el paquete `dnsutils` o `bind-utils`.
- Existen dos modos de uso:

  1. **Modo no interactivo**: una sola consulta.
  2. **Modo interactivo**: ingresas a un "prompt de nslookup" para hacer varias consultas sin salir.

- Actualmente, en Linux se recomienda más `dig`, pero `nslookup` sigue siendo muy usado.

### 🔹 Ejemplos

#### ✅ Modo no interactivo (una sola línea)

```bash
# Consultar la IP de un dominio
nslookup google.com

# Consultar usando un servidor DNS específico (ejemplo: Cloudflare)
nslookup openai.com 1.1.1.1

# Hacer una consulta inversa (IP → nombre)
nslookup 8.8.8.8
```

#### ✅ Modo interactivo

```bash
nslookup
```

👉 Aparece un prompt así:

```
>
```

Ahora puedes escribir comandos:

```bash
> set type=A
> openai.com
```

👉 Devuelve las direcciones IPv4 de `openai.com`.

```bash
> set type=MX
> gmail.com
```

👉 Devuelve los servidores de correo de Gmail.

```bash
> set type=NS
> wikipedia.org
```

👉 Devuelve los servidores de nombres de `wikipedia.org`.

```bash
> exit
```

👉 Sales del modo interactivo.

### 🔹 Resumen rápido

- **Sintaxis**: `nslookup [dominio o IP] [servidor DNS]`.
- **Modo no interactivo**: consultas rápidas (una sola línea).
- **Modo interactivo**: consultas avanzadas con parámetros (`set type=MX`, `set type=NS`, etc.).
- Muy útil para:

  - Diagnosticar problemas de resolución DNS.
  - Verificar registros de correo (`MX`).
  - Comprobar servidores DNS.

---

[🔼](#índice)

---

## **177. ¿Qué es Nslookup?**

**`nslookup`** (Name Server Lookup) es una utilidad de línea de comandos para **consultar servidores DNS** y obtener información sobre nombres de dominio y direcciones IP. Sirve para resolver dominios (forward lookup), hacer consultas inversas (reverse lookup), inspeccionar registros DNS (A, AAAA, MX, NS, TXT, SOA, CNAME, PTR, etc.) y diagnosticar problemas de resolución DNS. Está disponible en Windows y en la mayoría de distribuciones Unix/Linux (en este último caso suele venir con paquetes como `bind-utils` o `dnsutils`).

### Cómo funciona (resumen técnico)

- `nslookup` envía consultas DNS a un servidor (por defecto el configurado en tu sistema) usando **UDP** (normalmente) o **TCP** para respuestas grandes, en el puerto **53**.
- Puede preguntar a cualquier servidor DNS (local, del ISP, 8.8.8.8, 1.1.1.1, etc.) para comparar respuestas.
- Devuelve la(s) respuesta(s) que el servidor DNS proporciona y puede indicar si la respuesta es **authoritative** o **non-authoritative** (si viene de un servidor autoritativo o de la caché).

### Modos de uso

#### Modo **no interactivo** (una sola consulta — ideal para scripts y checks rápidos)

Sintaxis general:

```bash
nslookup [nombre_o_IP] [servidor_DNS]
# o
nslookup -type=MX dominio.com 8.8.8.8
```

Ejemplos:

```bash
# Consulta simple (usa tu servidor DNS por defecto)
nslookup example.com
```

Salida típica (resumida):

```
Server:  dns.mi-isp.net
Address: 192.0.2.53

Non-authoritative answer:
Name: example.com
Address: 93.184.216.34
```

```bash
# Consultar el registro MX usando Google DNS
nslookup -type=MX gmail.com 8.8.8.8
```

Salida típica:

```
Server: 8.8.8.8
gmail.com  MX preference = 5, mail exchanger = alt1.gmail-smtp-in.l.google.com
...
```

```bash
# Reverse lookup (IP -> nombre)
nslookup 8.8.8.8
```

> Observación: la opción de línea `-type=` también puede aparecer como `-query=` en algunas implementaciones (`nslookup -query=TXT example.com`). Si tu `nslookup` no acepta un flag, prueba la otra variante o usa el modo interactivo.

### Modo **interactivo** (útil para hacer varias consultas en sesión)

Inicias `nslookup` sin argumentos y obtienes un prompt:

```bash
nslookup
>
```

Comandos comunes dentro del prompt:

- `server <IP/host>` — cambiar el servidor que se va a consultar.
- `set type=<TIPO>` — definir el tipo de registro (A, AAAA, MX, NS, SOA, TXT, PTR, CNAME, ANY).
- `set timeout=<segundos>` — ajustar tiempo de espera.
- `set retry=<n>` — número de reintentos.
- Teclear un dominio (ej. `example.com`) para consultar con las opciones actuales.
- `exit` — salir.

Ejemplo de sesión:

```
$ nslookup
> server 1.1.1.1
Default server: 1.1.1.1
> set type=MX
> example.com
Server: 1.1.1.1
Non-authoritative answer:
example.com    MX preference = 10, mail exchanger = mx.example.com
> set type=TXT
> example.com
(example.com) TXT "v=spf1 include:_spf.example.com -all"
> exit
```

### Registros DNS comunes y ejemplos de consulta

- **A** (IPv4): `nslookup -type=A example.com`
- **AAAA** (IPv6): `nslookup -type=AAAA example.com`
- **MX** (mail exchangers): `nslookup -type=MX example.com`
- **NS** (name servers): `nslookup -type=NS example.com`
- **TXT** (texto / SPF / DKIM): `nslookup -type=TXT example.com`
- **SOA** (start of authority): `nslookup -type=SOA example.com`
- **CNAME** (alias): `nslookup -type=CNAME www.example.com`
- **PTR** (reverse): `nslookup 93.184.216.34` (consulta inversa)

Ejemplo (TXT):

```bash
nslookup -type=TXT example.com 1.1.1.1
```

Salida (resumida):

```
example.com    text = "v=spf1 include:_spf.example.com -all"
```

### Interpretación de resultados: conceptos clave

- **Server / Address**: servidor DNS que respondió a la consulta.
- **Non-authoritative answer**: la respuesta vino de la caché de un servidor recursivo (no directamente del servidor autoritativo del dominio).
- **Authoritative answers can be found from**: si aparece, indica que la respuesta proviene del servidor autoritativo.
- **NXDOMAIN**: el nombre no existe.
- **Timeout / no answer**: el servidor DNS no respondió (puede deberse a firewall o fallo).

### Observaciones y buenas prácticas

- `nslookup` es muy útil para comparar respuestas entre servidores (p. ej. `nslookup example.com 8.8.8.8` vs `nslookup example.com 192.0.2.53`) — esto ayuda a detectar problemas de propagación de DNS o configuraciones diferentes (split-horizon).
- **Reverse DNS** depende de que la entidad propietaria de la IP configure la entrada PTR; muchas IPs no tienen PTR.
- `nslookup` puede comportarse ligeramente distinto según sistema/versión (Windows vs BIND `nslookup`). Si necesitas salida más detallada o scripting, `dig` suele ser preferido en Linux.
- En entornos con firewalls, ten en cuenta que las consultas DNS pueden estar bloqueadas o forzadas a servidores internos (verifica `Server:` en la salida).

### Comparación rápida: `nslookup` vs `dig` vs `host`

- `nslookup`: simple, interactivo y disponible en Windows; fácil para consultas rápidas y aprendizaje.
- `dig`: más potente y flexible para scripting y salidas estructuradas (muy usado en Linux).
- `host`: interfaz simple para consultas puntuales (sirve para scripts cortos).

### Mini-cheat sheet (comandos útiles listos para copiar)

```bash
# Consulta A (no interactivo)
nslookup -type=A example.com

# Consulta MX usando Cloudflare
nslookup -type=MX example.com 1.1.1.1

# Reverse lookup (IP -> nombre)
nslookup 8.8.8.8

# Entrar a modo interactivo
nslookup
# Dentro del prompt:
# > server 8.8.8.8
# > set type=TXT
# > example.com
# > set timeout=3
# > set retry=2
# > exit
```

### Ejemplo práctico de troubleshooting DNS

1. Si un dominio no resuelve para ti:

   - `nslookup example.com` → ¿qué devuelve tu servidor DNS?
   - `nslookup example.com 8.8.8.8` → ¿qué devuelve Google DNS?
   - Si las respuestas difieren, puede ser propagación DNS, caché del ISP o configuración de DNS local (split-horizon).

2. Si correo no llega:

   - `nslookup -type=MX domain.com` → comprobar prioridades y servidores MX.
   - `nslookup -type=TXT domain.com` → comprobar SPF/DKIM/DMARC.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 8**            | **Siguiente 10**         |
| ------------------ | ---------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./7_8_Tracert.md) | [⏩](./7_10_Iptables.md) |
