| **Inicio**         | **atrás 46**             | **Siguiente 48**              |
| ------------------ | ------------------------ | ----------------------------- |
| [🏠](../README.md) | [⏪](./13_46_UrlVoid.md) | [⏩](./13_48_Stakeholders.md) |

---

## **Índice**

| Temario                                                                                      |
| -------------------------------------------------------------------------------------------- |
| [502. Cómo utilizar el comando whois en Linux](#502-cómo-utilizar-el-comando-whois-en-linux) |
| [503. Búsqueda whois](#503-búsqueda-whois)                                                   |

# **Whois**

## **502. Cómo utilizar el comando whois en Linux**

![Whois](/img/13_Tools_for_Incident_Response_and_Discovery/Whois.webp "Whois")

### ¿Qué hace `whois`?

`whois` consulta la base de datos WHOIS de registradores y registros para devolver información pública sobre **dominios**, **nombres de host** y **direcciones IP**: registrar, fechas de creación/expiración, name servers, contactos (registrant/admin/tech), estado del dominio y a veces contactos de abuso. Es la herramienta clásica para obtener metadatos de un dominio.

### Instalación (rápida)

- Debian / Ubuntu:

```bash
sudo apt update
sudo apt install whois
```

- RHEL / CentOS / Fedora:

```bash
sudo dnf install whois    # o yum install whois en versiones antiguas
```

- macOS (Homebrew):

```bash
brew install whois
```

### Uso básico

```bash
whois ejemplo.com
```

Eso consulta automáticamente al servidor WHOIS correspondiente y devuelve el texto con la metadata del dominio.

### Consultas concretas (por tipo)

#### 1) Dominio (ejemplo)

```bash
whois example.com
```

Salida típica (extracto; cambia según TLD):

```
   Domain Name: EXAMPLE.COM
   Registry Domain ID: 2336799_DOMAIN_COM-VRSN
   Registrar: EXAMPLE REGISTRAR, INC.
   Registrar WHOIS Server: whois.example-registrar.com
   Creation Date: 1995-08-14T04:00:00Z
   Registry Expiry Date: 2026-08-13T04:00:00Z
   Name Server: A.IANA-SERVERS.NET
   Name Server: B.IANA-SERVERS.NET
   DNSSEC: signedDelegation
   Registrant Organization: Example Organization
   Registrant Country: US
```

**Qué fijarte:** `Creation Date`, `Registry Expiry Date`, `Registrar`, `Name Server(s)`, `Registrant` (puede estar redacted/privacy), y `DNSSEC`.

#### 2) IP / ASN

Puedes consultar información de quien gestiona una IP (registro regional ARIN, RIPE, APNIC, etc.):

```bash
whois 8.8.8.8
# o especificar servidor WHOIS:
whois -h whois.arin.net 8.8.8.8
```

Salida típica (extracto):

```
NetRange:       8.8.8.0 - 8.8.8.255
CIDR:           8.8.8.0/24
NetName:        GOOGLE
OrgName:        Google LLC
Address:        ...
Country:        US
NetType:        Reallocated
```

#### 3) Forzar consulta a un servidor WHOIS concreto

Si conoces el servidor WHOIS que quieres interrogar:

```bash
whois -h whois.nic.es ejemplo.es
```

(`-h` es la opción estándar para indicar host/servidor WHOIS).

### Interpretación: campos frecuentes y qué significan

- **Domain Name** – dominio consultado.
- **Registrar** – empresa que administra el registro del dominio.
- **Registrar WHOIS Server** – servidor WHOIS del registrador (útil si la respuesta del registro es escueta).
- **Creation Date** / **Updated Date** / **Expiry Date** – fechas clave (vida del dominio).
- **Name Server(s)** – servidores DNS autoritativos.
- **Registrant / Admin / Tech contacts** – datos del propietario y contactos técnicos (a menudo redacted por privacidad).
- **Status** – estados como `clientTransferProhibited`, `ok`, `pendingDelete` indican restricciones/estado legal.
- **DNSSEC** – indica si DNSSEC está activado (`signedDelegation` etc.).
- **Registry Domain ID / Domain ID** – identificadores internos del registro.

> Nota: la salida exacta y el idioma de los campos dependen del TLD y del registrador; para ccTLDs (por ejemplo `.es`, `.pe`, `.cl`) el formato cambia bastante.

### Extraer campos prácticos (grep / awk) — comandos útiles

#### Fecha de expiración

```bash
whois ejemplo.com | grep -i 'Expiry\|Expiration\|Registry Expiry Date'
# o una línea más tolerante:
whois ejemplo.com | grep -iE 'expire|expir|registry expiry|paid-till'
```

#### Registrar y name servers

```bash
whois ejemplo.com | egrep -i 'Registrar:|Name Server:'
```

#### Extraer dominio y fechas con awk (ejemplo)

```bash
whois example.com | awk -F: '/Creation Date|Registry Expiry Date/ {print $1": "$2}'
```

#### Script corto que extrae campos clave y los muestra ordenados

```bash
whois example.com | awk '
/Domain Name/ {print "Domain:", $0}
/Registrar:/ {print "Registrar:", substr($0, index($0,$2))}
/Creation Date/ {print "Created:", $0}
/Registry Expiry Date/ {print "Expires:", $0}
/Name Server/ {print "NS:", $0}'
```

(Adapta patrones según salida del whois para tu TLD).

### Desacoplar shorteners / ver la URL final sin visitar (con whois no se sigue redirecciones)

`whois` solo da metadatos del dominio; para conocer la URL final de un acortador no uses `whois` (usa urlscan/VirusTotal u otras técnicas seguras). `whois` te ayudará a evaluar el dominio al que apunta un shortener cuando hayas extraído el host.

### Buenas prácticas y precauciones

- **Calcula hash y registra** la evidencia antes de subir o compartir detalles.
- **Respeta límites de consultas**: registradores y registros a menudo limitan tasa de consultas; si haces muchas peticiones, podrías ser bloqueado.
- **Privacidad:** muchos WHOIS muestran `REDACTED FOR PRIVACY`; la información real puede estar oculta detrás de servicios de privacidad.
- **No confíes únicamente en whois** para decidir si un sitio es malicioso: combina con DNS, passive DNS, VirusTotal, urlscan, APIVoid, logs internos.
- **Para búsquedas masivas** usa APIs oficiales (RDAP o servicios de reputación) y respeta términos de uso.

### Rate limiting y alternativas estructuradas

- Los WHOIS tradicionales son texto, no estructurado; varios TLDs limitan consultas automáticas.
- **RDAP** (Registration Data Access Protocol) es el reemplazo moderno que devuelve JSON estructurado y es más amigable para automatizar (muchos registros soportan RDAP). Si vas a integrar, considera RDAP + caching.

### Ejemplos prácticos completos

#### Ejemplo A — Buscar datos básicos de `example.com`:

```bash
whois example.com | egrep -i 'domain name:|registry expiry date:|name server:|registrar:'
```

Salida simulada:

```
Domain Name: EXAMPLE.COM
Registrar: EXAMPLE REGISTRAR, INC.
Registry Expiry Date: 2026-08-13T04:00:00Z
Name Server: A.IANA-SERVERS.NET
Name Server: B.IANA-SERVERS.NET
```

#### Ejemplo B — Comprobar IP pública / obtener organización:

```bash
whois 203.0.113.45
```

Salida simulada:

```
NetRange:       203.0.113.0 - 203.0.113.255
OrgName:        ACME HOSTING LTD
Country:        US
```

#### Ejemplo C — Usar servidor WHOIS del registrador (si el registro central devuelve poco)

1. Primero obtienes el `Registrar WHOIS Server` desde `whois`:

```bash
whois ejemplo.com | grep -i 'Registrar WHOIS Server'
# Registrar WHOIS Server: whois.nic-example-registrar.com
```

2. Luego consultas directamente:

```bash
whois -h whois.nic-example-registrar.com ejemplo.com
```

Algunos registradores ofrecen información más completa en su WHOIS.

### Automatización: ejemplo de pequeño script Bash que extrae campos clave a CSV

```bash
#!/usr/bin/env bash
domain="$1"
if [ -z "$domain" ]; then
  echo "Uso: $0 dominio"
  exit 1
fi

out=$(whois "$domain")

created=$(echo "$out" | egrep -i 'creation date|created on|created:' | head -n1 | sed 's/^[^:]*:[[:space:]]*//I')
expires=$(echo "$out" | egrep -i 'expiry date|expiration date|registry expiry date|paid-till' | head -n1 | sed 's/^[^:]*:[[:space:]]*//I')
registrar=$(echo "$out" | egrep -i '^Registrar:' | head -n1 | sed 's/^[^:]*:[[:space:]]*//I')
nameservers=$(echo "$out" | egrep -i 'name server' | awk '{print $NF}' | tr '\n' ';' | sed 's/;$//')

echo "domain,registrar,created,expires,nameservers"
echo "\"$domain\",\"$registrar\",\"$created\",\"$expires\",\"$nameservers\""
```

Guarda como `whois2csv.sh`, `chmod +x`, y usa: `./whois2csv.sh example.com`.

### Qué hacer si la información está "REDACTED" o el dominio usa privacidad

- Consulta `Registrar WHOIS Server` y revisa si el registrador ofrece mecanismo de contacto para reportar abuso (abuse contact).
- Usa **DNS**, **Passive DNS**, **RDAP**, servicios de reputación y sandboxing (urlscan, VirusTotal) para enriquecer la investigación.
- Para incidentes reales, reporta al registrador o proveedor de hosting con la evidencia recogida.

### Resumen rápido (cheat-sheet)

- Instalar: `sudo apt install whois`
- Consulta básica: `whois ejemplo.com`
- Forzar servidor: `whois -h whois.arin.net 8.8.8.8`
- Extraer expiración: `whois ejemplo.com | grep -i 'expir'`
- Extraer registrar & NS: `whois ejemplo.com | egrep -i 'registrar:|name server:'`
- Automatizar / parsear: combinar `whois` + `grep`/`awk`/`sed` o usar RDAP para JSON estructurado.
- Complementa siempre con DNS, passive DNS, VirusTotal, urlscan y feeds TI.

---

[🔼](#índice)

---

## **503. Búsqueda whois**

### 🔎 1. ¿Qué es una búsqueda de dominio Whois?

Una **búsqueda de dominio Whois** es una consulta a una base de datos pública que contiene información sobre quién registró un dominio en Internet.

- Sirve para saber **quién es el dueño de un dominio, con qué registrador se contrató, cuándo fue creado y cuándo expira**.
- Ejemplo: si buscas `openai.com` en un servicio Whois, podrás ver su fecha de registro y el registrador que lo administra.

### 📂 2. ¿Qué contiene la base de datos del dominio Whois?

Depende de la política de privacidad, pero normalmente incluye:

- **Datos de registro del dominio**:

  - Nombre del registrador (GoDaddy, Namecheap, etc.)
  - Nombre del registrante (persona/empresa dueña)
  - Información de contacto (email, dirección, teléfono) → a veces oculto.

- **Fechas**: creación, última actualización y expiración.
- **Servidores DNS** asociados.
- **Estado del dominio** (activo, suspendido, en disputa, etc.).

Ejemplo (simplificado para `openai.com`):

```
Registrar: MarkMonitor Inc.
Creation Date: 2007-04-16
Expiration Date: 2028-04-16
Name Servers: ns1.openai.com, ns2.openai.com
```

### 🌐 3. ¿Qué es una búsqueda de IP Whois?

En vez de consultar un dominio, consultas una **dirección IP**.

- Muestra información sobre **qué organización administra esa IP** (ISP, empresa, proveedor de nube).
- Ejemplo: si haces `whois 8.8.8.8`, verás que pertenece a **Google LLC**.

### 🖥 4. ¿Cómo realizo una búsqueda Whois?

Tienes varias formas:

1. **En Linux/Mac** (con el comando integrado):

   ```bash
   whois openai.com
   whois 8.8.8.8
   ```

2. **Con herramientas online** como `whois.domaintools.com` o `whois.com/whois/`.
3. **Con APIs** para automatizar (ej. `whoisxmlapi.com`).

-

### 🔄 5. ¿Cómo mantengo mi información Whois actualizada?

- Inicia sesión en el panel de tu **registrador de dominios** (ej. GoDaddy, Namecheap).
- Actualiza tus datos de contacto (nombre, email, teléfono).
- Los registradores **están obligados por ICANN** a mantener la información precisa y verificable.

### 🛡 6. ¿Qué medidas puedo tomar para garantizar que la privacidad de mi dominio esté protegida?

- Usar **servicios de privacidad Whois** (también llamados _WhoisGuard_, _Privacy Protection_).
- Estos reemplazan tus datos reales con los de un servicio intermediario.
- Ejemplo: en vez de mostrar tu email personal, aparece `proxy@whoisprivacy.com`.

### 👀 7. ¿Por qué algunas entradas están ocultas en mi búsqueda de dominio Whois?

- Muchas veces se debe a la **ley GDPR (protección de datos en Europa)**.
- También puede ser porque el dueño activó **privacidad Whois**.
- Por eso, en muchos dominios verás campos como:

  ```
  Registrant Name: REDACTED FOR PRIVACY
  Registrant Email: REDACTED FOR PRIVACY
  ```

### ✏️ 8. Mi información no coincide con los resultados de Whois, ¿cómo puedo cambiar mi información de Whois?

- Debes hacerlo a través de tu **registrador de dominios**.
- Una vez modificada, la información se actualizará en la base Whois en minutos o hasta 48 horas.
- Ejemplo: si cambias tu email de contacto en Namecheap, después de unas horas el Whois reflejará ese cambio.

### 🛒 9. ¿Puedo registrar nuevos dominios a través de la búsqueda de dominio Whois?

- **No directamente** desde la base Whois.
- Pero si el dominio aparece como **"not found"** o **"available"**, puedes ir a un registrador y comprarlo.

### 🔍 10. ¿Cómo puedo encontrar dominios disponibles a través de la base de datos Whois?

- Si haces una búsqueda y no hay resultados → significa que el dominio está libre.
- También puedes usar **buscadores de dominios** (ej. GoDaddy, Namecheap) que ya integran consultas Whois y te dicen si está disponible.
- Ejemplo:

  ```bash
  whois mi-nuevo-proyecto123.com
  ```

  Si devuelve `No match for domain "MI-NUEVO-PROYECTO123.COM"`, está disponible para registrar.

✅ **En resumen:**

Whois es como la **cédula de identidad de dominios e IPs**. Sirve para averiguar quién los administra, cuándo se registraron y a qué organización pertenecen. Además, permite verificar disponibilidad de dominios y mantener actualizada la información de contacto, siempre cuidando la privacidad.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 46**             | **Siguiente 48**              |
| ------------------ | ------------------------ | ----------------------------- |
| [🏠](../README.md) | [⏪](./13_46_UrlVoid.md) | [⏩](./13_48_Stakeholders.md) |
