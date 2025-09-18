| **Inicio**         | **atrás 45**                 | **Siguiente 47**       |
| ------------------ | ---------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./13_45_Joe_Sandbox.md) | [⏩](./13_47_Whois.md) |

---

## **Índice**

| Temario                                                                                                                                |
| -------------------------------------------------------------------------------------------------------------------------------------- |
| [500. UrlVoid](#500-urlvoid)                                                                                                           |
| [501. Cómo comprobar un enlace web sospechoso sin hacer clic en él](#501-cómo-comprobar-un-enlace-web-sospechoso-sin-hacer-clic-en-él) |

# **UrlVoid**

## **500. UrlVoid**

### 1) ¿Qué es URLVoid? (definición)

**URLVoid** es un servicio web creado por NoVirusThanks que permite comprobar la reputación y el estado de seguridad de un dominio o URL consultándolo ante múltiples listas negras (blacklists) y servicios de reputación. Se usa para identificar sitios potencialmente maliciosos (phishing, malware, scam) antes de interactuar con ellos.

### 2) Qué hace concretamente (resumen funcional)

- Consulta **múltiples engines de reputación y blocklists** para ver si un dominio/URL aparece en listas de advertencia.
- Ofrece utilidades relacionadas (WHOIS, lookup de IP/ASN, conversión web→PDF, etc.).
- **Nota importante:** la API original de URLVoid fue movida y consolidada en **APIVoid** (la recomendación actual para integrar programáticamente reputación de dominios/URLs es usar APIVoid).

### 3) ¿Cómo usar URLVoid (web UI) — guía rápida

1. Entra a `https://www.urlvoid.com/`.
2. Pega el dominio o la URL en el campo de búsqueda (ej.: `example.com`) y pulsa _Scan Website_.
3. En el informe verás: score/resumen, motor por motor (qué engines marcaron el sitio), quiénis, IP(s) y detalles de reputación.
4. Si aparece en una o varias listas, URLVoid mostrará las fuentes que lo detectaron (por ejemplo: listas antispam, motores AV, servicios de reputación).

### 4) Interpretación de resultados — qué mirar primero

- **Resumen / Score**: indica si hay detecciones agregadas.
- **Listas negras detectadas**: qué servicios/reportes han marcado el dominio. Una detección en varias fuentes es más relevante que una sola (pero ojo: falsos positivos existen).
- **WHOIS / Fecha de creación**: dominios muy nuevos pueden ser sospechosos en campañas de phishing.
- **IP / ASN**: verifica si el host pertenece a un ASN con mala reputación o a proveedores de hosting usados por abusadores.
- **Notas y enlaces**: URLVoid suele enlazar a la fuente que marca el dominio — úsalas para profundizar.

### 5) Uso por línea de comandos / scripts (API moderna: APIVoid)

La **API pública original de URLVoid** fue trasladada a **APIVoid** (servicio que centraliza APIs de reputación y que ofrece el endpoint de _domain/url reputation_). Si quieres automatizar (SIEM, SOAR, escáner de correo), usa APIVoid y su endpoint de _Domain Reputation / URL Reputation_.

Ejemplo (conceptual) — **consulta simple con APIVoid (cURL)**:

```bash
# Este ejemplo usa el endpoint de APIVoid (sustituye TU_API_KEY y la URL)
curl "https://endpoint.apivoid.com/urlrep/v1/pay-as-you-go/?key=TU_API_KEY&url=https%3A%2F%2Fexample.com"
```

(La respuesta JSON incluye score, lista de controles, detecciones en blocklists, WHOIS y otros factores). Revisa la documentación de APIVoid para los parámetros exactos, cuotas y planes.

> Nota práctica: algunos integradores (Chronicle, FortiSOAR, etc.) documentan cómo obtener la API key de APIVoid y configurarla en sus conectores; cuando integres con SIEM/SOAR sigue la guía del conector.

### 6) Ejemplo de flujo práctico (triage de un enlace sospechoso)

Situación: llega un correo con `hxxp://malicious-example[.]com/login`.

1. **Sin abrirlo**, copia la URL y búscala en URLVoid (o llama a APIVoid desde tu playbook).
2. **Chequea:**

   - ¿El dominio aparece en varias blacklists? (alto riesgo).
   - ¿Quiénis muestra registro muy reciente o privacidad en registrar? (sospecha).
   - ¿IP/ASN están asociados con hosting barato o abiertamente malicioso?

3. **Acción según evidencia**:

   - Si varias detecciones + C2/blacklists → bloquear en proxy/firewall y marcar en ticket IR.
   - Si detección única y sospecha débil → enriquecer con VirusTotal, urlscan, y un análisis manual.

4. **Registrar IOCs**: dominio, IP, whois y el resultado agregado.

### 7) Integración en SOC / SOAR — ideas prácticas

- **En pipelines de triage**: cuando EDR o gateway de correo observe una URL, llamar a APIVoid para enriquecer y decidir bloqueos automáticos según reglas (ej.: si `blacklist_count >= 2` o `risk_score > threshold`).
- **Conectores disponibles**: hay módulos / integraciones documentadas para plataformas (ej.: Chronicle, FortiSOAR) que usan APIVoid/URLVoid; úsalas para ahorrar trabajo.

### 8) Limitaciones importantes — lee esto antes de confiar ciegamente

- **No es infalible**: URLVoid agrega señales de terceros; un resultado `clean` (0/xx) **no garantiza** que el sitio sea seguro. Del mismo modo, una detección puede ser un **falso positivo**. Siempre correlaciona con otras fuentes y logs.
- **Privacidad/API**: URLVoid público es gratis pero **no** pretende usarse como API comercial; su FAQ deja claro que no está pensado para integraciones comerciales automatizadas (usa APIVoid para integración programática). Además, el servicio puede bloquear IPs que generen muchas peticiones automatizadas.
- **Latencia / frescura**: la reputación puede cambiar rápido; comprueba la fecha del hallazgo y vuelve a verificar si es crítico.
- **Cobertura limitada**: URLVoid consulta una cantidad finita de motores (aunque decentes); complementa con VirusTotal, urlscan, feeds de TI y análisis propios.

### 9) Buenas prácticas al usar URLVoid / APIVoid

- **Calcula y registra el dominio/URL** antes de cualquier acción (hashes, evidencia).
- **No subas contenido sensible** a servicios públicos; si necesitas privacidad usa integraciones privadas/On-Prem o servicios empresariales.
- **Automatiza con APIVoid**, no con la interfaz pública de URLVoid; respeta cuotas y límites.
- **Correlaciona** resultados con otras fuentes (VirusTotal, urlscan, logs proxy/EDR).
- **Registra la decisión** tomada por tu playbook (bloqueo, investigar, monitorear) y la evidencia (pantallazos/JSON de la consulta).

### 10) Recursos útiles (enlaces para seguir leyendo)

- Página principal URLVoid (uso web UI).
- Nota: API de URLVoid movida a APIVoid → documentación de APIVoid (endpoints de reputación).
- FAQ de URLVoid (límites y política de uso).

### Resumen corto (para llevar)

- **URLVoid** = comprobador de reputación de dominios/URLs (consulta múltiples blacklists y engines) creado por NoVirusThanks.
- **Para automatizar** y uso en SOC, emplea **APIVoid** (API moderna que incorpora la funcionalidad de reputación).
- **No confíes ciegamente** en un único reporte: correlaciona, añade análisis dinámico (urlscan) y toma acciones basadas en umbrales claros.

---

[🔼](#índice)

---

## **501. Cómo comprobar un enlace web sospechoso sin hacer clic en él**

> **Regla general:** evita consultar directamente la URL desde tu máquina de trabajo. Usa servicios de análisis en sandbox (VirusTotal, urlscan, APIVoid), una VM aislada o herramientas offline. Cualquier petición a un dominio malicioso puede disparar callbacks, revelar tu IP o activar cargas condicionales del atacante.

### Resumen rápido (qué hacer primero)

1. **No cliques.**
2. **Inspecciona visualmente** y extrae el dominio del enlace.
3. **Consulta fuentes públicas y servicios sandbox** (VirusTotal, urlscan, APIVoid, Google Safe Browsing, PhishTank).
4. **Haz búsquedas WHOIS / DNS / Passive DNS** para ver antigüedad y hosting.
5. **Revisa reputación y sandboxes** — preferiblemente mediante API/web UI.
6. **Decide acción**: bloquear / reportar / investigar más.

### 1) Inspección visual (rápida — ya desde el correo o mensaje)

- **Hover** (pasar el cursor) en el enlace: la URL real suele mostrarse en la esquina inferior del navegador/cliente de correo.
- **Texto vs destino:** el texto del enlace puede decir `banco.com` pero apuntar a `http://tiny[.]url/xyz`.
- **Busca trucos**: subdominios largos (`banco-secure.example.com`), uso de caracteres confusos (I/O en vez de l/), dominios parecidos (`paypa1.com`), puertos raros (`:8080`) o path sospechoso.
- **Si es un acortador** (bit.ly, t.co, tinyurl): no confíes en el acortador; trata de **desacortar** con un servicio seguro.

### 2) Extraer el dominio de forma segura (sin abrir la URL)

Si tienes la URL en texto, extrae el dominio localmente (sin hacer requests):

Ejemplo en bash — obtiene dominio sin conectar:

```bash
url='http://bit.ly/abcdef'
# extrae host sin hacer petición
host=$(echo "$url" | sed -E 's#^[a-z]+://##' | cut -d/ -f1)
echo "$host"
# salida: bit.ly
```

Esto **no** realiza ninguna conexión; solo manipula texto.

### 3) Comprobar reputación **sin** abrir la URL (recomendado: online sandbox / reputación)

#### A — urlscan.io (escaneo en sandbox del URL)

- urlscan **“visita”** la URL en un entorno controlado y te muestra screenshot, requests, redirecciones y DOM. Muy útil para phishing.
- **No** hace falta visitar la URL tú mismo; subes la URL a urlscan y ellos la analizan de forma segura.

Ejemplo cURL (usa tu API key):

```bash
curl -sS -X POST "https://urlscan.io/api/v1/scan/" \
  -H "API-Key: TU_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"http://ejemplo-sospechoso.test","visibility":"unlisted"}'
```

Respuesta: `uuid` y `result` (URL del informe). Luego recuperas el JSON con el `result` o con:

```bash
curl -sS "https://urlscan.io/api/v1/result/UUID/" -H "API-Key: TU_API_KEY"
```

#### B — VirusTotal (lookup sin navegar)

- VirusTotal analiza URLs y reporta detecciones por distintos motores, además de indicar si ya hay un scan previo.
- **Endpoint** (ejemplo conceptual v3): subes la URL o haces lookup por short link hash.
  Ejemplo cURL para consultar (requiere API key):

```bash
# enviar URL para analizar (VirusTotal v3)
curl -s -X POST "https://www.virustotal.com/api/v3/urls" \
  -H "x-apikey: TU_API_KEY" \
  -d "url=http://ejemplo-sospechoso.test"
# luego consulta /urls/{id}
```

VirusTotal ejecuta su propio análisis en servidores controlados — no expones tu máquina.

#### C — APIVoid / URLVoid (reputación de dominio)

- APIVoid ofrece endpoints para reputación de dominios y URLs (agrega blacklists).
  Ejemplo cURL:

```bash
curl "https://endpoint.apivoid.com/urlrep/v1/pay-as-you-go/?key=TU_API_KEY&url=https%3A%2F%2Fejemplo-sospechoso.test"
```

#### D — Google Safe Browsing (API)

- Verifica en la base de datos de Google Safe Browsing. Requiere API key y cuota.

#### E — PhishTank, OpenPhish, Sucuri, ThreatCrowd, MalwareBazaar

- Busca la URL o dominio en bases comunitarias (PhishTank para phishing, Sucuri para reputación de sitios web, etc.).

### 4) Comprobaciones DNS / WHOIS / Passive DNS (sin abrir la web)

Estas consultas **resuelven** información pública sobre el dominio; algunas realizan conexiones DNS, pero no abren la web.

**WHOIS** — fecha de registro, registrar, privacy:

```bash
whois ejemplo-sospechoso.test
```

**DNS A / NS / MX** (usa `dig`):

```bash
dig +short ejemplo-sospechoso.test A
dig +short ejemplo-sospechoso.test NS
dig +short ejemplo-sospechoso.test MX
```

Observa: IPs en hosting “barato” o en ASNs con mala reputación pueden ser indicador.

**Passive DNS** — ver historial de resoluciones (requiere cuenta en servicios como VirusTotal, RiskIQ, PassiveTotal). Muy útil para pivotear.

**Nota:** `dig` y `whois` hacen consultas de red; si no quieres que tu IP sea vista por el dominio (por registro DNS recursivo), usa servicios externos (VirusTotal, urlscan) o una VM aislada.

### 5) Desacortar enlaces (shorteners) con seguridad

- **No hagas la petición en tu navegador**. Usa servicios de _preview/unshorten_ o usa urlscan/VT which expand redirects in sandbox.
- Ejemplo: urlscan o VirusTotal resolverán la cadena de redirecciones y te mostrarán la URL final sin que la visites tú.

Comando (peligroso — **evítalo si no estás en VM**):

```bash
# Esto seguirá redirecciones y contactará al servidor; **no recomendado** en máquina no aislada
curl -I -L -s "http://bit.ly/xyz" -o /dev/null -w "%{url_effective}\n"
```

> **Advertencia:** lo anterior hace requests a la URL; un atacante podría usar redirecciones para capturar tu IP. Mejor: usa urlscan / VirusTotal para desacortar de forma segura.

### 6) Comprobar certificado TLS / HTTPS (sin cargar página HTML)

Obtener el certificado TLS usando `openssl s_client` **conecta al servidor** en su puerto 443. Esto hace tráfico de red — si quieres evitar toda conexión desde tu red corporativa, usa servicios online (SSL Labs) o hazlo desde una VM aislada.

Ejemplo (si lo haces desde entorno seguro):

```bash
echo | openssl s_client -servername ejemplo-sospechoso.test -connect ejemplo-sospechoso.test:443 2>/dev/null | openssl x509 -noout -dates -subject -issuer
```

### 7) Comprobaciones de contenido sin renderizar (solo meta)

Si tienes el HTML (p. ej. recibido por otros medios) puedes inspeccionarlo offline (strings, formularios, scripts) sin ejecutar JS. Pero **no** descargues la página desde tu máquina de trabajo; pide al SOC que lo haga en sandbox o usa urlscan.

### 8) Señales que indican _alta probabilidad_ de malicia

- Dominio **muy nuevo** + privacidad en WHOIS.
- Aparece en varias blacklists / detecciones en VirusTotal / APIVoid.
- Redirecciones múltiples a dominios distintos.
- Formularios que envían datos a dominios no relacionados.
- Scripts ofuscados cargados desde dominios distintos (detectable en urlscan).
- IP alojada en ASN conocido por abuse/spam.

### 9) Qué hacer tras el análisis (acciones prácticas)

- **Si es malicioso**: bloquear dominio/IP en proxy/firewall, añadir a listas internas, crear ticket/incidente. Extraer IOCs y subir artefactos (hashes, dominio) a MISP/Feeds y VirusTotal.
- **Si es dudoso**: marcar como sospechoso y escalar a analista para correr sandbox en VM segura (ANY.RUN, Joe Sandbox, Cuckoo).
- **Reportar**: denunciar a PhishTank o al proveedor de alojamiento si aplica.

### 10) Herramientas y comandos útiles (resumen con ejemplos seguros)

> **Recomendación**: prioriza _servicios sandbox_ y consultas de reputación por API sobre hacer requests directos desde tu máquina.

- **Extraer host localmente (sin conectar):**

```bash
echo 'https://tiny.one/abc?x=1' | sed -E 's#^[a-z]+://##' | cut -d/ -f1
```

- **urlscan (API):** ver más arriba (POST /scan → GET /result).
- **VirusTotal (API):** subir/consultar URL sin abrir el sitio.
- **APIVoid:** reputación de dominio/url.
- **whois / dig:** ver datos de registro y DNS.
- **PhishTank / OpenPhish:** buscar si ya está reportado.
- **Google Safe Browsing API:** comprobar en la base de Google.

### 11) Qué **NO** hacer

- **No** pegues el enlace en la barra de direcciones de tu navegador normal.
- **No** uses `curl -L` ni `wget` desde tu máquina corporativa si sospechas que es malicioso (podrías exponer tu IP o disparar payloads).
- **No** compartas el enlace públicamente sin sanitizarlo (usa `example[.]com`).
- **No** confíes en una sola fuente: siempre correlaciona.

### Ejemplo ilustrativo completo (flujo de un analista)

1. Recibes en correo: `http://tiny[.]cc/AbC123` — **no clics**.
2. Extraes host localmente: `tiny.cc` (acortador).
3. Usas urlscan (API): subes `http://tiny.cc/AbC123` con `visibility=unlisted`. Obtienes `result` → urlscan muestra que redirige a `http://malicious-example[.]com/login` y captura screenshot con formulario de “banco falso”.
4. Paralelamente consultas VirusTotal y APIVoid: ambos reportan detecciones y dominio nuevo (WHOIS hace 2 días).
5. Resultado: bloquear dominio y la short URL en proxy, añadir a lista de IOCs, abrir incidente y avisar usuario receptor del correo.

### Recursos rápidos (para usar ahora)

- urlscan: `https://urlscan.io/` — ideal para ver lo que se carga y obtener screenshots.
- VirusTotal: `https://www.virustotal.com/` — reputación multi-AV y sandbox.
- APIVoid / URLVoid: reputación de dominio/URL y blacklists.
- Google Safe Browsing API — para integraciones automáticas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 45**                 | **Siguiente 47**       |
| ------------------ | ---------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./13_45_Joe_Sandbox.md) | [⏩](./13_47_Whois.md) |
