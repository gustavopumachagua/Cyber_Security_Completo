| **Inicio**         | **atrás 42**                | **Siguiente 44**         |
| ------------------ | --------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_42_VirusTotal.md) | [⏩](./13_44_ANY_RUN.md) |

---

## **Índice**

| Temario                                                                                                                                                                          |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [494. urlscan io](#494-urlscan-io)                                                                                                                                               |
| [495. Herramienta de ciberseguridad para analistas de seguridad principiantes - URLScan](#495-herramienta-de-ciberseguridad-para-analistas-de-seguridad-principiantes---urlscan) |

# **urlscan io**

## **494. urlscan io**

![urlscan io](/img/13_Tools_for_Incident_Response_and_Discovery/urlscan_io.png "urlscan io")

**urlscan.io** es un servicio (y sandbox) para escanear y analizar URLs y páginas web: cuando envías una URL, urlscan la “visita” de forma automatizada como si fuera un navegador real y registra todo lo que ocurre — recursos cargados, dominios e IP contactadas, redirecciones, capturas de pantalla, tecnologías detectadas y mucha metadata útil para triage e inteligencia.

### Resumen del flujo — ¿qué hace exactamente?

1. **Recepción / envío**: envías una URL por la web o por la API. Puedes elegir la visibilidad del escaneo: **Public**, **Unlisted** o **Private** (mismo resultado técnico, cambia solo si queda indexado públicamente).
2. **Navegación automatizada**: un agente de urlscan carga la página (ejecuta JavaScript) como lo haría un navegador real.
3. **Registro de actividad**: captura y extrae:

   - lista de recursos solicitados (JS, CSS, imágenes).
   - dominios e IPs contactadas.
   - redirecciones HTTP (cadena completa).
   - cookies, headers, formularios y formularios auto-post.
   - captura(s) de pantalla (snapshot) y DOM (HTML final).

4. **Post-procesado**: genera metadatos (fingerprints, tecnologías detectadas, whois, ASN, certificados TLS), calcula relaciones con otros scans y construye el informe JSON accesible por la API.

### Por qué es útil (casos de uso)

- **Triage de phishing**: ver la página tal como la vería la víctima, detectando formularios, imágenes base64, similitudes estructurales.
- **Hunting / threat intel**: pivotar desde un dominio a IPs, ASNs y otros scans relacionados.
- **Automatización en SOAR/SIEM**: integrarlo para enriquecer alertas y bloquear dominios/URLs sospechosas.

### Entendiendo un resultado (campo a campo — lo más importante)

Al recuperar el resultado de un scan (Result API) verás, entre otros campos clave:

- `page` / `page.domain` / `page.url` — URL original y dominio.
- `task` / `task.method` / `task.options` — cómo se realizó el scan (país, user-agent, visibilidad).
- `stats` — número de requests, recursos bloqueados, etc.
- `dom` / `screenshot` — HTML final y captura de pantalla (útil para presentación a managers).
- `requests` — array con cada petición (URL, status, response headers, referer).
- `page.resources` — lista y tipos de recursos cargados (scripts, imágenes).
- `links` / `forms` — enlaces y formularios detectados (útil para phishing).
- `tags` / `technologies` — heurísticas que identifican frameworks o bibliotecas.

  La documentación del Result API describe la estructura completa y cómo explorarla.

### Opciones útiles al escanear

- **Visibilidad**: Public / Unlisted / Private (elige según privacidad).
- **País del escaneo**: puedes seleccionar desde qué país se realiza la navegación (importante: páginas con contenido geolocalizado).
- **User-agent / dispositivo**: algunas integraciones te dejan simular distintos agentes (móvil/desktop).
- **Reintentos / timeouts**: la API permite controlar límites (ver docs de API).

### Ejemplo práctico 1 — Uso rápido por web

1. Entra a `https://urlscan.io/` y pega la URL.
2. Selecciona **Public/Unlisted/Private** y el país.
3. Envía el scan; tras unos segundos/minutos (según la URL) verás: captura de pantalla, lista de requests, redirecciones, y el JSON del resultado.

### Ejemplo práctico 2 — API (cURL) — enviar y recuperar un scan

> Necesitas una cuenta y una API Key (las peticiones no autenticadas tienen pocas cuotas).

**Enviar URL para escanear (cURL)**:

```bash
curl -sS -X POST "https://urlscan.io/api/v1/scan/" \
  -H "API-Key: TU_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"http://example.com","visibility":"unlisted","public":false}'
```

La respuesta contiene un `uuid` y una `result` (URL al informe). Guarda el `uuid` o el enlace.

**Recuperar resultado (cURL)**:

```bash
curl -sS "https://urlscan.io/api/v1/result/UUID_HERE/" -H "API-Key: TU_API_KEY"
```

O usa la `result` URL que te devolvió para abrir el JSON en el navegador.

### Ejemplo práctico 3 — Python (requests) — enviar y parsear requests

```python
import requests, time

API_KEY = "TU_API_KEY"
headers = {"API-Key": API_KEY, "Content-Type": "application/json"}
data = {"url":"http://example.com","visibility":"unlisted"}

r = requests.post("https://urlscan.io/api/v1/scan/", headers=headers, json=data)
resp = r.json()
print("Scan creado:", resp.get("uuid"), resp.get("result"))

# Esperar y recuperar (o hacer polling)
time.sleep(5)
result_url = resp.get("result")
res = requests.get(result_url, headers=headers).json()
print("Requests encontradas:", len(res.get("requests", [])))
```

(Adaptar `time.sleep` con backoff o usar webhooks / notificaciones en flujos productivos.)

### Cómo interpretar resultados en análisis de phishing/malware

- **Captura de pantalla + DOM**: muestra la liveness del ataque (página de login falsa).
- **Requests / redirecciones**: cadenas largas de redirecciones o dominios no relacionados → sospechoso.
- **Formularios que envían a terceros**: campo rojo (phishing).
- **Recursos externos inusuales**: carga de scripts desde CDNs raros, base64 en JS → posible ofuscación.
- **Coincidencias estructurales**: urlscan puede encontrar sitios “estructuralmente similares” (útil para descubrir más páginas clonadas).

### Integraciones y automatización

urlscan dispone de integraciones y es común en SOARs y herramientas de hunting; puedes automatizar scans desde alertas y usar los resultados para bloqueos o enriquecimiento en un SIEM. Además, ofrecen APIs de búsqueda para consultar scans archivados por dominio, IP, ASN o hash.

### Novedades / características avanzadas

- En 2025 lanzaron (experimental) un motor de **ML Verdicts** para clasificar automáticamente resultados y ayudar a priorizar escaneos potencialmente maliciosos. Si buscas automatizar triage, puede ser útil evaluarlo.

### Limitaciones y precauciones

- **Privacidad / sensibilidad**: un scan público queda indexado; no subas contenido con datos personales o secretos si usas visibilidad pública. Usa `unlisted/private` según necesidad.
- **Contenido geolocalizado**: la página puede cambiar según país o user-agent; selecciona el país correcto para reproducir lo que vio la víctima.
- **Evasión por parte del atacante**: páginas pueden detectar crawling/sandbox y servir contenido distinto (fingerprinting). Haz pruebas con diferentes user-agents y países.
- **Cuotas API**: las cuentas no autenticadas o gratuitas tienen límites; para volumen considera un plan Pro o empresa.

### Buenas prácticas rápidas

1. Calcula y busca por URL/host/hashes antes de escanear (evita duplicados).
2. Usa `unlisted` o `private` para análisis con información sensible.
3. Selecciona país y user-agent que reproduzca la vista de la víctima.
4. Automatiza con polling o integra webhooks/si tu flujo lo soporta; maneja retries/backoff.

---

[🔼](#índice)

---

## **495. Herramienta de ciberseguridad para analistas de seguridad principiantes - URLScan**

### ¿Qué es urlscan.io y por qué te importa?

**urlscan.io** es un servicio/sandbox que _visita_ automáticamente una URL con un navegador controlado y devuelve una “foto” completa de lo que ocurre: captura de pantalla, DOM final, todas las peticiones (requests) realizadas, redirecciones, recursos cargados (JS/CSS/iframes), dominios/IP contactados, metadatos (WHOIS/ASN/TLS) y un JSON con todo el detalle. Es ideal para triage rápido de URLs sospechosas, hunting de phishing y enriquecimiento de alertas.

### Primeros pasos (rápido)

1. **Cuenta**: crea una cuenta en urlscan.io para obtener una API key (las peticiones sin autenticar son muy limitadas).
2. **Visibilidad del scan**: al enviar una URL puedes elegir `public`, `unlisted` o `private`. Los resultados técnicos son los mismos; lo que cambia es si el scan aparece en búsquedas públicas o solo para tu cuenta/cliente. Usa `unlisted/private` para pruebas con datos sensibles.
3. **Opciones**: puedes seleccionar país o user-agent para reproducir vistas geolocalizadas o móviles/desktop. Esto importa cuando una web sirve contenido distinto según origen.

### Cómo hacer un scan — 2 formas (web y API)

#### A) Rápido por la web (interfaz)

- Pega la URL en [https://urlscan.io](https://urlscan.io), elige visibilidad y país y pulsa _Submit_. En segundos tendrás: captura (screenshot), DOM HTML final, lista de requests y un enlace al JSON del resultado. Útil para triage manual rápido.

#### B) Usando la API (recomendado para automatizar)

**Enviar URL (cURL)**:

```bash
curl -sS -X POST "https://urlscan.io/api/v1/scan/" \
  -H "API-Key: TU_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"http://ejemplo.com","visibility":"unlisted"}'
```

Respuesta: te devuelve un `uuid` y un `result` (URL al informe). Haz polling (o usa webhook/backoff) para recuperar el JSON final.

**Recuperar resultado (cURL)**:

```bash
curl -sS "https://urlscan.io/api/v1/result/UUID_HERE/" -H "API-Key: TU_API_KEY"
```

El JSON contiene campos como `task`, `page`, `requests`, `dom`, `screenshot`, `page.resources`, `technologies`, `stats`, etc.

**Pequeño ejemplo en Python** (requests + polling mínimo):

```python
import requests, time

API_KEY = "TU_API_KEY"
headers = {"API-Key": API_KEY, "Content-Type": "application/json"}
data = {"url":"http://example.com","visibility":"unlisted"}

r = requests.post("https://urlscan.io/api/v1/scan/", headers=headers, json=data)
resp = r.json()
uuid = resp.get("uuid")
result_url = resp.get("result")

# polling simple
for _ in range(10):
    res = requests.get(result_url, headers={"API-Key": API_KEY})
    if res.status_code == 200:
        report = res.json()
        break
    time.sleep(2)
print("Requests:", len(report.get("requests", [])))
```

(Adapta el polling con backoff o usa webhooks en producción.)

### Cómo leer e interpretar el resultado (campos clave)

Estos son los campos que más te ayudarán en triage:

- `screenshot` — imagen PNG de cómo se ve la página (rápido para confirmar phishing visualmente).
- `dom` — HTML final después de ejecutar JS (útil para buscar formularios/trampas).
- `requests` — lista de todas las peticiones que hizo la página (cada item tiene: `url`, `status`, `method`, `response headers`, `request headers`, `timings`). Aquí verás exfiltración, trackers y CDNs usados.
- `page.resources` — recursos cargados (scripts, iframes, imágenes); detecta scripts de terceros o kit de ofuscación.
- `links` / `forms` — enlaces y formularios detectados (fundamental para phishing).
- `technologies` — heurísticas que identifican CMS, librerías JS, plataformas (útil para fingerprinting).
- `task` / `task.options` — muestra el país/user-agent usados; si necesitas reproducir la vista, toma nota.

### Ejemplo práctico de triage — Phishing paso a paso

Situación: recibes enlace en correo sospechoso.

1. **Hash/URL primero**: busca la URL en urlscan (o en otros feeds) antes de clicar. Si no aparece, envíala a urlscan con `visibility: unlisted`.
2. **Revisa screenshot + DOM**: si la pantalla muestra un formulario de login que imita un banco → alto riesgo.
3. **Requests**: mira a qué dominio envía el formulario (action del form) y qué terceros carga (p. ej. trackers, shorteners). Si el `action` apunta a un dominio no relacionado o a un IP hospedada en ASNs sospechosos, es señal clara.
4. **Extrae IOCs**: dominio(s), IP(s), certificados TLS, URLs de recursos, patrones en el DOM. Añádelos a tu SIEM/IOC-list.
5. **Acción**: bloquear dominio en firewall/proxy si la evidencia es fuerte; reportar al equipo de phishing; alertar al usuario afectado si entró.

Ejemplo de decisión práctica:

- Screenshot muestra formulario de banco + `requests` envía credenciales a `evil[.]host` → bloquear y escalar.
- Screenshot benign-looking + requests solo a CDN y dominio legítimo → investigar origen del correo (posible falso positivo).

### Integración con flujo de SOC / SOAR

- **Automatizar**: cuando EDR/Email Gateway genere alerta con URL, un playbook puede:

  1. llamar a urlscan API (crear scan unlisted),
  2. esperar resultado o usar webhook,
  3. parsear `requests` y `page.resources` para extraer IOCs,
  4. enriquecer alerta y decidir bloquear automáticamente si cumple reglas (ej.: formulario + dominio no relacionado).

- **Herramientas ya integradas**: plataformas como Chronicle, Exabeam o MS Sentinel ofrecen conectores/acciones con urlscan para facilitar este flujo.

### Buenas prácticas y precauciones (imprescindibles)

- **No uses `public`** si la URL contiene datos sensibles: `unlisted` o `private` para evitar indexación pública.
- **Prueba con varios países / user-agents** si sospechas contenido geolocalizado.
- **No confíes solo en una captura**: usa requests + dom + recursos para confirmar.
- **Respeta cuotas** y usa API keys para automatización (las cuentas sin autenticar tienen límites).

### Comandos y snippets listos para usar (resumen)

1. **Enviar scan (cURL)**:

```bash
curl -sS -X POST "https://urlscan.io/api/v1/scan/" \
  -H "API-Key: TU_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"url":"http://phish.example","visibility":"unlisted"}'
```

2. **Recuperar resultado (cURL)**:

```bash
curl -sS "https://urlscan.io/api/v1/result/UUID_HERE/" -H "API-Key: TU_API_KEY"
```

3. **Descargar screenshot**:

```bash
curl -H "API-Key: TU_API_KEY" https://urlscan.io/screenshots/UUID_HERE.png --output shot.png
```

4. **Python (básico) — ver número de requests**: (ver snippet arriba)

(Estos endpoints y propiedades están documentados en la API y Result API de urlscan).

### Resumen corto para principiantes

- **urlscan.io** te permite «ver» exactamente qué hace una página al cargarla (screenshot, DOM, requests) y te entrega todo en JSON para automatizar. Es una herramienta esencial para triage de phishing y enriquecimiento de alertas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 42**                | **Siguiente 44**         |
| ------------------ | --------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_42_VirusTotal.md) | [⏩](./13_44_ANY_RUN.md) |
