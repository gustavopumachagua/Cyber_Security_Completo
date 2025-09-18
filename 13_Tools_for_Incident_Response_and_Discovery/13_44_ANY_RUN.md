| **Inicio**         | **atrás 43**                | **Siguiente 45**             |
| ------------------ | --------------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./13_43_urlscan_io.md) | [⏩](./13_45_Joe_Sandbox.md) |

---

## **Índice**

| Temario                                                                      |
| ---------------------------------------------------------------------------- |
| [496. ANY RUN](#496-any-run)                                                 |
| [497. Análisis de malware con ANY RUN](#497-análisis-de-malware-con-any-run) |

# **ANY RUN**

## **496. ANY RUN**

![ANY RUN](/img/13_Tools_for_Incident_Response_and_Discovery/ANY_RUN.webp "ANY RUN")

### ¿Qué es ANY.RUN? (definición clara)

**ANY.RUN** es una plataforma en línea de análisis dinámico (sandbox) que permite ejecutar y analizar archivos y URLs en máquinas virtuales seguras _de forma interactiva_. A diferencia de sandboxes que sólo ejecutan una muestra y muestran resultados, ANY.RUN permite al analista **interactuar en tiempo real** con la VM (hacer clics, introducir credenciales, ejecutar comandos), registrar todas las acciones y producir informes ricos (árbol de procesos, tráfico de red, archivos creados, dumps, capturas, IOCs, etc.).

### Por qué es útil para un analista (casos de uso)

- **Triage de muestras que requieren interacción humana**: malware que pide “presionar OK”, habilitar macros o introducir credenciales.
- **Investigación de URLs/drive-by**: ver lo que carga la página, redirecciones y scripts en tiempo real.
- **Generación rápida de IOCs**: dominios, IPs, URLs, hashes, cadenas observadas en memoria/archivos.
- **Soporte SOC / DFIR**: reduce el tiempo de investigación porque puedes reproducir y forzar comportamientos del malware.

(La plataforma se promociona como optimizada para SOCs y DFIR y es usada por profesionales para acelerar investigaciones).

### Cómo funciona (flujo general, paso a paso)

1. **Subes la muestra** (archivo o URL) o creas una tarea desde la UI.
2. **Elijes configuración**: sistema operativo, versión, país de red, presets (p. ej. Windows 10/11, sandbox con Office instalado).
3. **Inicias la ejecución**: la VM arranca y la muestra se ejecuta. En ANY.RUN puedes **interactuar en vivo** con la VM (simular usuario), pausar o ejecutar acciones manuales.
4. **Registro en vivo**: VERY RUN captura procesos, llamadas al sistema, conexiones de red, paquetes, archivos creados/modificados, entradas de registro, capturas de pantalla y DOM (para URLs).
5. **Post-procesado**: genera reportes, extrae IOCs, permite exportar PCAPs, dumps de memoria y artefactos para análisis forense.

### Qué verás en un informe de ANY.RUN (campos/elementos clave)

- **Proceso / Process Tree**: jerarquía de procesos, líneas de comando y módulos cargados. (clave para ver persistencia y parent/child).
- **Network / Traffic**: conexiones salientes, dominios contactados, HTTP headers y posibilidad de descargar PCAP.
- **File system changes**: archivos creados, rutas, hashes.
- **Registry changes** (en Windows): claves creadas/modificadas que indican persistencia.
- **Screenshots / Live view**: imagen de la VM en distintos tiempos, útil para phishing/UX.
- **Memory dumps / yara matches**: si el plan lo permite, puedes extraer memoria y aplicar reglas YARA.
- **IOCs & Summary**: lista de artefactos detectados listos para exportar.

### Ejemplo práctico 1 — Usar la UI (paso a paso, ideal para principiantes)

1. Regístrate en [https://app.any.run/](https://app.any.run/) y entra.
2. Crea una nueva tarea: selecciona **File** o **URL**, escoge un preset (ej. Windows 10, navegador instalado) y la visibilidad (public / private / team).
3. Inicia la tarea. Verás la VM en el navegador y, mientras corre, se generan: proceso tree, requests, screenshots y logs.
4. Interactúa: por ejemplo, si el sample muestra un diálogo “Enable macros”, haz clic en la VM para habilitarlas y observa cambios en el árbol de procesos.
5. Exporta: descarga PCAP, archivos creados, dump de memoria o exporta el reporte en JSON/PDF según lo necesites.

### Ejemplo práctico 2 — Uso de API / automatización (concepto y ejemplo)

ANY.RUN ofrece API y una SDK para integrarlo en pipelines de triage y SOAR/SIEM (necesitas cuenta y API key). Siempre revisa la **documentación oficial** para endpoints concretos y límites.

Ejemplo conceptual (cURL **de ejemplo**, sustituye `API_ENDPOINT` y `API_KEY` por los dados en la documentación):

```bash
# 1) Subir/crear tarea (conceptual)
curl -X POST "https://API_ENDPOINT/analysis" \
  -H "Authorization: Bearer TU_API_KEY" \
  -F "file=@/ruta/muestra.exe" \
  -F "preset=windows-10" \
  -F "visibility=private"

# 2) Consultar estado / obtener informe (conceptual)
curl -X GET "https://API_ENDPOINT/analysis/ID_DEL_ANALISIS" \
  -H "Authorization: Bearer TU_API_KEY"
```

> Nota: los nombres exactos de endpoints, parámetros y formatos deben consultarse en la **API docs** de ANY.RUN antes de implementarlo.

### Ejemplo interpretativo (cómo decidir si algo es malicioso)

Supón que ejecutas `sospechoso.exe` en ANY.RUN y observas:

- El proceso `sospechoso.exe` crea `svchost.exe` en `%AppData%` y añade clave `HKCU\Software\Microsoft\Windows\CurrentVersion\Run\evil` → indicador de persistencia.
- Se observan conexiones HTTP POST a `c2-123[.]example` y transferencia de datos (payload) → posible C2.
- Descarga de DLLs ofuscadas y llamadas a `CreateRemoteThread` → comportamiento típico de loader.
  En conjunto: múltiples indicadores dinámicos + comunicaciones C2 => alta probabilidad de malicioso. Extrae IOCs (dominios, IPs, hashes) y actúa: aislar host, bloquear dominios/IPs, y escalar a respuesta a incidentes. (El árbol de procesos y la captura en ANY.RUN te ayudan a documentar el caso).

### Integración con SOC / SOAR (playbook simple)

1. **Alerta inicial** (email/EDR detecta archivo o URL).
2. **Automático**: el playbook envía la muestra/URL a ANY.RUN (API) en `unlisted/private`.
3. **Polling / webhook**: espera el informe o recibe notificación de finalización.
4. **Parseo**: extrae `process_tree`, `network IOCs`, `files created`, `yara matches`.
5. **Reglas**: si hay >X indicadores C2 o persistencia detectada → auto-aislar host y abrir ticket.
6. **Enriquecimiento**: añadir IOCs a bloqueadores (firewall/proxy) y al SIEM.
   (ANY.RUN se integra con plataformas y ofrece conectores / SDKs; revisa su API y SDKs en GitHub para ejemplos).

### Limitaciones y precauciones (importantísimo)

- **Evasión por sandbox-fingerprint**: malware avanzado detecta entornos virtuales y cambia comportamiento. Si sospechas evasión, prueba presets distintos, modifica user activity o usa interactividad manual.
- **Privacidad / sensibilidad**: dependiendo del plan, las tareas pueden quedar accesibles; **no** subas datos sensibles a menos que uses opciones privadas y el plan lo permita. Ver políticas y opciones de visibilidad.
- **Requisitos de licencia y cuota**: la API y capacidades avanzadas suelen necesitar planes de pago; la cuenta “community” ofrece funcionalidades limitadas. Consulta límites de API y cuotas.

### Buenas prácticas para principiantes

- **Siempre comienza con la UI en modo `unlisted/private`** si la muestra viene de un entorno productivo.
- **Documenta cada interacción** que hagas en la VM (qué clics, qué inputs) para reproducibilidad.
- **Extrae y guarda PCAP y dumps** si vas a hacer análisis forense posterior.
- **Aplica YARA/local rules** sobre memoria/archivos extraídos para correlacionar con reglas internas.
- **Combina con otras fuentes** (VirusTotal, urlscan, TI feeds) — ANY.RUN es parte del conjunto, no un reemplazo.

### Recursos oficiales y lectura recomendada

- Página y características de ANY.RUN (features).
- Portal principal / producto: ANY.RUN Interactive Sandbox.
- Documentación de la API (lee aquí antes de automatizar).
- Blog / artículos sobre process tree y técnicas para usar ANY.RUN eficientemente.
- “About us” / estadísticas y productos (TI Lookup, feeds).

---

[🔼](#índice)

---

## **497. Análisis de malware con ANY RUN**

### 1) ¿Por qué usar ANY.RUN para análisis de malware?

ANY.RUN es una sandbox **interactiva**: a diferencia de muchos sandboxes automáticos, te permite ver la máquina virtual en tiempo real e interactuar con ella (hacer clics, introducir datos, activar macros, simular usuario). Esto es clave con malware que:

- requiere interacción humana (p. ej. “Enable macros”),
- espera un evento (clic, input) para desplegar payload,
- se comporta diferente si detecta automatización.

ANY.RUN captura: árbol de procesos, tráfico de red (PCAP), archivos creados, claves de registro, capturas de pantalla, dumps y opciones de exportación. Eso acelera triage y generación de IOCs.

### 2) Preparación antes de ejecutar una muestra

1. **Cuenta y visibilidad**: usa una cuenta con plan adecuado. Para muestras sensibles, selecciona visibilidad `private`/`team` (no `public`).
2. **Entorno**: elige preset (Windows 7/10/11, Office instalado, navegador) que represente el entorno de la víctima.
3. **Aislamiento de red**: por defecto ANY.RUN usa red controlada; confirma que la red no permitirá daño a terceros.
4. **Toma de precauciones legales y privacidad**: no subas datos sensibles ni muestras que contengan secretos sin políticas internas aprobadas.
5. **Recolección previa**: guarda hash (SHA256), origen (mail, URL), y metadatos. Calcula hash localmente antes de subir.

Ejemplo (Linux):

```bash
sha256sum sospechoso.exe
# 3b7a...e8c4  sospechoso.exe
```

### 3) Flujo básico de análisis interactivo (paso a paso)

1. **Crear nueva tarea** → elegir `File` o `URL` → seleccionar preset (p. ej. `Windows 10 + Office`) → `visibility: private`.
2. **Iniciar ejecución** → observa la consola / live view.
3. **MONITOREA en vivo**:

   - Árbol de procesos (Process Tree).
   - Panel Network (conexiones, dominios, HTTP headers).
   - File System (archivos creados / modificados).
   - Registry (claves añadidas para persistencia).
   - Screenshots / Live viewer (comportamientos visuales).

4. **INTERACTÚA si es necesario**: si ves “Enable content” o un dialog, haz clic en la VM para permitirlo y observa cambios.
5. **PAUSA/REWIND**: pausa la ejecución para analizar un momento; toma screenshots.
6. **EXPORTA**: PCAPs, archivos extraídos, dumps de memoria y JSON/PDF del informe.

### 4) Qué buscar y cómo interpretarlo (indicadores claves)

A continuación los elementos que más importan en triage con ejemplos de interpretación.

#### a) Árbol de procesos

- **Padres inesperados**: proceso malicioso lanzado por `explorer.exe` vs. por `mshta.exe` — da pistas de vectores.
- **API suspicious / técnicas**: llamadas a `CreateRemoteThread`, `VirtualAllocEx` → indicadores de inyección.
  **Ejemplo**: `docmacro.exe` → `winword.exe` → `rundll32.exe` creando `svchost.exe` en `%AppData%` → sugiere loader + persistencia.

#### b) Tráfico de red

- **Conexiones a dominios dinámicos** (DGA) o IPs en ASNs sospechosos.
- **HTTP POST con datos extraños** → exfiltración.
- **User-agent o headers ofuscados**.
  **Ejemplo**: se observan POST a `hxxp://c2-123[.]evil/t` con payload cifrado → alto riesgo C2.

#### c) Archivos y persistencia

- Archivos nuevos en `%AppData%`, `Startup` o claves en `HKCU\...\Run` → persistencia.
- DLLs dropeadas y cargadas por procesos legítimos → DLL sideloading.

#### d) Registro y servicios

- Creación de servicio windows con `sc create` o modificación `services`.
- Entradas `Run` o `Scheduled Tasks`.

#### e) Artefactos en memoria / YARA

- Coincidencia con YARA rules (firmas) o strings característicos.
- Si puedes, extrae dump de memoria y ejecútalas contra YARA.

### 5) Ejemplo de triage realista (simulado) — paso a paso

Supón que subes `malware_sample.exe`:

1. **Observación inicial**: al ejecutar aparece `malware_sample.exe` en pantalla; proceso padre `winword.exe` (muestra vector: macro).
2. **Process tree**: `malware_sample.exe` crea `svchost_fake.exe` en `%AppData%` y llama `reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v update /d "%AppData%\svchost_fake.exe"`. → Persistencia detectada.
3. **Network**: conexiones a `c2-01.badhost[.]com:443` y envío de datos (POST). DNS lookups muestran resolución a IPs en ASN poco reputado.
4. **Files**: dropea `config.dat` (contenido base64), `temp.dll`. Hashes calculados.
5. **Decision de triage**: evidencia dinámica de persistencia + comunicación C2 -> marcar como **malicioso**, aislar host, bloquear dominio/IP y abrir ticket IR.
6. **Extraer IOCs**: dominio(s), IP(s), hashes (SHA256), rutas de archivos, claves de registro. Exportar PCAP y dumps para equipo forense.

### 6) Extracción y formato de IOCs (qué y cómo)

Para integración en SIEM/SOAR o bloqueo, extrae:

- Hashes: MD5 / SHA1 / SHA256
- Dominios y URLs (normalizados, p. ej. `example[.]com`)
- IPs y ASNs
- Rutas y nombres de archivos dropeados
- Entradas de registro y servicios creados
- Strings significativos (p. ej. nombres de C2 endpoints)

Formato común: CSV o STIX/TAXII (si tu SOC lo usa). Ejemplo CSV (simplificado):

```
type,value,source
sha256,3b7a...e8c4,any.run
domain,c2-01.badhost[.]com,any.run
ip,203.0.113.45,any.run
file_path,%AppData%\svchost_fake.exe,any.run
registry,HKCU\...\Run\update,any.run
```

### 7) Exportar artefactos útiles

- **PCAP**: para análisis de red y retroalimentación a IDS/Suricata (reglas).
- **Memory dump**: para YARA / Volatility.
- **Files dropeados**: subir a VirusTotal / sandboxes adicionales.
- **Reporte JSON/PDF**: para adjuntar al ticket.

### 8) Ejemplo de YARA sencilla (para buscar patrón en memoria/archivo)

```yara
rule Ejemplo_C2_String {
  meta:
    author = "Gustavo"
    description = "Detecta cadena de C2 duro en muestras"
  strings:
    $c2 = "c2-01.badhost" fullword ascii
    $s1 = { 63 32 2D 30 31 2E 62 61 64 68 6F 73 74 } // hex "c2-01.badhost"
  condition:
    $c2 or $s1
}
```

Aplica esta regla sobre dumps extraídos desde ANY.RUN para buscar coincidencias.

### 9) Integración en un playbook SOAR — pseudocódigo

```pseudo
on_alert(file_hash, sample_path_or_url):
  if exist_in_repo(file_hash): enrich_with_existing()
  else:
    task = anyrun.create_analysis(sample_path_or_url, preset="win10_office", visibility="private")
    wait_for_completion(task)  # polling / webhook
    report = anyrun.get_report(task.id)
    iocs = parse_iocs(report)
    if report.indicates_persistence or report.network.c2_count > 0:
       isolate_host(alert.host)
       block_iocs(iocs)
       create_incident(ticket, report.summary, attachments=[pcap, dump, files])
    else:
       mark_as_low_priority_and_monitor()
```

(Adapta a tu SOAR: Phantom, Demisto, TheHive, etc.)

### 10) Cómo lidiar con evasiones y comportamientos anti-sandbox

Malware puede detectar sandbox o entornos virtualizados. Tácticas para mitigar:

- **Cambiar preset**: usar diferentes SO/versión, agregar Office si se sospecha vector de documento.
- **Simular interacción humana**: en ANY.RUN puedes hacer clics, introducir teclas o ejecutar manualmente pasos (activar macros).
- **Variar red / country**: algunas muestras sirven payloads según geolocalización.
- **Modificar fingerprint**: cambiar user-agent, instalar plugins, aumentar tiempo de ejecución (si el malware tiene delays).
- **Multiple runs**: ejecutar varias veces con distintos parámetros; correlacionar resultados.

Cuidado: estas acciones requieren experiencia y justificación, porque pueden cambiar la muestra y deben registrarse para reproducibilidad.

### 11) Buenas prácticas y límites éticos

- **No interactúes con muestras fuera del entorno**; usa siempre la VM.
- **Registra todo** (clics, inputs) para reproducibilidad y trazabilidad.
- **No subas datos sensibles** ni samples que contengan PII sin autorización.
- **Combina fuentes**: usa ANY.RUN + VirusTotal + urlscan + feeds de TI para confirmar hallazgos.
- **Mantén catálogo de IOCs** y reglas (YARA / Suricata) a partir de artefactos confiables.

### 12) Ejemplos de comandos conceptuales (cURL / API)

ANY.RUN tiene API y endpoints, pero **los nombres y parámetros precisos pueden cambiar** según versiones y planes — confirma en su documentación antes de automatizar. Ejemplo conceptual:

**Subir/crear análisis (conceptual)**:

```bash
curl -X POST "https://api.any.run/analyses" \
 -H "Authorization: Bearer TU_API_KEY" \
 -F "file=@/ruta/muestra.exe" \
 -F "preset=windows-10" \
 -F "visibility=private"
```

**Obtener informe (conceptual)**:

```bash
curl -X GET "https://api.any.run/analyses/ANALYSIS_ID" -H "Authorization: Bearer TU_API_KEY"
```

(Consulta docs de ANY.RUN para payloads exactos y formatos de respuesta.)

### 13) Checklist rápido para cada análisis

- [ ] Calcular y registrar hashes antes de subir.
- [ ] Seleccionar preset apropiado (SO, Office, navegador).
- [ ] Ejecutar y observar process tree / network / files / registry.
- [ ] Interactuar si la muestra requiere (clics, enable content).
- [ ] Exportar PCAP, files dropeados y memory dump si es necesario.
- [ ] Generar IOCs y subir/hacer búsqueda en feeds (VT, MISP).
- [ ] Tomar acción en base a criterios (aislar, bloquear, monitorear).
- [ ] Documentar pasos y resultados en el ticket.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 43**                | **Siguiente 45**             |
| ------------------ | --------------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./13_43_urlscan_io.md) | [⏩](./13_45_Joe_Sandbox.md) |
