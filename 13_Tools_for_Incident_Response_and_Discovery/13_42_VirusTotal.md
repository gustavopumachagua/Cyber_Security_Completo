| **Inicio**         | **atrás 41**                       | **Siguiente 43**            |
| ------------------ | ---------------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_41_Endpoint_Security.md) | [⏩](./13_43_urlscan_io.md) |

---

## **Índice**

| Temario                                                        |
| -------------------------------------------------------------- |
| [492. Cómo funciona VirusTotal](#492-cómo-funciona-virustotal) |
| [493. Definición de VirusTotal](#493-definición-de-virustotal) |

# **VirusTotal**

## **492. Cómo funciona VirusTotal**

![VirusTotal](/img/13_Tools_for_Incident_Response_and_Discovery/VirusTotal.png "VirusTotal")

### ¿Qué es VirusTotal?

VirusTotal es un servicio en línea que permite analizar archivos y URLs con múltiples motores de detección (antivirus, sandbox, heurísticas, firmas, YARA, etc.) y agrega metadata, reputación y observaciones de la comunidad para ayudar a decidir si algo es malicioso. Se usa mucho en SOCs, análisis forense, triage de alertas y para enriquecer detecciones automáticas.

### Flujo general: cómo funciona (alto nivel)

1. **Recepción**: un usuario/herramienta sube un archivo o una URL, o simplemente consulta un hash (SHA256/MD5). También recibe muestras desde integraciones (antivirus, honeypots, partners) y su comunidad.
2. **Indexado por hash**: VirusTotal calcula hashes (MD5, SHA1, SHA256). Si el hash ya existe, devuelve el informe ya generado (evita repetir análisis).
3. **Escaneo multi-motor**: el archivo/URL se escanea con decenas de motores antivirus y herramientas de análisis estático (extracción de strings, imports, firmas).
4. **Análisis dinámico (sandbox)**: si procede, ejecuta la muestra en entornos aislados (sandbox) para observar comportamiento: procesos creados, conexiones de red, archivos escritos, claves de registro, etc.
5. **Correlación e inteligencia**: extrae IOCs (dominios, IPs, archivos, certificados), aplica YARA, clasifica la muestra (maligno/sospechoso/limpio) y agrega metadatos y comentarios de la comunidad.
6. **Entrega de informe**: el usuario obtiene un informe con: ratio de detecciones (ej. 5/72), detalles de comportamiento, firmas YARA coincidientes, relaciones (gráfica) y estadísticas por proveedor.

### Tipos de entradas que acepta

- **Archivo** (ej. .exe, .docx, .js)
- **Hash** (SHA256/MD5/SHA1) — para buscar si ya hay análisis
- **URL** — para analizar redirecciones y reputación
- **Dominios / IPs** — para reputación y conexiones observadas
- **Samples desde integraciones** (APIs, correo, endpoints)

### Cómo interpretar un informe (conceptos clave)

- **Detection ratio**: p.ej. `3/70` = 3 motores detectaron algo malicioso entre 70. No es absoluto: puede haber falsos positivos o detecciones nuevas.
- **Last analysis date**: cuándo se analizó por última vez (útil para saber si hay detección nueva).
- **Last_analysis_stats** (típico en JSON): número de `malicious`, `suspicious`, `undetected`, `harmless`, `timeout`.
- **YARA matches**: reglas YARA que coincidieron con el archivo.
- **Sandbox behavior**: procesos creados, ficheros escritos, conexiones de red (dominios/ips contactados), llamadas API del sistema.
- **Relations / Graph**: muestra cómo el archivo se relaciona con otros archivos, dominios e IPs (muy útil para pivotar).
- **Community comments / votes**: observaciones humanas.

### Ejemplo práctico 1 — Triage rápido de un archivo sospechoso

Situación: recibes `sospechoso.exe` por correo.

1. Calcula hash (en Linux/WSL):

```bash
sha256sum sospechoso.exe
# salida: 3b7a...e8c4  sospechoso.exe
```

2. Búscalo en VirusTotal (web o API). Si no existe, súbelo.
3. Interpretación posible:

   - Informe muestra `detections: 2/72` → dos motores lo marcan como malicioso.
   - Sandbox muestra conexiones salientes a `malicious-domain[.]com` y creación de un archivo en `%TEMP%\xyz.dll`.
   - YARA coincidió con una regla de un kit conocido.

Decisión de triage:

- Si las dos detecciones son de motores reputados y el sandbox muestra comportamiento de C2, tratarlo como malicioso (aislar host, bloquear dominio, remover archivo).
- Si hay solo 1 detection y sandbox no muestra nada sospechoso, investigar más (hash en listas públicas, versiones del fichero, origen).

### Ejemplo práctico 2 — Uso de la API (ejemplos)

> Nota: necesitas una **API key** (gratuita/limitada o de pago). A modo de ejemplo:

**Subir un archivo (cURL)**:

```bash
curl -X POST "https://www.virustotal.com/api/v3/files" \
  -H "x-apikey: TU_API_KEY" \
  -F "file=@sospechoso.exe"
```

**Consultar un informe por SHA256 (cURL)**:

```bash
curl -X GET "https://www.virustotal.com/api/v3/files/3b7a...e8c4" \
  -H "x-apikey: TU_API_KEY"
```

**Ejemplo en Python (requests)**:

```python
import requests

API_KEY = "TU_API_KEY"
sha256 = "3b7a...e8c4"
url = f"https://www.virustotal.com/api/v3/files/{sha256}"

resp = requests.get(url, headers={"x-apikey": API_KEY})
data = resp.json()
print(data["data"]["attributes"]["last_analysis_stats"])
```

(Este es un esquema típico; consulta la documentación oficial para detalles de endpoints y paginación.)

### Ejemplo de regla YARA

Detectar una cadena específica o patrón:

```yara
rule EjemploMalicioso {
  meta:
    author = "Gustavo"
    description = "Detecta cadena de ejemplo maliciosa"
  strings:
    $str1 = "malicious_string_example"
    $hex1 = { 6A 00 68 2E 65 78 65 }
  condition:
    any of them
}
```

Si VirusTotal aplica esa regla y hay coincidencia, aparecerá en el apartado de YARA matches.

### Integraciones y automatización (cómo se usa en empresas)

- **SIEM / SOAR**: cuando surge una alerta, la plataforma consulta VirusTotal (por hash o URL) para enriquecer y priorizar la alerta.
- **EPP/EDR**: al detectar un comportamiento, el endpoint envía muestras a VirusTotal para corroborar.
- **Herramientas de triage**: analistas usan la gráfica y sandbox de VT para pivotar rápidamente a dominios e IPs relacionadas.

### Limitaciones y riesgos

- **Falsos positivos / falsos negativos**: un detection ratio bajo no garantiza que sea benigno; un ratio alto puede contener falsos positivos.
- **Sandbox evasion**: malware sofisticado detecta sandboxes y no ejecuta payloads maliciosos en entornos virtuales.
- **Privacidad**: al subir un archivo a VirusTotal, se vuelve accesible públicamente (en la versión pública). No subas archivos con datos personales, secretos comerciales o propiedad intelectual sin usar opciones privadas o funciones empresariales.
- **Latencia**: puede haber retraso entre la aparición de una campaña y la detección por varios motores.
- **No es reemplazo**: VirusTotal es una herramienta de enriquecimiento/triage, no sustituye controles de seguridad en endpoints ni una investigación forense completa.

### Buenas prácticas

- **Calcular hash primero** y buscar antes de subir (evitas exponer datos innecesarios).
- **No subir información sensible** a la versión pública.
- **Usar la API** para automatizar y respetar límites de rate (plan gratuito limitado).
- **Correlacionar** resultados con otras fuentes (logs internos, feeds de IOCs).
- **Actualizar YARA** y reglas internas basadas en hallazgos confiables.

### Resumen corto (para llevar)

- VirusTotal agrega múltiples motores y sandboxes para analizar archivos/URLs.
- Devuelve un ratio de detecciones, sandbox behavior, YARA matches y relaciones entre IOCs.
- Es ideal para triage rápido y enriquecimiento automático, pero tiene limitaciones (privacidad, evasión, falsos positivos).
- Usa hashes (SHA256) para búsquedas rápidas; usa la API para automatizar.

---

[🔼](#índice)

---

## **493. Definición de VirusTotal**

**VirusTotal** es un servicio web que centraliza el análisis y la reputación de archivos, URLs, dominios e IPs. Cuando subes (o consultas) una muestra, VirusTotal la analiza con decenas de motores antivirus, sandboxes (análisis dinámico), reglas YARA y otras herramientas de análisis estático/dinámico, y entrega un informe consolidado con detecciones, comportamiento observado, indicadores (IOCs) y relaciones con otras muestras. Es una herramienta de _triage_ y enriquecimiento para analistas de seguridad, equipos SOC/IR y desarrolladores.

### Qué hace (resumen funcional)

- **Agrega múltiples motores** antivirus y firmas para dar un “voto” combinado sobre si algo parece malicioso.
- **Ejecuta sandboxes** para observar comportamiento (conexiones de red, procesos creados, archivos modificados).
- **Aplica reglas YARA** y otras heurísticas para reconocer familias o patrones.
- **Extrae y correlaciona IOCs** (dominios, IPs, certificados, hashes) y muestra relaciones (gráfica).
- **Expone APIs** para automatizar consultas y subir muestras.
- **Proporciona comunidad y metadatos** (comentarios, reputación temporal, quién subió la muestra).

### Entradas que acepta

- **Archivo** (ej. `.exe`, `.docx`, `.pdf`, `.js`)
- **Hash** (MD5 / SHA1 / SHA256) — buscar si ya existe el análisis
- **URL** — para reputación y análisis de redirecciones
- **Dominio / IP** — para reputación y relaciones

### Salidas clave en un informe

- **Detection ratio** (ej. `5/72` → 5 motores marcaron malicioso entre 72)
- **last_analysis_stats** (ej. cuántos `malicious`, `suspicious`, `undetected`)
- **Sandbox behavior** (procesos, archivos creados, llamadas de red)
- **YARA matches** (reglas que coinciden)
- **Gráfica de relaciones** (cómo se conecta con otros IOCs)
- **Metadatos** (firmas digitales, strings extraídos, tamaño, tipo MIME)

Ejemplo simulado de `last_analysis_stats`:

```json
"last_analysis_stats": {
  "malicious": 5,
  "suspicious": 2,
  "undetected": 65,
  "harmless": 0,
  "timeout": 0
}
```

### Ejemplos prácticos (rápido)

1. **Buscar por hash localmente**

   Calculas el SHA256 y lo buscas en VirusTotal:

   ```bash
   sha256sum sospechoso.exe
   # salida: 3b7a...e8c4  sospechoso.exe
   ```

2. **Interpretación sencilla**

   - `0/72` → probablemente benigno, pero sigue investigando si viene de fuente dudosa.
   - `1–3/72` → detección ligera: investigar (puede ser falso positivo o detección nueva).
   - `>10/72` → alta probabilidad de malicioso, especialmente si el sandbox muestra comportamiento C2 o persistencia.

3. **Consultar via API (ejemplo cURL)**

   > Nota: necesitas tu `API_KEY`.

   ```bash
   curl -X GET "https://www.virustotal.com/api/v3/files/3b7a...e8c4" \
     -H "x-apikey: TU_API_KEY"
   ```

   La respuesta JSON contendrá `last_analysis_stats`, `behavioral` (si hay sandbox), `relations`, etc.

### Casos de uso

- **Triage de alertas**: enriquecer una alerta de EDR con reputación y sandbox.
- **Investigación forense**: pivotar desde un hash a dominios e IPs relacionadas.
- **Automatización**: scripts que bloquean IPs o cuarentenan archivos si VT muestra detecciones altas.
- **Investigación de malware**: ver firmas y YARA matches para identificar familias.

### Limitaciones importantes

- **Privacidad**: archivos subidos a la versión pública pueden quedar accesibles públicamente. No subir datos sensibles.
- **Falsos positivos/negativos**: el conteo no es una verdad absoluta. Correlaciona con otras fuentes y logs.
- **Evasión de sandbox**: malware avanzado puede detectar entornos virtuales y no ejecutar su payload allí.
- **Latencia de firmas**: un motor puede tardar en actualizar su firma; detecciones pueden cambiar con el tiempo.

### Buenas prácticas

- **Buscar por hash antes de subir** (evita exponer datos innecesarios).
- **Usar la API** para integración con SIEM/SOAR y respetar límites de uso.
- **Correlacionar** con logs internos, DNS, proxy y EDR antes de tomar acciones.
- **No relies solo en VT** para decisiones de bloqueo definitivo; úsalo como parte del triage.

### Resumen rápido

VirusTotal = agregador + análisis (estático + dinámico) + correlación de IOCs; útil para triage, enriquecimiento y análisis inicial de malware, pero no sustituye una investigación forense completa ni controles internos.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 41**                       | **Siguiente 43**            |
| ------------------ | ---------------------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./13_41_Endpoint_Security.md) | [⏩](./13_43_urlscan_io.md) |
