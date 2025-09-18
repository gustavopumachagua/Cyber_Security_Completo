| **Inicio**         | **atrás 10**          | **Siguiente 12**         |
| ------------------ | --------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_10_grep.md) | [⏩](./13_12_tracert.md) |

---

## **Índice**

| Temario                                        |
| ---------------------------------------------- |
| [432. nslookup](#432-nslookup)                 |
| [433. ¿Qué es Nslookup?](#433-qué-es-nslookup) |

# **nslookup**

## **432. nslookup**

![nslookup](/img/13_Tools_for_Incident_Response_and_Discovery/nslookup.jpg "nslookup")

### 1. ¿Qué es `nslookup`?

`nslookup` (**Name Server Lookup**) es una herramienta de red que sirve para consultar registros **DNS** (Domain Name System).
Permite obtener información como:

- Dirección IP de un dominio.
- Nombre de host asociado a una IP (resolución inversa).
- Consultar servidores de correo, registros NS, TXT, etc.

Es muy usado en **diagnóstico de red**, **ciberseguridad** y **administración de sistemas**.

### 2. Salida básica de `nslookup`

Ejemplo:

```bash
nslookup google.com
```

Salida típica:

```
Servidor:   8.8.8.8
Address:    8.8.8.8#53

Respuesta no autoritativa:
Nombre:     google.com
Address:    142.250.78.14
```

👉 Explicación:

- **Servidor** → servidor DNS al que preguntaste.
- **Respuesta no autoritativa** → respuesta obtenida de la caché, no directamente del servidor oficial.
- **Nombre / Address** → mapea dominio a IP.

### 3. `nslookup dedo`

El comando **`finger` o dedo** antiguamente servía para obtener información de usuarios en un sistema remoto.

En el contexto de `nslookup`, **"dedo"** es un error de traducción de "finger mode" → en algunos sistemas, `nslookup` puede trabajar de forma interactiva (modo "dedo" o "finger"), donde escribes consultas directamente sin volver a ejecutar el comando.

Ejemplo:

```bash
nslookup
> google.com
> set type=MX
> openai.com
```

### 4. Ayuda de `nslookup`

Para ver todas las opciones disponibles:

```bash
nslookup -h
```

o

```bash
man nslookup
```

### 5. `nslookup ls`

Permite listar los registros de una zona DNS (si el servidor lo permite).

Ejemplo:

```bash
nslookup
> ls -d ejemplo.com
```

👉 Hoy en día casi todos los servidores DNS bloquean esta opción por seguridad (enumeración de zonas).

### 6. `nslookup lserver`

Cambia el servidor DNS al que se consultan los registros.

Ejemplo:

```bash
nslookup
> lserver 8.8.8.8
```

👉 Ahora todas las consultas se hacen contra Google DNS.

### 7. `nslookup root`

Muestra o consulta los **servidores raíz DNS**.

Ejemplo:

```bash
nslookup
> root
```

### 8. `nslookup server`

Permite cambiar el servidor DNS directamente:

```bash
nslookup google.com 1.1.1.1
```

👉 Consulta el dominio `google.com` usando Cloudflare DNS.

### 9. `nslookup set` (opciones)

Dentro del modo interactivo puedes usar `set` para modificar parámetros:

#### 🔹 `nslookup set all`

Muestra todas las configuraciones actuales.

```bash
> set all
```

#### 🔹 `nslookup set class`

Define la clase DNS (normalmente `IN` = Internet).

```bash
> set class=IN
```

#### 🔹 `nslookup set d2`

Modo **depuración detallada** (debug nivel 2).

```bash
> set d2
```

👉 Muestra todo el tráfico entre el cliente y el servidor DNS.

#### 🔹 `nslookup set debug`

Activa depuración básica.

```bash
> set debug
```

#### 🔹 `nslookup set domain`

Define un dominio por defecto para consultas.

```bash
> set domain=ejemplo.com
```

#### 🔹 `nslookup set port`

Define el puerto del servidor DNS (por defecto 53).

```bash
> set port=5353
```

#### 🔹 `nslookup set querytype` (o `q`)

Define el tipo de registro a consultar:

- `A` → dirección IPv4
- `AAAA` → dirección IPv6
- `MX` → correo
- `NS` → nameserver
- `TXT` → registros de texto

Ejemplo:

```bash
> set type=MX
> gmail.com
```

#### 🔹 `nslookup set recurse`

Activa/desactiva la **resolución recursiva**.

```bash
> set recurse=no
```

#### 🔹 `nslookup set retry`

Define cuántos intentos hará antes de fallar.

```bash
> set retry=2
```

#### 🔹 `nslookup set root`

Muestra el servidor raíz que se usará para la consulta.

#### 🔹 `nslookup set search`

Activa el **modo búsqueda** (aplica sufijos de dominio).

#### 🔹 `nslookup set srchlist`

Define una lista de búsqueda de dominios.

#### 🔹 `nslookup set timeout`

Configura el tiempo de espera de la consulta.

```bash
> set timeout=5
```

#### 🔹 `nslookup set type`

Define el tipo de registro (igual que `querytype`).

```bash
> set type=NS
> openai.com
```

#### 🔹 `nslookup set vc`

Fuerza el uso de TCP en vez de UDP para la consulta.

```bash
> set vc
```

### 10. `nslookup view`

En algunas versiones, `view` muestra la vista actual de configuraciones y resultados según el servidor DNS.

### 🔑 Conclusión

- `nslookup` es una herramienta clave para diagnosticar problemas de **DNS**.
- Tiene un **modo interactivo** con comandos como `set`, `server`, `lserver`, `root`.
- Permite consultar **tipos específicos de registros DNS** (A, AAAA, MX, NS, TXT).
- Aunque hoy en día se prefiere `dig` o `host`, `nslookup` sigue siendo muy usado por su simplicidad.

---

[🔼](#índice)

---

## **433. ¿Qué es Nslookup?**

**`nslookup`** (Name Server Lookup) es una herramienta de línea de comandos para **consultas DNS**. Te permite preguntar a servidores DNS por registros (A, AAAA, MX, NS, TXT, PTR, SOA, CNAME, etc.), hacer búsquedas inversas (IP → nombre) y ajustar parámetros de la consulta (servidor, puerto, tipo de consulta, depuración). Es muy útil para diagnóstico de red y resolución de problemas DNS.

> Nota: `nslookup` existe en Linux, macOS y Windows. Hoy día muchos administradores prefieren `dig` o `host` por su flexibilidad, pero `nslookup` sigue siendo sencillo y ampliamente disponible.

### 1) Uso básico — forma no interactiva

La forma más simple (preguntar por la IP de un dominio):

```bash
nslookup ejemplo.com
```

Salida típica (simulada):

```
Server:  1.1.1.1
Address: 1.1.1.1#53

Non-authoritative answer:
Name:    ejemplo.com
Address: 93.184.216.34
```

**Qué significa:**

- **Server / Address**: el servidor DNS al que preguntaste.
- **Non-authoritative answer**: la respuesta vino de la caché del servidor consultado (no directamente del servidor autoritativo de la zona).
- **Name / Address**: el mapeo dominio → IP.

Consultar con un servidor específico (Cloudflare):

```bash
nslookup ejemplo.com 1.1.1.1
```

### 2) Consultas de tipo específico (A, MX, NS, TXT, etc.)

Puedes pedir registros concretos sin entrar al modo interactivo:

```bash
# MX (mail exchangers)
nslookup -type=MX gmail.com 8.8.8.8

# NS (nameservers)
nslookup -type=NS ejemplo.com 8.8.8.8

# TXT (por ejemplo SPF/DMARC)
nslookup -type=TXT example.com 1.1.1.1
```

Salida de ejemplo (MX):

```
Server:  8.8.8.8
Address: 8.8.8.8#53

Non-authoritative answer:
gmail.com    mail exchanger = 10 alt1.gmail-smtp-in.l.google.com.
...
```

### 3) Búsqueda inversa (PTR) — IP → nombre

```bash
nslookup 8.8.8.8
```

Salida:

```
Server:  1.1.1.1
Address: 1.1.1.1#53

Name: dns.google
Address: 8.8.8.8
```

Eso indica la entrada PTR configurada por el propietario de la IP.

### 4) Modo interactivo de `nslookup`

Si ejecutas `nslookup` sin argumentos entras en modo interactivo:

```bash
nslookup
> server 1.1.1.1        # elegir servidor
> set type=MX           # elegir tipo de consulta
> gmail.com             # consulta por MX de gmail.com
> set debug             # activar salida detallada (depuración)
> set vc                # usar TCP (virtual circuit) en vez de UDP
> set port=5353         # cambiar puerto
> exit
```

Comandos útiles en modo interactivo:

- `set type=<A|AAAA|MX|NS|TXT|SOA|PTR|ANY>` → tipo de consulta.
- `server <IP|host>` → cambiar servidor DNS con el que preguntar.
- `set debug` / `set d2` → activar más detalles (cabeceras, flags, TTL, etc.).
- `set vc` → forzar uso de TCP (útil para transferencias grandes o diagnóstico).
- `set port=<n>` → cambiar puerto (por ejemplo para servidores DNS que no usan 53).
- `set timeout=<s>` → ajustar tiempo de espera.
- `set retry=<n>` → número de reintentos.
- `ls -d <dominio>` → intentar un listado de zona (AXFR/zone transfer) — normalmente bloqueado por razones de seguridad.
- `set all` → muestra la configuración actual.
- `exit` → salir.

### 5) Ejemplo práctico paso a paso (interactivo)

```bash
$ nslookup
> server 8.8.8.8
Default server: 8.8.8.8
> set type=MX
> example.com
```

Salida (resumida):

```
Server: 8.8.8.8
Address: 8.8.8.8#53

example.com   mail exchanger = 10 mx1.example.com.
example.com   mail exchanger = 20 mx2.example.com.
```

### 6) Interpretación de respuestas y mensajes comunes

- **Non-authoritative answer**: respuesta desde caché o resolución recursiva del resolver consultado.
- **Authoritative answer**: proviene del servidor autoritativo para esa zona.
- **No response / Timeout**: puede indicar servidor DNS inaccesible, firewall bloqueando puerto 53, o que la consulta fue filtrada.
- **"Server can't find ..." / NXDOMAIN**: el nombre no existe en DNS.
- **Zone transfer refused**: `ls -d` o AXFR fue denegado por el servidor (es lo normal en servidores públicos).

### 7) Opciones prácticas y trucos

- **Especificar servidor inline (no interactivo):**

  ```bash
  nslookup -type=TXT example.com 1.1.1.1
  ```

- **Forzar TCP (útil para respuestas grandes o testing):**

  ```bash
  nslookup
  > set vc
  > example.com
  ```

- **Aumentar detalle (depuración):**

  ```bash
  nslookup
  > set debug
  > example.com
  ```

  Verás la sección adicional con la respuesta DNS detallada (TTL, flags, secciones ANSWER/AUTHORITY/ADDITIONAL).

- **Cambiar puerto (si el DNS escucha en otro puerto):**

  ```bash
  nslookup
  > set port=5353
  > example.com 192.0.2.53
  ```

### 8) Comparación rápida: `nslookup` vs `dig` vs `host`

- `nslookup`: simple, interactivo, disponible en muchos sistemas; buena para consultas rápidas.
- `dig`: más completa y orientada a diagnosticar (mejor para scripts y para ver secciones del mensaje DNS).
- `host`: interfaz simple para búsquedas de nombre/IPv4/IPv6 y PTR.
  Si necesitas trazas o salida más personalizable, `dig` suele ser preferida; para tareas rápidas `nslookup` está bien.

### 9) Cuándo usar `nslookup`

- Confirmar qué dirección IP devuelve un dominio.
- Ver registros MX/NS/TXT/SOA.
- Hacer búsquedas inversas (PTR).
- Diagnosticar problemas con un resolver concreto (probando con diferentes servidores: 8.8.8.8, 1.1.1.1, el resolver del ISP, o el autoritativo).
- Probar cambios DNS durante propagación (comparando respuestas entre servidores).

### 10) Ejercicios rápidos (para practicar)

1. Obtener A record usando Google DNS:

   ```bash
   nslookup ejemplo.com 8.8.8.8
   ```

2. Obtener MX de `gmail.com`:

   ```bash
   nslookup -type=MX gmail.com 8.8.8.8
   ```

3. Resolver 8.8.8.8 a un nombre (PTR):

   ```bash
   nslookup 8.8.8.8
   ```

4. Entrar en modo interactivo y activar `debug`:

   ```bash
   nslookup
   > set debug
   > example.com
   ```

### 11) Consejos de resolución de problemas (si no obtienes respuesta)

- Prueba con otro servidor DNS (`8.8.8.8`, `1.1.1.1`).
- Comprueba conectividad UDP/TCP al puerto 53 (`nc -v -u server 53` o `telnet server 53` con `set vc`).
- Si sólo falla en tu resolver local, reinicia el servicio de DNS (systemd-resolved / dnsmasq).
- Para ver si el cambio DNS ya se propagó, consulta directamente a los servidores autoritativos (buscar NS y luego preguntar esos servidores).

### 12) Consideraciones finales

- `nslookup` es ideal para comprobaciones rápidas y uso interactivo.
- Para análisis más profundo o scripting automatizado, valora usar `dig`.
- No olvides que muchas operaciones (como `ls -d`/AXFR) suelen bloquearse por seguridad — eso es normal.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 10**          | **Siguiente 12**         |
| ------------------ | --------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_10_grep.md) | [⏩](./13_12_tracert.md) |
