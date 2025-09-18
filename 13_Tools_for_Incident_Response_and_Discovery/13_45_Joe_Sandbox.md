| **Inicio**         | **atrás 44**             | **Siguiente 46**         |
| ------------------ | ------------------------ | ------------------------ |
| [🏠](../README.md) | [⏪](./13_44_ANY_RUN.md) | [⏩](./13_46_UrlVoid.md) |

---

## **Índice**

| Temario                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------ |
| [498. Joe Sandbox](#498-joe-sandbox)                                                                                     |
| [499. Sandbox de ciberseguridad para analistas de seguridad](#499-sandbox-de-ciberseguridad-para-analistas-de-seguridad) |

# **Joe Sandbox**

## **498. Joe Sandbox**

![Joe Sandbox](/img/13_Tools_for_Incident_Response_and_Discovery/Joe_Sandbox.png "Joe Sandbox")

**Joe Sandbox** es una plataforma comercial de análisis automático y profundo de malware que combina análisis estático y dinámico, múltiples sistemas operativos (Windows, macOS, Linux, Android, iOS) y capacidades de reporting ricas (PCAP, dumps de memoria, ejecución gráfica, YARA, MAEC/STIX/MISP, etc.). Está disponible como servicio Cloud, como instalación On-Prem (Desktop / Ultimate) y ofrece APIs/SDK para integración con SOAR/SIEM.

### Arquitectura y ediciones (qué elegir)

- **Joe Sandbox Cloud**: servicio online (Cloud) que permite enviar archivos/URLs y obtener informes sin desplegar infraestructura. Tiene opciones privadas/compartidas según el plan.
- **Joe Sandbox Desktop / On-Prem**: despliegue local (controlador + máquinas de análisis virtualizadas) para organizaciones que requieren privacidad total y mayor control. La arquitectura usa controladores Linux que orquestan máquinas virtuales (VMs) con Windows/Android/etc. para el análisis.
- **Joe Sandbox Ultimate**: edición con análisis “en cadena” e inteligencia adicional (más presets, más profundidad de instrumentación, plantillas). Ideal para equipos DFIR/SOCs grandes.

### Qué hace Joe Sandbox (capabilities)

- **Análisis dinámico**: ejecuta la muestra en VM instrumentadas, registra process tree, llamadas al sistema, archivos creados, cambios en registro, servicios, etc.
- **Análisis estático**: extracción de strings, secciones PE, firmas, desensamblado, y reglas YARA.
- **Enriquecimiento y reporting**: genera HTML/PDF/JSON/MAEC/STIX/MISP y permite descargar PCAP, memory dumps, archivos dropeados, YARA matches. Esto facilita añadir IOCs a sistemas de bloqueo y alimentar plataformas de threat intel.
- **Interactividad y vídeo/snapshots**: algunos informes incluyen capturas y video de la ejecución y un “execution graph” para seguir el flujo del ataque.

### Workflow típico — paso a paso (manual / automático)

#### 1) Recolección previa

- Calcula hashes localmente (SHA256/MD5) y guarda metadatos de procedencia (mail, URL, endpoint).
- Si la muestra contiene datos sensibles, opta por On-Prem / Cloud privado. Joe Sandbox Cloud también ofrece planes privados.

#### 2) Envío de la muestra

- **UI (rápido)**: subir archivo o pegar URL en la consola Cloud/On-Prem y elegir preset (Windows 10, Office, Android, etc.).
- **API / jbxapi (recomendado para automatizar)**: usa la librería oficial `jbxapi` (Python) o llamadas REST para crear tareas, monitorear estado y recuperar informes.

**Ejemplo básico con la librería jbxapi (Python)**

(este repo es el wrapper oficial / recomendado; instala con `pip install jbxapi` según sus instrucciones):

```python
from jbxapi import JoeSandbox
js = JoeSandbox(api_key="TU_API_KEY", host="https://www.joesandbox.com")

# Subir fichero y crear análisis
task = js.submit_file("/ruta/muestra.exe", options={"preset":"win10"})
task_id = task["task_id"]

# Polling básico
report = js.get_report(task_id)
print("Estado:", report["task"]["state"])
# Cuando esté ready, descarga: report contiene links a HTML, JSON, PCAP, artefactos
```

(Adapta parámetros y maneja límites/errores según la documentación).

#### 3) Monitoreo y ejecución

- Observa process tree, tráfico de red, archivos dropeados, claves de registro, y si la muestra necesita interacción (macros, diálogos).
- Joe Sandbox puede ejecutar cadenas de análisis más profundas (Ultimate) según reglas y heurísticas automáticas.

#### 4) Recuperar e interpretar informe

- Descarga HTML/JSON, PCAP, memory dumps y lista de IOCs (dominios, IPs, hashes, rutas, entradas de registro). Los informes incluyen secciones: Static, Dynamic/Behavior, Network, Dropped, Hooks, YARA matches y Execution Graph.

### Qué mirar primero en el informe (prioridad para triage)

1. **Resumen / Clasificación**: ¿qué comportamientos se han detectado? (ej. persistence, network C2, injection).
2. **Process tree**: busca recomendaciones de persistencia, parent/child sospechosos e inyecciones.
3. **Network**: dominios contactados, URLs enviadas, POSTs/GETs, headers, y descarga de payloads (descargar PCAP para IDS).
4. **Files dropeados y hashes**: subir estos hashes a VirusTotal y almacenarlos en tu repositorio de IOCs.
5. **YARA matches / strings**: pistas rápidas sobre familia de malware o kits usados.

Ejemplo interpretativo: si hay creación de ejecutable en `%AppData%` + entrada `HKCU\Software\...\Run` + POSTs a dominios con patterns DGA => alto riesgo y acción inmediata (aislar host, bloquear dominios/IPs, abrir incidente).

### Ejemplo práctico de triage (simulado, paso a paso)

1. Subes `muestra.docx` con macros y preset `win10_office`.
2. En la ejecución, observas en el process tree: `winword.exe` → `cmd.exe` → `powershell.exe` que descarga `payload.exe`.
3. Network muestra POSTs a `hxxp://c2-example[.]com/collect` y resolución a IP `203.0.113.45`.
4. Dropeo de `svchost_fake.exe` en `%AppData%`. Informe exportable contiene: PCAP, SHA256 del dropeo, JSON y report HTML.
5. Acción recomendada: bloquear dominio/IP, alertar/aislar host, subir artefactos a VT y MISP, añadir regla IDS/Suricata basada en PCAP/strings.

### Integración con SOAR / SIEM (ejemplo de playbook)

Pseudocódigo simple:

```pseudo
on_alert(url_or_file, source):
  hash = calc_hash(sample)
  if repo.has(hash): enrich_and_return()
  task = jbxapi.submit(sample, preset="win10_office", visibility="private")
  report = poll_until_ready(task)
  iocs = parse_iocs(report)  # domains, ips, hashes, filepaths, registry
  if report.indicates("persistence") or report.network.c2_count>0:
     isolate_host(host)
     block_domains(iocs.domains)
     create_incident(report.summary, attachments=[pcap, json])
  else:
     mark_as_monitor_and_log(report)
```

Joe Sandbox ofrece integraciones y conectores para XSOAR, D3, Devo y otras plataformas (revisa los conectores oficiales/terceros).

### Export formats y artefactos (para compartir)

Joe Sandbox permite descargar: **PCAP**, **memory dumps**, **files dropeados**, **YARA signatures**, **HTML/PDF/JSON reports**, **MAEC / STIX / MISP** — ideales para compartir automáticamente con tus feeds TI o MISP.

### Ejemplo de YARA simple (para buscar C2 en dumps)

```yara
rule js_c2_example {
  meta:
    author = "Gustavo"
    description = "Detecta cadena C2 ejemplo"
  strings:
    $c2 = "c2-example"
  condition:
    $c2
}
```

Aplica sobre dumps/memory extraída desde Joe Sandbox y luego correlaciona con reportes.

### Limitaciones y riesgos (muy importantes)

- **Evasión de sandboxes**: malware sofisticado detecta entornos virtuales y modifica su comportamiento. Ejecuciones múltiples con presets distintos y “user simulation” pueden ayudar, pero no garantizan revelar todo.
- **Privacidad / compartición**: en la versión pública/Cloud básica, algunos análisis pueden ser visibles o descargables — usa Cloud privado u On-Prem para muestras sensibles. Revisa tu plan.
- **Coste / licenciamiento**: capacidades avanzadas (más sistemas operativos, mayor duración de run, extracción de memoria, API calls) suelen requerir planes empresariales. Planifica presupuesto.

### Buenas prácticas en tu workflow

- **Calcular hashes localmente antes de subir** (evitas exponer innecesariamente).
- **Usar visibilidad privada / On-Prem** para datos sensibles.
- **Correlacionar** Joe Sandbox con VirusTotal, urlscan, MISP y otros feeds.
- **Registrar** cada interacción si vas a hacer análisis interactivo (qué clics/inputs hiciste) para reproducibilidad.
- **Automatizar** con `jbxapi` y controlar ratelimits/errores.

### Recursos oficiales y enlaces útiles (para leer más)

- Joe Sandbox Cloud / overview (features & Cloud).
- Ejemplos de análisis / reports públicos (muestras de informes y artefactos descargables).
- Joe Sandbox Desktop (arquitectura On-Prem).
- `jbxapi` (Python wrapper / integración).
- Integraciones SOAR (Cortex XSOAR, D3, Devo).

---

[🔼](#índice)

---

## **499. Sandbox de ciberseguridad para analistas de seguridad**

### 1. ¿Qué es un _sandbox_ en ciberseguridad?

Un **sandbox** es un entorno controlado y aislado donde se ejecuta o “visita” software (archivos, URLs) para observar su comportamiento sin que afecte sistemas productivos. Los sandboxes registran actividad estática (análisis del binario) y dinámica (ejecución): procesos, llamadas al sistema, archivos creados, claves de registro, tráfico de red, captures (screenshots/PCAP) y más. Se usan para triage, generación de IOCs y análisis forense inicial.

### 2. Tipos de sandboxes (y cuándo usar cada uno)

- **Sandboxes automatizados (headless)**: Ejecutan la muestra automáticamente sin interacción. Buenos para volumen (triage inicial). Ej.: Cuckoo (open-source), VMRay.
- **Sandboxes interactivas**: Permiten al analista interactuar con la VM en tiempo real (hacer clics, introducir credenciales). Útiles para muestras que requieren interacción. Ej.: ANY.RUN.
- **URL/Browser sandboxes**: Visitadores automáticos que registran recursos, redirecciones y DOM (ideal para phishing). Ej.: urlscan.io.
- **Cloud vs On-Prem**:

  - _Cloud_ — rápido y sin infra; conveniente para investigadores individuales o equipos con bajo volumen.
  - _On-Prem / Appliance_ — necesario para muestras sensibles (privacidad), cumplimiento o cuando la empresa no puede subir muestras a servicios externos. Ej.: Joe Sandbox On-Prem, Cuckoo desplegado localmente.

- **Comercial vs Open-Source**: comerciales suelen ofrecer mejor cobertura, soporte y formatos estándar (MAEC/STIX); open-source (Cuckoo) ofrece flexibilidad y cero coste de licencias.

### 3. Cómo funciona un sandbox (flujo general)

1. **Recolección**: calculas hash local (SHA256) y etiquetas origen.
2. **Envío/Creación**: subes archivo/URL por UI, CLI o API (o lo pones en cola en un Cuckoo local).
3. **Ejecución**: el sandbox inicia VM(s) instrumentadas con preset (Windows, Office, Android, etc.).
4. **Monitoreo**: se registran process tree, llamadas a APIs, filesystem, registry, network, screenshots.
5. **Post-procesado**: correlación, YARA, extracción de IOCs, generación de informe (HTML/JSON/PDF/MAEC/STIX).
6. **Acciones**: exportas IOCs/artefactos (PCAP, memory dumps, archivos dropeados) y tomas medidas (aislar, bloquear, enriquecer SIEM).

### 4. Qué debes revisar en un informe (prioridad para triage)

- **Resumen / Clasificación**: comportamientos detectados (persistencia, C2, inyección).
- **Process Tree**: procesos padres y hijos; técnicas sospechosas (inyección, ejecución remota).
- **Network / PCAP**: dominios contactados, URLs, HTTP POST/GET, resoluciones DNS, puertos.
- **Dropped files**: archivos creados/dropeados y sus hashes.
- **Registry changes / Services**: entradas Run, tareas programadas, servicios añadidos.
- **Artefactos en memoria**: strings interesantes, módulos inyectados.
- **YARA / Signatures**: coincidencias con reglas conocidas.
- **Screenshots / DOM**: evidencia visual (phishing forms, dialogs).

Interpretación: la suma de indicios (persistencia + C2 + inyección) aumenta la probabilidad de malicioso; no confíes en un solo indicador.

### 5. Ejemplo práctico (simulado) — triage de un EXE sospechoso

**Situación**: recibes `documento_adjunto.exe` por correo.

**Pasos:**

1. Calcula hash:

```bash
sha256sum documento_adjunto.exe
# 3b7a...e8c4 documento_adjunto.exe
```

2. Busca hash en feeds (VT, repositorios internos). Si no existe, súbelo al sandbox `visibility=private` (o al Cuckoo local).
3. Ejecuta con preset _Windows 10 + Office_ (si sospechas vector de documento).
4. Observaciones en el informe:

   - Process tree: `winword.exe` → `cmd.exe` → `powershell.exe` → descarga `payload.exe` → crea `%AppData%\svchost_fake.exe`.
   - Registry: `HKCU\Software\Microsoft\Windows\CurrentVersion\Run\update` apunta a `svchost_fake.exe`.
   - Network: POST a `hxxp://c2-01.bad[.]com/collect` con datos base64.
   - Dropped file: `config.dat` (sha256: `abc...123`).

5. Decision: evidencia dinámica de persistencia + comunicación C2 → marcar como **malicioso**.
6. Acciones: aislar host, bloquear dominio/IP, subir artefactos a VT, exportar PCAP y memory dump para crear reglas IDS/Suricata.

### 6. Ejemplo de YARA (útil dentro del sandbox)

```yara
rule Detect_C2_String {
  meta:
    author = "Gustavo"
    description = "Detecta string duro del C2"
  strings:
    $s1 = "c2-01.bad"
    $s2 = "POST /collect"
  condition:
    any of them
}
```

Aplica esta regla a dumps/memory extraídos del sandbox para detectar coincidencias.

### 7. Código de ejemplo — extraer IOCs de un informe JSON (plantilla en Python)

Este script es genérico: intenta leer claves comunes y extraer dominios, IPs, hashes y paths. (No requiere ejecutar nada en el sandbox; procesa el JSON que te devuelva el sandbox.)

```python
import json
import re

def extract_iocs(report_json):
    iocs = {"domains": set(), "ips": set(), "hashes": set(), "file_paths": set()}
    text = json.dumps(report_json).lower()

    # dominios simples
    for m in re.finditer(r"[a-z0-9.-]+\.(?:com|net|org|info|biz|ru|cn|io|co)(?:\:[0-9]{1,5})?", text):
        iocs["domains"].add(m.group(0))

    # IPs
    for m in re.finditer(r"\b(?:\d{1,3}\.){3}\d{1,3}\b", text):
        iocs["ips"].add(m.group(0))

    # hashes (sha256/sha1/md5)
    for m in re.finditer(r"\b[a-f0-9]{64}\b", text):
        iocs["hashes"].add(m.group(0))
    for m in re.finditer(r"\b[a-f0-9]{40}\b", text):
        iocs["hashes"].add(m.group(0))
    for m in re.finditer(r"\b[a-f0-9]{32}\b", text):
        iocs["hashes"].add(m.group(0))

    # rutas de archivos simples (windows)
    for m in re.finditer(r"(?:c:\\|%appdata%|%temp%)[^\"\\,;\n]+", text):
        iocs["file_paths"].add(m.group(0))

    # convertir sets a listas
    return {k:list(v) for k,v in iocs.items()}

# uso:
# with open("sandbox_report.json") as f:
#     report = json.load(f)
# print(extract_iocs(report))
```

### 8. Artefactos clave que debes exportar siempre

- **PCAP** (para crear reglas IDS y revisar tráfico).
- **Memory dumps** (para análisis con Volatility/GRR).
- **Archivos dropeados** (para subir a VT o replicar análisis).
- **Reporte JSON/HTML** (para adjuntar al ticket).
- **Hashes** (MD5/SHA1/SHA256) y lista de dominios/IPs.

Formato sugerido para IOCs (CSV simple):

```
type,value,source
sha256,3b7a...e8c4,sandbox
domain,c2-01.bad[.]com,sandbox
ip,203.0.113.45,sandbox
file_path,%AppData%\svchost_fake.exe,sandbox
registry,HKCU\...\Run\update,sandbox
```

### 9. Cómo integrar un sandbox en tu flujo SOC / SOAR

- **Automatización básica**: EDR o gateway de correo envía muestra/URL al sandbox (unlisted/private).
- **Polling / Webhook**: tu SOAR espera el resultado y lo parsea (extrae IOCs).
- **Reglas**: si `network.c2_count>0` o `persistence==true` → aislar host + crear incidente en ITSM + bloquear IOCs.
- **Enriquecimiento**: enviar hashes/domains a VirusTotal, MISP y reputational feeds antes de bloquear definitivamente.
- **Triage humano**: analista revisa screenshots/process tree y confirma antes de acciones destructivas (borrado masivo, bloqueo global).

### 10. Evasiones comunes y cómo mitigarlas

- **Fingerprinting del sandbox**: malware detecta VM (hardware, timestamps, drivers) y no ejecuta payload.

  - _Mitigación_: variar presets, simular interacción humana, usar sandboxes interactivas o On-Prem con fingerprints más realistas.

- **Delays / sleep largos**: malware espera horas.

  - _Mitigación_: incrementar tiempo de ejecución o usar técnicas que aceleran relojes (si la plataforma lo permite).

- **Depuración / anti-instrumentation**: checksums, detectores de hooks.

  - _Mitigación_: usar varios sandboxes, combinar análisis estático y dinámico, y extraer memoria.

- **Carga condicional por geolocalización**: sirve payload según país.

  - _Mitigación_: ejecutar scans desde distintas geolocalizaciones o configurar país en sandbox (si el servicio lo permite).

### 11. Buenas prácticas y precauciones

- **Calcular hashes antes de subir** (evita exponer datos involuntarios).
- **No subir PII o secretos** a sandboxes públicos; usa `private` o On-Prem.
- **Documenta cada interacción** (qué clics/inputs hiciste) para reproducibilidad.
- **Correlaciona**: sandbox + VirusTotal + urlscan + feeds TI antes de tomar decisiones críticas.
- **Mantén catálogo de IOCs y reglas (YARA/Suricata)** con artefactos validados.
- **Respeta licencias y cuotas**: sandboxes comerciales aplican límites; open-source requiere infra.

### 12. Checklist rápido para cada análisis

- [ ] Hashs calculados (MD5/SHA1/SHA256).
- [ ] Visibilidad apropiada (public/unlisted/private/on-prem).
- [ ] Preset correcto (SO, Office, navegador).
- [ ] Ejecutar y observar Process Tree, Network, Files, Registry.
- [ ] Interactuar si es necesario y documentar acciones.
- [ ] Exportar: PCAP, memory dump, dropeados, reporte JSON.
- [ ] Extraer IOCs y enriquecer en VT/MISP.
- [ ] Acción: bloquear/aislar según criterios.
- [ ] Guardar evidencia en ticket/incidente.

### 13. Recomendaciones para principiantes (por herramientas)

- **Comienza con**: urlscan (URLs) + Cuckoo (open-source local para archivos) + ANY.RUN (interactivo, rápido) + VirusTotal (reputación multi-AV).
- **Si trabajas en empresa** y necesitas privacidad/soporte: evalúa soluciones comerciales (Joe Sandbox, VMRay, ANY.RUN enterprise).
- **Aprende a extraer y procesar** JSONs de informes y a convertir IOCs a STIX/MISP.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 44**             | **Siguiente 46**         |
| ------------------ | ------------------------ | ------------------------ |
| [🏠](../README.md) | [⏪](./13_44_ANY_RUN.md) | [⏩](./13_46_UrlVoid.md) |
