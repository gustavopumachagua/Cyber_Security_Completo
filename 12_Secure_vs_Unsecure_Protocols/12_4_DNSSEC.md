| **Inicio**         | **atrás 3**           | **Siguiente 5**       |
| ------------------ | --------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./12_3_IPSec.md) | [⏩](./12_5_LDAPS.md) |

---

## **Índice**

| Temario                                                |
| ------------------------------------------------------ |
| [364. Cómo funciona DNSSEC](#364-cómo-funciona-dnssec) |
| [365. ¿Qué es DNSSEC?](#365-qué-es-dnssec)             |

# **DNS Security Extensions (DNSSEC)**

## **364. Cómo funciona DNSSEC**

![DNSSEC](/img/12_Secure_vs_Unsecure_Protocols/DNSSEC.jpg "DNSSEC")

### 🔑 1. Definición de DNSSEC

**DNSSEC (Domain Name System Security Extensions)** es una extensión de seguridad para el **DNS** que agrega **firmas digitales** a los registros DNS.

👉 El objetivo no es **cifrar** (como en HTTPS), sino **garantizar integridad y autenticidad** de las respuestas DNS, evitando ataques como:

- **DNS spoofing / cache poisoning** (inyectar respuestas falsas).
- **Suplantación de servidores** (resolver un dominio a un servidor controlado por el atacante).

### 🔧 2. ¿Cómo funciona DNSSEC?

Cuando un usuario consulta un dominio (ejemplo: `www.ejemplo.com`), el flujo normal de DNSSEC es:

1. El **resolver** (tu proveedor de Internet o DNS público como 8.8.8.8) pide la dirección IP al servidor DNS autoritativo.
2. El servidor autoritativo responde con el registro (ejemplo: `93.184.216.34`) **más una firma digital (RRSIG)**.
3. El resolver valida esa firma usando la **clave pública DNSKEY** del dominio.
4. Si la firma es válida ✅, se acepta la respuesta. Si falla ❌, se descarta (mejor no resolver que resolver mal).

👉 Así, DNSSEC agrega **confianza criptográfica** a DNS.

### 📌 3. Conceptos clave de DNSSEC

Para entenderlo bien, necesitas conocer los registros extra que introduce DNSSEC:

- **DNSKEY** 🔑 → contiene la clave pública del dominio.
- **RRSIG** ✍️ → firma digital de un conjunto de registros DNS (ej. firma del registro A o MX).
- **DS (Delegation Signer)** → vínculo entre una zona y su subzona, usado en la cadena de confianza.
- **NSEC / NSEC3** 🚫 → pruebas firmadas de no-existencia (ejemplo: “ese subdominio no existe, y aquí está la prueba criptográfica”).

Ejemplo simplificado de registros con DNSSEC:

```text
ejemplo.com.     3600  IN A      93.184.216.34
ejemplo.com.     3600  IN RRSIG  A 8 2 3600 (firma digital...)
ejemplo.com.     3600  IN DNSKEY 257 3 8 (clave pública...)
```

### 🛡️ 4. ¿Cómo se protegen las zonas con DNSSEC?

Una **zona DNS** (ej. `ejemplo.com`) se asegura con **pares de claves criptográficas**:

1. **KSK (Key Signing Key):** clave usada para firmar otras claves DNSKEY.
2. **ZSK (Zone Signing Key):** clave usada para firmar los registros DNS de la zona (A, MX, TXT, etc.).

Flujo:

- El ZSK firma los registros (ej. registros A).
- El KSK firma el ZSK (para verificar que es válido).
- Se publica un **registro DS** en la zona padre (ej. `.com` guarda el DS de `ejemplo.com`).

Esto asegura que cualquier cambio no autorizado en los registros será detectado.

### 🔗 5. La cadena de confianza (DNSSEC chain of trust)

DNSSEC funciona porque hay una **cadena jerárquica de confianza**:

1. La **zona raíz (“.”)** tiene su propia clave pública bien conocida (instalada en los resolvers como "trust anchor").
2. La raíz firma el **TLD** (ej. `.com`).
3. El TLD firma el **dominio** (ej. `ejemplo.com`).
4. El dominio firma sus propios registros (ej. `A`, `MX`).

👉 Si una pieza de la cadena no es válida, la validación DNSSEC falla y el resolver no devuelve respuesta.

Ejemplo de cadena de confianza para `www.ejemplo.com`:

```
Raíz (.) → .com → ejemplo.com → www.ejemplo.com
```

### 📊 6. Resumen visual del flujo

1. Resolver pide `www.ejemplo.com A`.
2. Autoritativo responde con `A` + `RRSIG`.
3. Resolver obtiene la `DNSKEY` de `ejemplo.com`.
4. Comprueba que `RRSIG` fue generado con esa clave.
5. Comprueba que `DNSKEY` de `ejemplo.com` está validada por el **DS en .com**.
6. Comprueba que `.com` está validada por la **raíz**.
7. Si todo encadena → respuesta válida.

### ✅ 7. Beneficios de DNSSEC

- Evita **respuestas falsas en DNS**.
- Protege contra **ataques de cache poisoning**.
- Garantiza que el usuario llegue al servidor **auténtico**.
- Base para tecnologías adicionales como **DANE (DNS-based Authentication of Named Entities)**.

### ⚠️ 8. Limitaciones de DNSSEC

- No cifra el tráfico DNS (para eso se usa **DoH/DoT**).
- Configuración y mantenimiento complejos (rotación de claves, sincronización).
- Aumenta el tamaño de las respuestas DNS (más riesgo de fragmentación/DoS).

---

[🔼](#índice)

---

## **365. ¿Qué es DNSSEC?**

**DNSSEC (Domain Name System Security Extensions)** es un conjunto de extensiones al DNS cuyo objetivo es **garantizar la integridad y la autenticidad** de las respuestas DNS.
No cifra los datos; lo que hace es **firmar digitalmente** los registros DNS para que un resolver pueda comprobar que:

1. La respuesta proviene realmente de la zona autorizada (autenticidad).
2. La respuesta no fue alterada en tránsito (integridad).

Con DNSSEC se previenen ataques como _cache poisoning_ o _DNS spoofing_.

### Conceptos y registros clave

- **RRSet**: conjunto de registros con mismo nombre y mismo tipo (por ejemplo, todos los `A` de `www.ejemplo.com`). DNSSEC firma RRsets, no registros individuales.
- **DNSKEY**: contiene una clave pública (la contraparte de la clave privada que firma).
- **RRSIG**: firma digital de un RRSet. Cada RRSet firmado produce un registro `RRSIG`.
- **DS (Delegation Signer)**: registro en la zona padre que apunta a la clave pública (hash) de la subzona. Es el «vínculo» entre padre e hijo en la cadena de confianza.
- **NSEC / NSEC3**: mecanismos para probar de forma firmada que un nombre NO existe. `NSEC` revela nombres vecinos (permite _zone walking_), `NSEC3` evita revelar nombres al usar hashes.
- **KSK (Key Signing Key)** y **ZSK (Zone Signing Key)**:

  - **ZSK**: firma los RRsets de la zona.
  - **KSK**: firma las claves DNSKEY (es decir, firma a la ZSK). El DS que se pone en la zona padre suele corresponder al KSK.

### Cómo funciona (flujo simplificado — la cadena de confianza)

1. El resolver recursivo consulta `www.ejemplo.com`.
2. El servidor autoritativo responde con el registro solicitado **y** con su `RRSIG`.
3. El resolver descarga la `DNSKEY` del dominio (o la trae de cache), verifica la `RRSIG` contra la `DNSKEY`.
4. Para confiar en la `DNSKEY`, el resolver comprueba el `DS` publicado en la zona padre (ej. `.com`) y valida la cadena hasta la raíz.
5. La raíz es el _trust anchor_ conocido por el resolutor (o administrado manualmente).
   Si todo encadena → la respuesta es **AD** (authenticated). Si falla → la respuesta se rechaza.

Diagrama simple:

```
Raíz (.) -> TLD (.com) -> ejemplo.com -> www.ejemplo.com
    DS         DS           DNSKEY       RRSIG sobre A record
```

### Ejemplo práctico: firmar una zona con BIND (pasos clave)

Supongamos que tienes una zona `example.com` y BIND9 instalado.

1. **Archivo de zona (simplificado)** `/var/named/example.com.db`:

```text
$ORIGIN example.com.
$TTL 3600
@   IN SOA  ns1.example.com. admin.example.com. (
        2025091301 ; serial
        7200       ; refresh
        3600       ; retry
        1209600    ; expire
        3600 )     ; minimum

    IN NS   ns1.example.com.
ns1 IN A    198.51.100.10
www IN A    198.51.100.20
```

2. **Generar claves (ZSK + KSK)** con `dnssec-keygen`:

```bash
# KSK (marca -f KSK)
dnssec-keygen -a RSASHA256 -b 4096 -n ZONE -f KSK example.com
# ZSK
dnssec-keygen -a RSASHA256 -b 2048 -n ZONE example.com
```

Esto crea ficheros `Kexample.com.+008+NNNNN.key` y `.private`. El KSK tiene la bandera KSK.

3. **Incluir las .key en el archivo de zona** (o dejar que `dnssec-signzone` las añada automáticamente). Ejemplo:

```text
$INCLUDE Kexample.com.+008+12345.key
$INCLUDE Kexample.com.+008+54321.key
```

4. **Firmar la zona**:

```bash
dnssec-signzone -A -3 $(head -c 1000 /dev/urandom | sha1sum | cut -b1-16) \
  -N increment -o example.com -t /var/named/example.com.db
```

Esto genera `example.com.db.signed` con `RRSIG` y `DNSKEY` añadidos.

5. **Comprobar la zona firmada**:

```bash
named-checkzone example.com /var/named/example.com.db.signed
```

6. **Obtener el DS (para el padre)** y enviar ese DS al registrar:

```bash
# Parte con KSK: generar DS para publicar en la zona padre
dnssec-dsfromkey -2 Kexample.com.+008+12345.key
# -2 = SHA-256 digest (recomendado)
```

El comando imprime algo como:

```
example.com. IN DS 12345 8 2 <digest>
```

Ese `DS` es el que debes registrar en tu **registrar** (panel del dominio). Sin publicar el DS en la zona padre la cadena de confianza NO estará completa.

### Cómo comprobar que DNSSEC está funcionando (comandos)

- Ver respuesta firmada (muestra RRSIG):

```bash
dig +dnssec @<tus-resolutores> example.com A
```

Busca en la respuesta el bloque `;; ANSWER SECTION` con `RRSIG` justo debajo del `A` record.

- Para ver la `DNSKEY`:

```bash
dig +dnssec example.com DNSKEY
```

- Comprobar DS en la zona padre (consultar servidores TLD):

```bash
dig +short DS example.com @a.gtld-servers.net
```

- Ver si el resolver marcó la respuesta como autenticada (AD bit):

```bash
dig @1.1.1.1 +dnssec example.com A
# fíjate en las flags: ... ad ...
```

Si la respuesta contiene `ad` en flags significa que el resolver la validó y consideró la respuesta auténtica.

### NSEC vs NSEC3 (prueba de no-existencia)

- **NSEC**: cuando pides `sub.ejemplo.com` que no existe, el servidor devuelve un `NSEC` que demuestra qué nombre sigue al solicitado (prueba firmada de inexistencia). Problema: permite _zone walking_ (enumerar todos los nombres de la zona).
- **NSEC3**: devuelve hashes en vez de nombres claros, mitigando el zone walking aunque no lo anula totalmente (aumenta coste para enumerar).

### Rollover de claves (resumen práctico)

- **ZSK rollover (más simple)**:

  1. Generas nueva ZSK.
  2. Publicas la nueva DNSKEY junto con la vieja (ambas en la zona).
  3. Vuelves a firmar la zona con la nueva ZSK y mantener la vieja un tiempo (para que los resolvers con cache validen).
  4. Retiras la ZSK vieja después de plazo de seguridad.

- **KSK rollover (más delicado)**:

  1. Generas nueva KSK.
  2. Publicas nueva KSK en la zona (junto con la antigua).
  3. Generas nuevo `DS` a partir de la nueva KSK y lo publicas en la zona padre (registrar) — este es el paso crítico y debe coordinarse (propagación).
  4. Esperas propagación, luego retiras la KSK antigua periódicamente.
     (El proceso exacto depende del registrador y del software DNS.)

### Limitaciones y consideraciones prácticas

- **DNSSEC NO cifra**: protege autenticidad/integridad, no privacidad. Para privacidad del transporte DNS usa DoT (DNS over TLS) o DoH (DNS over HTTPS).
- **Aumenta tamaño de respuesta**: firmas y claves aumentan el tamaño DNS → más probabilidad de fragmentación UDP; por eso muchos resolvers/clienes usan TCP fallback o EDNS0/DO.
- **La cadena debe completarse**: si no hay `DS` en la zona padre la validación falla (resolver reclama que la zona no está firmada). Publicar el DS en el registrar es paso obligatorio para que la validación funcione globalmente.
- **Depende del resolver**: si el resolver recursivo no es validador (no hace DNSSEC validation), el usuario final no percibirá beneficio. Los resolvers modernos como Unbound, Cloudflare (1.1.1.1), Google Public DNS (8.8.8.8) sí hacen validación.
- **Configuración y mantenimiento**: gestionar KSK/ZSK, rollovers y sincronización con registrador requiere procesos y monitoreo.

### Buenas prácticas

- Usa **KSK + ZSK** (no solo una clave).
- Prefiere **algoritmos y digests modernos** (RSA 2048/4096 o ECDSA, DS con SHA-256).
- Automatiza la **rotación** de ZSK y establece un plan para KSK con coordinación del registrador.
- Habilita **NSEC3** si no quieres exponer la lista completa de nombres.
- Comprueba regularmente con `dig +dnssec`, `named-checkzone` y herramientas como **DNSViz** o **verificadores online** (si quieres una revisión externa).
- Asegúrate de que tus servidores autoritativos soporten EDNS0 y TCP fallback para evitar problemas de truncamiento/fragmentación.

### Ejemplo de uso real (DANE)

DNSSEC se usa también para **DANE** (vínculo entre certificado TLS y DNS). Un ejemplo es publicar un registro `TLSA` bajo `_443._tcp.example.com` firmado por DNSSEC que indica el certificado esperado para `example.com`. Si el cliente valida DNSSEC y DANE, puede chequear el certificado TLS contra el `TLSA` y rechazar conexiones si no coinciden. Esto ilustra cómo DNSSEC permite nuevas técnicas de autenticación.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**           | **Siguiente 5**       |
| ------------------ | --------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./12_3_IPSec.md) | [⏩](./12_5_LDAPS.md) |
