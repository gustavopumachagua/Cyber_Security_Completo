| **Inicio**         | **atrás 3**                                | **Siguiente 5**                      |
| ------------------ | ------------------------------------------ | ------------------------------------ |
| [🏠](../README.md) | [⏪](./8_3_True_Negative_True_Positive.md) | [⏩](./8_5_Understand_Handshakes.md) |

---

## **Índice**

| Temario                                                                                      |
| -------------------------------------------------------------------------------------------- |
| [206. Inteligencia de fuentes abiertas (OSINT)](#206-inteligencia-de-fuentes-abiertas-osint) |
| [207. OSINT Framework](#207-osint-framework)                                                 |
| [208. Inteligencia de amenazas](#208-inteligencia-de-amenazas)                               |

# **Conceptos básicos de inteligencia de amenazas (OSINT)**

## **206. Inteligencia de fuentes abiertas (OSINT)**

![OSINT](/img/8_Security_Skills_and_Knowledge/OSINT.png "OSINT")

### ¿Qué es OSINT (Inteligencia de Fuentes Abiertas)?

**OSINT** (Open-Source Intelligence) es la disciplina de **recopilar, evaluar y analizar información disponible públicamente** (legalmente accesible) para responder a una pregunta de inteligencia concreta: quién es un objetivo, cuál es la superficie de ataque de una empresa, qué ocurrió en un incidente, etc. OSINT no es sólo “googlear”: implica método, verificación y documentación de evidencias.

### ¿Para qué se usa OSINT? (casos de uso)

- **Ciberseguridad / Red Teaming**: reconocimiento inicial (fuente de IOCs, subdominios, exposiciones).
- **Blue Team / Threat Intelligence**: encontrar IOCs públicos, filtrar alerts y enriquecer detecciones.
- **Investigación periodística**: confirmar hechos, identificar actores y verificar imágenes.
- **Fraud & Risk**: verificación de identidad, detección de fraudes y cumplimiento.
- **Due diligence** antes de fusiones/contratos.
  OSINT es transversal: gobiernos, empresas, periodistas y consultoras lo usan para obtener contexto accionable.

### Fuentes de OSINT — categorías y ejemplos prácticos

(organiza tu búsqueda: primero pregunta → luego identidades/infraestructura/contexto)

1. **Web pública & buscadores**: Google, Bing, Wayback (archivos históricos).
2. **Registros de dominio / WHOIS / DNS**: whois, DNS records, certificados TLS (CT logs).
3. **Infraestructura expuesta**: Shodan, Censys, ZoomEye (servicios/puertos/IoT).
4. **Repositorios de breaches / dumps**: HaveIBeenPwned, Intelligence X, Leak sites.
5. **Redes sociales**: LinkedIn, Twitter/X, Facebook, Instagram, TikTok y foros (Reddit, StackExchange).
6. **Metadatos y documentos**: EXIF en imágenes, propiedades de Office/PDF (FOCA, exiftool).
7. **Registros públicos y bases de datos**: registros mercantiles, patentes, registros gubernamentales.
8. **Contenido multimedia/geo**: imágenes geolocalizables, videos (YouTube), fotogrametría.
9. **Dark web & paste sites** (con cautela y legalidad): textos filtrados, foros cerrados.
   Para orientarte sobre fuentes y herramientas hay marcos que las categorizan (OSINT Framework es un buen índice de recursos).

### Herramientas frecuentes y para qué sirven (rápido)

- **Recon / footprinting**: `theHarvester`, `Recon-ng`, `amass`, `subfinder` (subdominios, correos, hostnames).
- **Mapeo y gráficos**: **Maltego** (relaciones), **SpiderFoot** (automatización y correlación).
- **Búsqueda de activos expuestos**: **Shodan**, **Censys**, **ZoomEye**.
- **Bases de breaches / reputación**: **HaveIBeenPwned**, **VirusTotal**, **Intelligence X**.
- **Metadatos / documentos**: **exiftool**, **FOCA**, **Metagoofil**.
- **Google Dorks**: consultas avanzadas para encontrar archivos, paneles expuestos, páginas con credenciales.
- **APIs y plataformas comerciales**: SpiderFoot HX, Recorded Future, OSINT-as-a-Service.
  Las listas de herramientas actualizadas y comparativas se renuevan cada año; algunas referencias prácticas con listados están disponibles públicamente.

### Técnicas clave (con ejemplos seguros y autorizados)

#### 1) Reconnaissance pasivo (sin tocar al objetivo)

- **Objetivo**: obtener información sin interactuar con los sistemas del objetivo (reduce huella).
- **Ejemplo**: buscar direcciones de correo en GitHub, revisar certificados TLS públicos (crt.sh), consultar registros WHOIS.

  - Comando ejemplo (local, sólo lectura): `whois example.com` — devuelve registrante y DNS.

- Uso típico para perfilar la superficie de ataque sin generar logs en la red objetivo.

#### 2) Recon activo (con interacción autorizada)

- **Objetivo**: enumerar subdominios, puertos o servicios (si está autorizado).
- **Ejemplo**: `amass enum -d example.com` para subdominios; `nmap` para puertos (solo en entornos autorizados).
- **Importante**: sólo ejecutar scans activos con permiso por escrito.

#### 3) Google Dorking

- **Ejemplo**: `site:example.com filetype:pdf "contraseña" OR "password"` → busca PDFs que contengan la palabra “password”.
- Útil para encontrar información filtrada en el sitio público.

#### 4) Metadata y verificación de imágenes

- Extraer EXIF: `exiftool foto.jpg` → ver fecha, modelo de cámara y coordenadas si existen.
- Verificar si una imagen es antigua o fue manipulada (reverse image search, TinEye, Google Images).

#### 5) Correlación y enriquecimiento

- Tomar un correo encontrado → buscar en HaveIBeenPwned y VirusTotal para ver si aparece en breaches. (HIBP ofrece API autenticada).

### Flujo de trabajo OSINT (práctico, metodología)

1. **Definir pregunta de inteligencia** (qué quieres saber y por qué).
2. **Planificar límites y legalidad** (qué dominios/entidades están en scope y obtener autorización).
3. **Recolectar pasivamente** (buscadores, repositorios, CT logs, redes sociales).
4. **Enriquecer y automatizar** (usar APIs, SpiderFoot, Maltego para correlación).
5. **Validar / verificar** (triangulación: ≥2 fuentes independientes).
6. **Analizar y documentar** (timeline, TTPs, evidencias con metadatos y capturas).
7. **Reportar con mitigaciones** (si detectas exposición, notificar responsable).
   Registro cuidadoso del proceso es clave (qué buscaste, cuándo y con qué herramienta).

### Ejemplo práctico seguro (uso ilustrativo — _usa example.com o entornos autorizados_)

**Objetivo:** identificar subdominios y comprobación rápida de exposición pública.

1. `whois example.com` → obtener registrante y servidores DNS.
2. `amass enum -d example.com` → lista de subdominios (sin intrusión).
3. `theHarvester -d example.com -b all` → correos y hosts públicos (emails/hosts indexados).
4. `shodan host <IP>` o `shodan search "hostname:sub.example.com"` → ver servicios expuestos.
5. Revisar `crt.sh` para certificados TLS asociados a `example.com` (posibles subdominios): buscar filtraciones de nombres.
6. Cross-check con VirusTotal / HIBP para correos encontrados.

> **Advertencia**: los pasos 2–4 pueden generar tráfico: solo ejecutarlos con autorización si vas a escanear activos que no son tuyos. Usa `example.com` para practicar.

### Verificación y mitigación de hallazgos (buenas prácticas)

- **Triangula**: confirma un dato con al menos 2 fuentes independientes (p. ej. CT log + DNS + historial web).
- **Guarda evidencia**: captura, hashes, timestamps y URLs.
- **Clasifica riesgo**: exposición trivial vs. datos sensitivos (credenciales, secretos).
- **Notifica responsable**: proceso de divulgación responsable (coordinado) si encuentras credenciales/secretos.
- **Mitigación recomendada**: rotación de credenciales, corrección de configuraciones, bloqueo público temporal si aplica.

### Ética, legalidad y OPSEC (no negociable)

- **Siempre** trabaja solo en objetivos para los que tienes **autorización escrita** (pentest/Red Team) o en entornos públicos cuando el objetivo es información legítimamente pública.
- **No uses** OSINT para ingeniería social maliciosa, fraude, acceso no autorizado o publicar datos sensibles.
- **Cumple leyes locales** (protección de datos, delitos informáticos) y políticas internas.
- **OPSEC para el investigador**: evita dejar rastros innecesarios, usa entornos aislados, considera VPN/VM si tu actividad puede atraer atención.
  Estos límites protegen tanto al investigador como a terceros.

### Cómo integrarlo con Red Teams y Blue Teams

- **Red Team** usa OSINT para construir escenarios realistas (phishing, identificación de targets, spear-phishing templates).
- **Blue Team** usa OSINT para enriquecer detecciones (IOCs públicos, dominios de phishing, fugas de datos).
- **Purple Teaming**: compartir hallazgos OSINT entre ofensiva y defensa para mejorar reglas SIEM/EDR y playbooks SOAR.

### Riesgos, limitaciones y falsos positivos

- **Datos desactualizados**: muchos listados y dumps están obsoletos.
- **Identidades equivocadas**: nombres comunes producen falsos positivos (ej. varias “Juan Pérez”).
- **Envenenamiento de datos**: adversarios pueden publicar info falsa para desviar investigaciones.
- **Privacidad**: algunas fuentes públicas no implican permiso para uso masivo.

  Mitigar con verificación y documentación.

### Automatización y APIs (escala responsable)

- Usa APIs oficiales (Shodan, Censys, VirusTotal, HIBP) con claves y respetando TOS. HIBP, por ejemplo, ofrece API autenticada para buscar cuentas en breaches.
- Plataformas como **SpiderFoot HX** automatizan escaneos y correlación (útil para monitoreo del ataque de superficie).

### Recursos para estudiar OSINT (inicio rápido)

- **OSINT Framework** — índice de fuentes y herramientas.
- **SANS OSINT articles & training** — cursos y material formativo.
- Blogs y listas de herramientas actualizadas (p. ej. listados 2024–2025 sobre SpiderFoot, Maltego, Shodan).

### Cheat-sheet / Checklist rápido (para tus prácticas autorizadas)

1. Define pregunta y alcance.
2. Haz recon pasivo primero (Google, CT logs, WHOIS).
3. Guarda cada hallazgo: URL, timestamp, captura, hash.
4. Enriquécelo (Shodan, HIBP, VirusTotal).
5. Verifica con ≥2 fuentes.
6. Documenta riesgo y proposed remediation.
7. Notifica responsable siguiendo política de divulgación.
8. Borra/archiva tus pruebas y registra acceso (auditoría).

---

[🔼](#índice)

---

## **207. OSINT Framework**

El **OSINT Framework** es un índice web organizado de herramientas y recursos públicos que facilita encontrar fuentes para tareas de _Open-Source Intelligence_ (OSINT). No es una herramienta que haga búsquedas por ti: es un **mapa** (categorías y enlaces) que te ayuda a escoger la herramienta adecuada según lo que buscas (emails, subdominios, certificados, imágenes, redes sociales, etc.).

### ¿Por qué usarlo? — Ventaja práctica

- Ahorra tiempo: en lugar de recordar decenas de URLs, navegas por categorías (People, Domains, Metadata, Dark Web, Imaging, Social, etc.).
- Enseña flujo: te guía desde reconocimiento pasivo hasta técnicas de enriquecimiento y verificación.
- Centraliza alternativas: para una búsqueda concreta verás tanto herramientas gratuitas como comerciales.

### Cómo está organizado (visión general)

El sitio está dividido en **categorías** y subcategorías (por ejemplo: _Domain Names → Certificate Transparency → crt.sh_; _People → Social Networks → LinkedIn_; _Imaging → EXIF → exiftool_). Cada entrada normalmente apunta a una o varias herramientas que son apropiadas para ese tipo de búsqueda. Esa estructura hace sencillo convertir una pregunta ("¿qué subdominios tiene mi dominio?") en una lista de pasos concretos y herramientas.

### Cómo usar el OSINT Framework — flujo práctico (breve)

1. **Define la pregunta de inteligencia** (ej.: “¿qué subdominios públicos expone example.com?”).
2. **Abrir la categoría correcta** en OSINT Framework (Domain Names → CT logs / DNS / Subdomains).
3. **Seleccionar herramienta(s)** sugeridas (por ejemplo: crt.sh, amass, subfinder, VirusTotal).
4. **Ejecutar la(s) herramienta(s)** en modo pasivo primero (crt.sh, búsquedas en CT logs) y luego activo _solo si tienes permiso_ (amass, nmap).
5. **Correlacionar resultados** (cruzar CT logs + DNS + Shodan + histórico web) y documentar evidencia.

### Ejemplo práctico paso a paso (seguro — usa `example.com` como objetivo de práctica)

**Objetivo:** enumerar subdominios públicos y comprobar si hay servicios expuestos.

1. _Paso pasivo — Certificate Transparency (sin tocar hosts)_

   - En OSINT Framework: `Domain Names → Certificate Transparency → crt.sh` (o usar la API de crt.sh).
   - Comando (local): abrir en navegador `https://crt.sh/?q=%25example.com` → verás certificados con nombres (subdominios).

2. _Paso pasivo — búsquedas adicionales_

   - VirusTotal / PassiveTotal: buscar dominio para ver subdominios y activos históricos.
   - Shodan/Censys: buscar por hostname/IP para ver servicios expuestos (puertos, banners).
   - Ejemplo de uso de Shodan (si tienes API key):
     `shodan host 93.184.216.34` (reemplaza con IP real).
   - Estos enlaces y herramientas están referenciados en las secciones de OSINT Framework para "Infrastructure".

3. _Paso activo (solo con permiso)_

   - `amass enum -d example.com` → deporte de subdominios a partir de fuentes públicas y brute-force.
   - `nmap -Pn -sV -p- target.ip` → identificar servicios abiertos **solo si te han autorizado**.
   - Nota de OPSEC/legal: no ejecutes escaneos contra activos que no te pertenecen o para los que no tienes autorización expresa.

4. _Correlación y verificación_

   - Comparar resultados: ¿aparecen en crt.sh, en amass y en Shodan?
   - Guardar evidencias: capturas, URLs, timestamp y hash si corresponde.
   - Clasificar riesgo: panel expuesto, servicio desactualizado, credenciales en ficheros públicos, etc.

### Ejemplos concretos de categorías y herramientas comunes (rápido)

- **People / Usernames:** Pipl, SocialSearch, Hunter, GitHub search.
- **Emails:** HaveIBeenPwned, Hunter.io, MailTester.
- **Domains / Subdomains:** crt.sh, amass, subfinder, VirusTotal.
- **Infraestructura / Hosts:** Shodan, Censys, ZoomEye.
- **Metadata / Documents:** exiftool, FOCA.
- **Social media:** X/Twitter search operators, LinkedIn, Facebook (según privacidad).
  La propia página del OSINT Framework lista múltiples alternativas para cada una de estas necesidades.

### Integración con workflows de seguridad (Red / Blue / Purple)

- **Red Team:** usa el Framework para construir spear-phishing targets (identificar correos, perfiles, fotos, contextos).
- **Blue Team / TI:** usa las mismas fuentes para _monitoring_ y detección temprana (p.ej. detectar filtraciones públicas, credenciales expuestas).
- **Purple Team:** comparte hallazgos OSINT entre ofensiva y defensa para crear reglas SIEM/EDR y playbooks SOAR más precisos. La disciplina OSINT/adquisición estructurada está recomendada en guías y prácticas de CTI.

### Automatización y APIs (escalado responsable)

- Muchas herramientas listadas en el Framework ofrecen API (Shodan, Censys, VirusTotal, HIBP). Puedes orquestarlas con scripts, SpiderFoot o plataformas comerciales para monitorizar cambios en el perímetro.
- Consejos: usa claves con límites, respeta TOS y evita scrapers agresivos que provoquen bloqueo o problemas legales.

### Limitaciones, riesgos y legalidad

- **Datos públicos ≠ permiso para abuso**: recoger información pública es lícito en muchos casos, pero acciones activas (scans, fuzzing, intrusión) requieren autorización.
- **Envenenamiento y falsos positivos**: fuentes públicas pueden contener desinformación o datos obsoletos; siempre verifica con ≥2 fuentes.
- **Privacidad y cumplimiento**: ten cuidado con datos personales sensibles y con regulaciones locales (GDPR/LPD, etc.).
- Buenas prácticas y cursos profesionales (p. ej. SANS) recomiendan marcos de trabajo y rigor documental para no cruzar límites legales/éticos.

### Buenas prácticas / OPSEC para quien investiga

- Trabaja en VM aislada y registra cada paso (timestamps, herramientas, comandos).
- Usa cuentas/API con límites y proxies si necesitas ocultar tu IP **solo** por protección de OPSEC — no para evadir la ley.
- Mantén un registro de permisos: si haces scans, ten consentimiento por escrito.
- Documenta todo: hallazgos, fuentes, capturas y decisiones (vs. publicar evidencia sin coordinar).

### Mini-cheat-sheet (rápido, imprimible)

1. Pregunta → categoría del Framework.
2. Comienza pasivo (crt.sh, whois, buscadores).
3. Enriquecer (Shodan, VirusTotal, HIBP).
4. Verificar (sandbox, correlación ≥2 fuentes).
5. Actuar solo con permiso (scans/penetración).
6. Registrar y notificar responsable si hay datos sensibles.

### Recursos recomendados (lectura y aprendizaje)

- Sitio principal OSINT Framework — índice y categorías.
- Guía/entrada explicativa (Recorded Future) sobre cómo utilizar el Framework en CTI.
- “Must have” resources / cursos y referencias (SANS).
- OSINT Tools & Resources Handbook (PDF) — recopilación práctica.

---

[🔼](#índice)

---

## **208. Inteligencia de amenazas**

La **Inteligencia de Amenazas (Threat Intelligence, CTI)** es la disciplina que transforma datos sobre amenazas (logs, feeds, OSINT, informes) en **conocimiento accionable** para que una organización entienda **quién** la ataca, **cómo** (técnicas), **con qué objetivo** y **qué hacer** para mitigarlo. No es solo colección de IOCs; es contexto, análisis y operacionalización.

### Tipos / Niveles de Threat Intelligence (con ejemplos)

1. **Estratégica**

   - Enfoque: panorama largo plazo (riesgos geopolíticos, tendencias).
   - Público objetivo: junta directiva, CISO.
   - Ejemplo: “Aumento de campañas de ransomware contra proveedores en LATAM durante los últimos 6 meses”.

2. **Operacional**

   - Enfoque: campañas, actores, capacidades.
   - Público objetivo: equipos de análisis y respuesta.
   - Ejemplo: “Grupo APT X usa spear-phishing con archivos .docm y C2 en dominios registrados por registrador Y”.

3. **Táctica**

   - Enfoque: TTPs (técnicas, tácticas y procedimientos).
   - Público objetivo: hunters, SOC, red team.
   - Ejemplo: “Movimiento lateral mediante PsExec y cuentas de servicio con contraseñas sin rotar”.

4. **Técnica**

   - Enfoque: artefactos concretos (IOCs).
   - Público objetivo: SOC, infra, herramientas de seguridad.
   - Ejemplo: hash SHA256 de un binario malicioso, IP de C2, dominio malicioso.

### Ciclo de vida de la Inteligencia de Amenazas (Threat Intelligence Lifecycle)

1. **Requerimiento** — ¿qué necesita saber la organización? (priorizar).
2. **Colección** — ingestión de fuentes (telemetría interna, feeds, OSINT).
3. **Procesamiento/Normalización** — limpieza, normalización (p. ej. estandarizar timestamps, extraer hashes).
4. **Análisis** — enriquecimiento (WHOIS, VT, reputación), contextualización (actor, motivación, impacto).
5. **Producción** — informes, IOCs, TTP mappings (MITRE ATT\&CK).
6. **Difusión/Operationalización** — alimentar SIEM/EDR/Firewalls, playbooks SOAR, listas de bloqueo.
7. **Feedback** — medir efectividad y ajustar fuentes/umbral.

### Fuentes de inteligencia (ejemplos prácticos)

- **Internas**: logs EDR, telemetría de endpoints, firewall, proxy, DNS, correos (mail gateway).
- **Open-source (OSINT)**: blogs de seguridad, GitHub, paste sites, búsquedas públicas.
- **Commercial feeds**: proveedores de TI (feeds de reputación, indicadores premium).
- **Compartición comunitaria**: ISACs, MISP communities, CERTs.
- **Dark web / intel de amenazas humanas**: foros de leaks, marketplaces (solo con procesos legales/ética).

### Artefactos y elementos clave

- **IOCs (Indicators of Compromise)**: direcciones IP, dominios, URLs, hashes (MD5/SHA256), referencias a archivos.

  - Ejemplo (no clicables): Dominio C2 → `c2-example[.]malicious` ; SHA256 → `3f9b...a1b2`.

- **TTPs**: descripciones de comportamiento (ej. “uso de Living off the Land: PowerShell para descargar payload”).
- **Contexto**: atribución (actor), motivación (financiera, espionaje), objetivos (sector/país), confianza y fecha.
- **YARA/Sigma/Detections**: reglas para detectar familias de malware o patrones de logs.

### Formatos y estándares (cómo compartir)

- **STIX/TAXII** — formato y transporte para compartir CTI estructurada.
- **MISP** — plataforma de intercambio de IOCs colaborativa.
- **OpenCTI, ThreatConnect** — TIPs/PLATAFORMAS comerciales o open.
- **YARA** (malware signatures), **Sigma** (reglas para SIEM), **CEF/LEEF** (event formats), JSON/CSV para ingestión.

### Herramientas típicas (uso defensivo)

- **TIPs**: MISP, OpenCTI, ThreatConnect — para almacenar y compartir indicios y contextos.
- **Enriquecimiento**: VirusTotal, PassiveTotal, Shodan, Censys (buscar infraestructura).
- **Hunting / EDR / SIEM**: Splunk/ELK/QRadar + EDR (CrowdStrike, SentinelOne) integrados con TIP.
- **Orquestación**: SOAR (Demisto, Phantom) para aplicar playbooks que actúen sobre IOCs automáticamente.

### Ejemplo práctico (caso de uso: campaña de ransomware) — paso a paso

**Contexto:** tu SIEM recibe alertas de múltiples usuarios ejecutando un macro Office sospechoso.

1. **Recopilación inicial**

   - Telemetría EDR muestra ejecución `winword.exe → powershell -EncodedCommand ...`.
   - Mail gateway reporta mensajes con adjunto `invoice.docm`.

2. **Enriquecimiento**

   - Extraes hash del payload y lo consultas en VirusTotal → detectado por 2/70 motores (debajo del umbral, posible nueva muestra).
   - Whois/CT logs muestran dominio en los correos registrado hace 2 días.

3. **Análisis**

   - Se mapea comportamiento a MITRE ATT\&CK: T1204 (spearphishing), T1059.001 (PowerShell), T1486 (ransomware).
   - Correlación con feed: proveedor X reportó campaña RansomGang usando macros con exfil por FTP.

4. **Operationalización**

   - Añades IOC (hash, correo remitente, dominio) a TIP (MISP/OpenCTI).
   - Automatizas: SOAR bloquea dominio en proxy, crea regla EDR para detectar `powershell -EncodedCommand`, cuarentena automática de endpoints afectados.
   - Hunting: busca procesos similares en la red (EDR search).

5. **Respuesta / Mitigación**

   - Contención de hosts infectados, rotación de credenciales de cuentas de servicio, aplicación de parche en endpoint vulnerable.
   - Informe ejecutivo con impacto (hosts afectados, tiempo desde primer hallazgo).

6. **Feedback**

   - Medir MTTD (tiempo hasta detectar) y MTTR (tiempo a remediar). Ajustar umbrales y actualizar reglas Sigma/YARA para futura detección.

### Métricas y KPIs útiles para CTI

- **Relevancia / Actionability**: % de IOCs que se aplican con éxito (bloqueos que detuvieron ataques).
- **Feed quality**: tasa de falsos positivos/negativos por feed.
- **MTTD** (Mean Time To Detect) y **MTTR** (Mean Time To Remediate).
- **Coverage**: % de activos con telemetría que soporta CTI (EDR en endpoints, logs centralizados).
- **Tiempo de vida del IOC (TTL)**: antigüedad tras la cual IOC se considera obsoleta.

### Casos de uso concretos de la Inteligencia de Amenazas

- **Threat hunting** (búsqueda proactiva basada en TTPs).
- **Priorizar parches**: usar CTI para priorizar CVEs explotados activamente.
- **Respuesta a incidentes**: acelerar contener/excluir con IOCs verificados.
- **Detección y reglas**: crear firmas YARA / reglas Sigma / reglas EDR.
- **Protección de la cadena de suministro**: monitorear proveedores para compromisos.
- **Reporte estratégico**: informar a dirección sobre riesgos emergentes.

### Cómo montar un programa operativo de CTI (people + process + tech)

1. **People**

   - Analistas CTI (n1/n2), intel analyst (senior), inteligencia manager.
   - Liaison con SOC, CI/CD, infra y equipo legal.

2. **Process**

   - Definir requisitos (qué priorizar), SLAs (MTTD/MTTR), playbooks de reacción y procesos de compartir intel (coordinado disclosure).

3. **Tech**

   - TIP (MISP/OpenCTI), integraciones SIEM/EDR/SOAR, APIs para enriquecer (VT, Shodan), repositorio de reglas (Sigma/YARA).

4. **Governance**

   - Políticas de compartición, clasificación de inteligencia, manejos legales y privacidad.

### Errores comunes / Riesgos a evitar

- **Consumir demasiados feeds sin filtrado** → ruido y falsos positivos.
- **IOCs sin contexto** → bloqueos ineficaces o dañinos (falsos positivos en infra crítica).
- **No medir impacto** → CTI se vuelve “colección” sin valor operativo.
- **Compartir datos sensibles sin proceso** → problemas legales y reputacionales.

### Buenas prácticas rápidas

- Prioriza **actionability**: si no puedes automatizar o actuar sobre la intel, reduce su consumo.
- Mapea TTPs a MITRE ATT\&CK para estandarizar descripción y acelerar detección/hunting.
- Enriquecer IOCs antes de bloquear (quién, cuándo, confiabilidad).
- Automatiza playbooks de triage con SOAR para reducir tiempo humano en tareas repetitivas.
- Mantén una **lista de fuentes confiables** y evalúa periódicamente su calidad.

### Mini-checklist práctico (para empezar hoy)

- ¿Tienes EDR/Logs suficientes para apoyar CTI? → si no, priorizar instrumentación.
- Implementa un TIP simple (MISP/OpenCTI).
- Crear 3 playbooks SOAR: ingestión de IOC → enriquecimiento → bloqueo/quarantine.
- Mapea 10 TTPs críticos de tu sector en MITRE ATT\&CK y crea reglas Sigma básicas.
- Mide MTTD/MTTR y % de IOCs accionables.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**                                | **Siguiente 5**                      |
| ------------------ | ------------------------------------------ | ------------------------------------ |
| [🏠](../README.md) | [⏪](./8_3_True_Negative_True_Positive.md) | [⏩](./8_5_Understand_Handshakes.md) |
