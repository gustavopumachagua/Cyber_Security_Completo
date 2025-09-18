| **Inicio**         | **atrás 2**         | **Siguiente 4**        |
| ------------------ | ------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./7_2_Ping.md) | [⏩](./7_4_Netstat.md) |

---

## **Índice**

| Temario                                                                          |
| -------------------------------------------------------------------------------- |
| [164. Cómo buscar registros DNS con dig](#164-cómo-buscar-registros-dns-con-dig) |

# **Dig**

## **164. Cómo buscar registros DNS con dig**

![Dig](/img/7_Troubleshooting_Tools/Dig.png "Dig")

### ¿Qué es `dig`?

`dig` (Domain Information Groper) es una utilidad de línea de comandos para consultar servidores DNS. Es parte de la suite BIND y es la herramienta estándar para diagnosticar DNS porque devuelve mucha información y es altamente configurable.

### Sintaxis básica

```bash
dig [@servidor] [nombre] [tipo] [opciones]
# Ejemplo mínimo:
dig @8.8.8.8 ejemplo.com A
# Equivalente corto (usar el resolver por defecto):
dig ejemplo.com A
```

- `@servidor` → opcional: dirección o nombre del servidor DNS a consultar (p. ej. `@1.1.1.1` o `@ns1.midns.com`).
- `nombre` → dominio o subdominio (`example.com`, `_dmarc.example.com`, `selector._domainkey.example.com`).
- `tipo` → A, AAAA, MX, NS, TXT, SOA, CNAME, SRV, PTR, DNSKEY, DS, ANY, AXFR, etc.
- `opciones` → `+short`, `+trace`, `+tcp`, `+noall`, `+answer`, `+dnssec`, etc.

### Secciones de la respuesta de `dig` (qué ver e interpretar)

Una salida típica tiene secciones:

- `HEADER` → metadatos: flags (qr, aa, rd, ra, ad, cd), opcode, status.
- `QUESTION SECTION` → qué se preguntó.
- `ANSWER SECTION` → las respuestas (name TTL class type RDATA).
- `AUTHORITY SECTION` → servidores autoritativos (delegación).
- `ADDITIONAL SECTION` → datos extra (IP de los NS, RRSIG, etc).
- Estadísticas al final: tiempo total, servidores consultados.

**Línea de ANSWER típica:**

```
example.com.  3599  IN  A   93.184.216.34
# name   TTL  class type data
```

### Comandos y ejemplos prácticos

#### 1) Obtener la IP (A) y AAAA

```bash
dig ejemplo.com A
dig ejemplo.com AAAA
# salida concisa:
dig +short ejemplo.com
```

`+short` imprime solo el RDATA (útil en scripts).

#### 2) Consultar servidores autoritativos (NS)

```bash
dig ejemplo.com NS
# comprobar un NS específico:
dig @ns1.ejemplo.com ejemplo.com A
```

#### 3) MX, TXT (SPF/DMARC/DKIM)

```bash
dig ejemplo.com MX +short
dig ejemplo.com TXT +short
# DKIM: consulta selector._domainkey
dig selector._domainkey.example.com TXT +short
# DMARC:
dig _dmarc.example.com TXT +short
```

#### 4) SOA (serial, refresh, retry...)

```bash
dig example.com SOA +short
# devuelve: mname rname serial refresh retry expire minimum
```

El `serial` suele indicar la versión de zona; formato común: `YYYYMMDDnn`.

#### 5) SRV (servicios)

```bash
dig _sip._tcp.example.com SRV +short
# formato: priority weight port target
```

#### 6) Reverse lookup (PTR)

```bash
dig -x 8.8.8.8 +short
# ejemplo devuelve: dns.google.
```

#### 7) Seguimiento de delegación (`+trace`)

```bash
dig +trace ejemplo.com
```

`+trace` hace la resolución recursiva manualmente desde los root servers y muestra cada paso de la delegación — útil para ver dónde falla la delegación.

#### 8) Forzar TCP (útil si la respuesta está truncada o para AXFR)

```bash
dig +tcp ejemplo.com ANY
```

#### 9) Intentar transferencia de zona (AXFR) — solo si está permitido

```bash
dig @ns1.ejemplo.com example.com AXFR
# la mayoría de servidores rechaza AXFR a clientes no autorizados.
```

#### 10) DNSSEC — ver firmas (RRSIG) y verificación

```bash
dig example.com DNSKEY +dnssec
dig example.com A +dnssec
# fíjate en RRSIG en las secciones ANSWER/ADDITIONAL; el flag AD en HEADER indica que el resolver validó la firma.
```

### Opciones útiles (las que vas a usar siempre)

- `+short` → salida mínima (solo RDATA). Ideal en scripts.
- `+noall +answer` → solo muestra la sección ANSWER (orden para debugging).
- `+trace` → seguimiento de delegación (ver root → TLD → NS).
- `+dnssec` → solicita y muestra RRSIG/DNSSEC.
- `+tcp` → fuerza TCP en lugar de UDP.
- `-p <puerto>` → cambiar puerto (ej. si DNS está en puerto no estándar).
- `+time=<seg>` y `+tries=<n>` → tiempo de espera y reintentos.
- `+multiline` → formatea registros TXT y RRSIG de forma legible.
- `+nocmd` / `+nocomments` / `+nostats` → limpiar salida (útil en scripts).
- `-x <ip>` → reverse lookup (sin especificar -x también funciona como `dig -x ip`).

### Ejemplos con salida anotada

#### Ejemplo: `dig example.com A`

Salida simplificada:

```
; <<>> DiG 9.16.1 <<>> example.com A
;; ANSWER SECTION:
example.com.  3599  IN  A  93.184.216.34
```

Interpreta: `example.com` tiene A=93.184.216.34 con TTL 3599s.

#### Ejemplo: `dig @8.8.8.8 gmail.com MX +short`

Salida:

```
10 gmail-smtp-in.l.google.com.
20 alt1.gmail-smtp-in.l.google.com.
```

Interpreta prioridades MX (10 antes que 20).

#### Ejemplo: `dig +noall +answer _dmarc.example.com TXT`

Salida:

```
_dmarc.example.com. 3600 IN TXT "v=DMARC1; p=quarantine; rua=mailto:postmaster@example.com"
```

útil para verificar política DMARC.

### Diagnóstico: comparar distintos resolvers

Para detectar problemas de propagación o cachés:

```bash
dig @8.8.8.8 ejemplo.com A +short     # Google public DNS
dig @1.1.1.1 ejemplo.com A +short     # Cloudflare
dig ejemplo.com A +short              # tu resolver local (DHCP/ISP)
```

Si los resultados difieren, puede ser por caché o por diferencias en la vista del servidor autoritativo.

### `dig` en scripts — ejemplos prácticos

#### 1) Obtener IP A en una variable (bash)

```bash
IP=$(dig +short ejemplo.com A | head -n1)
echo "IP de ejemplo.com = $IP"
```

#### 2) Comprobar que un registro MX existe (retorna 0/1)

```bash
if dig +short ejemplo.com MX | grep -q '\.'; then
  echo "MX encontrado"
else
  echo "No existe MX"
fi
```

#### 3) Shell script para comprobar serial SOA (propagación)

```bash
current_serial=$(dig +short example.com SOA | awk '{print $3}')
echo "Serial actual: $current_serial"
```

Puedes comparar con el serial en el servidor autoritativo para verificar si la zona se actualizó.

### Trucos y pitfalls (lo que debes saber)

- `ANY` ya no es fiable: muchos servidores devuelven respuestas truncadas o vacías por política (RFCs y abusos).
- Si recibes **no answer pero no NXDOMAIN**, revisa `AUTHORITY SECTION` (puede que la delegación esté rota).
- **ICMP/Firewall** no afecta a `dig` (DNS usa UDP/TCP 53). Pero servidores DNS pueden bloquear consultas externas.
- TTL alto → los cambios tardan en propagarse. Comprueba SOA serial para saber si la zona cambió.
- `+trace` evita usar el resolver recursivo y te muestra la delegación desde la raíz (muy útil para delegación rota).
- `AD` en el HEADER indica que el resolver afirma la validación DNSSEC (si usas un resolver que valida).

### Comandos avanzados / específicos

- Forzar registro DNS dinámico (Windows DNS con `ipconfig /registerdns`) — pero con `dig` puedes usar `nsupdate` para actualizaciones dinámicas (TSIG).
- Probar transferencia de zona:

```bash
dig @ns1.example.com example.com AXFR
```

- Forzar EDNS (tamaños grandes):

```bash
dig +edns=0 ejemplo.com
```

- Ver las estadísticas y tiempos:

```bash
dig ejemplo.com +stats
# verás Query time: X msec
```

### Interpretación de flags importantes en HEADER

En la línea `;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 12345` fijate en:

- `status: NOERROR` → OK; `NXDOMAIN` → no existe.
- flags: `qr` (response), `aa` (authoritative answer), `rd` (recursion desired), `ra` (recursion available), `ad` (authenticated data), `cd` (checking disabled).

### Dónde usar `dig`

- Troubleshooting DNS (propagación, delegación, DNSSEC).
- Validar configuración de correo (MX, SPF, DKIM, DMARC).
- Verificar servicios (SRV) y descubrimientos (SIP, LDAP, XMPP).
- Automatización: integrarlo en scripts de CI/CD para testear infra DNS.

### Cómo acceder a `dig`

- **Linux:** generalmente preinstalado en paquetes `bind9-dnsutils`, `dnsutils` o similar: `sudo apt install dnsutils`.
- **macOS:** suele venir con `dig` (o instalar con Homebrew `bind`).
- **Windows:** no viene por defecto; instalar BIND tools o usar WSL (Linux) / Windows Server RSAT (tiene herramientas DNS) o usar `nslookup` (menos potente).

### Ejemplo final — checklist de comandos rápidos

```bash
# IP A
dig example.com A +short

# MX
dig example.com MX +short

# TXT (SPF/DMARC/DKIM)
dig example.com TXT +short
dig selector._domainkey.example.com TXT +short

# Reverse
dig -x 93.184.216.34 +short

# SOA serial
dig example.com SOA +short

# Delegation trace
dig +trace example.com

# DNSSEC keys
dig example.com DNSKEY +dnssec

# Forzar servidor
dig @1.1.1.1 example.com A +short

# Zona AXFR (si el servidor lo permite)
dig @ns1.example.com example.com AXFR
```

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**         | **Siguiente 4**        |
| ------------------ | ------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./7_2_Ping.md) | [⏩](./7_4_Netstat.md) |
