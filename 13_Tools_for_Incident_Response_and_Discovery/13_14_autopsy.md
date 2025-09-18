| **Inicio**         | **atrás 13**            | **Siguiente 15**          |
| ------------------ | ----------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./13_13_winhex.md) | [⏩](./13_15_ipconfig.md) |

---

## **Índice**

| Temario                                                                  |
| ------------------------------------------------------------------------ |
| [438. Autopsy](#438-autopsy)                                             |
| [439. Análisis de disco con Autopsy](#439-análisis-de-disco-con-autopsy) |

# **Autopsy**

## **438. Autopsy**

Perfecto — vamos con una **guía muy completa y práctica** sobre **Autopsy**, pensada para quien quiere entender qué es, para qué sirve, cómo funciona en la práctica, ejemplos de uso y buenas prácticas forenses. Todo en español y con comandos y flujos reales (pero siempre desde una óptica legal y defensiva).

---

# ¿Qué es Autopsy?

**Autopsy** es una plataforma gráfica de análisis forense digital (GUI) construida sobre **The Sleuth Kit (TSK)**. Permite a analistas forenses y a administradores investigar imágenes de disco, particiones, memorias y otros datos digitales de forma estructurada: búsqueda de archivos, recuperación de borrados, análisis de actividad del sistema, timeline, análisis de navegadores, correos, móviles, cálculos de hashes, y generación de informes.

Autopsy combina numerosas **“ingest modules”** (módulos de procesamiento) que automatizan la extracción de artefactos y facilita la navegación, filtrado y exportación de resultados.

---

# Para qué se usa (casos de uso)

- Investigación forense de incidentes (compromisos, intrusiones).
- Análisis de sospecha de conducta inapropiada (en entornos corporativos con autorización).
- Recuperación de archivos borrados y análisis de datos en discos/formats dañados.
- Examen de actividad de usuario: navegación web, correos, recientes, USB insertados.
- Creación de informes para peritajes (con cadena de custodia).

> Siempre con **autorización legal**: usar Autopsy en sistemas de terceros sin permiso es ilegal.

---

# Componentes clave

- **Autopsy (GUI)** — interfaz web/desktop para crear casos, ver resultados y generar informes.
- **The Sleuth Kit (TSK)** — herramientas de línea de comando (fls, mmls, icat, ils, istat, etc.) que Autopsy emplea en segundo plano.
- **Ingest modules** — módulos que extraen artefactos: Web History, File Type Identification, EXIF, Email Parser, Hash Lookup, Carving, Recent Activity, Registry Parser (Windows), Mobile apps, etc.
- **Plaso / log2timeline** — se integra para timelines avanzados en algunas instalaciones.
- **Hash sets** — listas (NSRL, internos) para marcar archivos conocidos/buenos/malos.

---

# Instalación (resumen rápido)

- **Linux (Ubuntu/Debian)**: instalar Java, The Sleuth Kit, Autopsy (paquete o .deb), dependencias (python, sleuthkit).
- **Windows**: existe instalador MSI con TSK incluido (versiones binarias oficiales).
- **macOS**: soporte más limitado; personalizaciones y builds comunitarias.

(Consulta la documentación oficial para la versión y dependencias exactas antes de instalar).

---

# Flujo de trabajo típico (paso a paso) — Ejemplo práctico

### 1) Crear un caso

- Abre Autopsy → _New Case_ → completa metadatos (nombre, investigador, número de caso).
- Crea _New Host_ dentro del caso (identificador del dispositivo).

### 2) Añadir una fuente de datos (Data Source)

- `Add Data Source` → seleccionar:

  - **Disk Image or VM file** (raw `.img`, `.dd`, `.e01`, `.aff`, `.VMDK`),
  - **Local disk** (con write-blocker o sólo lectura),
  - **Logical files** o **Memory Image**.

- Elegir módulos de ingest que quieres ejecutar (por ejemplo: File Type Identification, Extract EXIF, Email Parser, Web History, Recent Activity, Keyword Search, Carving, Hash Lookup).

### 3) Ejecutar ingest (procesamiento)

- Autopsy ejecuta TSK y módulos; produce artefactos indexados en la base del caso (SQLite/solr dependiendo de la versión).
- Tiempo: depende del tamaño y módulos seleccionados; puedes pausar/retomar.

### 4) Navegar resultados (GUI)

- **Tree** de resultados: _Views_ como File Types, Interesting Items, Extracted Content, Web History, Communications, Recent Activity.
- Haz clic en un artefacto para ver detalles: metadata, hex view, preview (imagen), timeline, associated files.

### 5) Ejemplo: recuperar un JPEG borrado

- En _Views → File Types_ filtra por `jpg` y marca `Deleted` o `Carved` files.
- Selecciona archivo → `Extract` → guarda copia para análisis.
- Verifica integridad (hash) desde la vista de Autopsy.

### 6) Ejemplo: timeline de actividad

- Usa módulo _Timeline_ (o integra Plaso) para construir una línea temporal con eventos: archivos modificados, ejecuciones, conexiones web, inserciones USB.
- Ordena y filtra por rangos de tiempo para vincular actividad con un incidente.

### 7) Búsqueda por palabras clave

- `Keyword Search`: ingresa patrones o expresiones regulares (p.ej. URLs, nombres, correos).
- Puedes usar _Proximity_ y _Context_ para ver líneas alrededor de la coincidencia.

### 8) Hash lookup

- Autopsy compara hashes de archivos contra sets conocidos (NSRL, malware). Marca _known good_ y _known bad_ automáticamente.

### 9) Exportar reportes

- `Generate Report` → seleccionar formato (HTML, Excel, XML) → incluir artefactos/metadata/evidencia.
- Adjunta hashes, descripciones y capturas de evidencia para cadena de custodia.

---

# Herramientas en segundo plano (TSK) — comandos útiles

Autopsy usa TSK internamente; conocer comandos ayuda en diagnóstico:

- `mmls image.dd` — muestra particiones y offsets.
- `fls -r -m / image.dd` — lista archivos y entradas (incluye borrados).
- `icat image.dd inode > file` — extrae archivo por inode.
- `ils image.dd` — lista inodes.
- `istat image.dd inode` — muestra metadata del inode.

**Ejemplo**: listar archivos borrados recursivamente:

```bash
fls -r -m / image.dd | grep deleted
```

---

# Módulos importantes y qué extraen

- **File Type Identification** — clasifica por tipo/extension.
- **Keyword Search** — búsqueda textual/regex.
- **Carving** — recuperación por firmas (JPG, PNG, DOC, ZIP).
- **Web History / Browser Artifacts** — historial, cookies, downloads (Chrome, Firefox, IE).
- **Email Parser** — PST/EML/MSG parsing.
- **Windows Registry Parser** — artefactos de registro (mount points, MRU lists, user activity).
- **Recent Activity** — eventos de sistema y usuarios (prefetch, link files).
- **Hash Sets** — comparación con NSRL u otros sets.
- **Mobile Module** — extracción de artefactos móviles (depende de versión/plug-ins).

---

# Ejemplo de caso real (escenario simplificado)

**Situación:** sospecha de fuga de datos.

1. Imagen del laptop sospechoso.
2. Ingest: Web History, Recent Activity, Keyword Search (palabras clave: “confidencial”, “contrato”), Hash Lookup.
3. Resultados: archivo `contract_final.docx` marcado como `deleted` y archivo en USB reciente.
4. Timeline: el documento fue copiado a `/media/usb` a las 02:12 AM — correlacionado con login de usuario.
5. Reporte: exportar evidencia (hashes + copias) y timeline para presentación al equipo legal.

---

# Buenas prácticas forenses con Autopsy

- **Imagenar primero**: crea imagen forense (E01/RAW) y trabaja sobre la copia.
- **Write-blocker** al examinar discos físicos.
- **Registrar hashes** (MD5/SHA1/SHA256) antes y después del análisis.
- **Documentar** cada paso en el caso (quién hizo qué y cuándo).
- **Mantener la cadena de custodia** para evidencia judicial.
- **Seleccionar módulos con cuidado**: algunos pueden ser intrusivos o costosos en tiempo.

---

# Limitaciones y consideraciones

- **Escala**: para imágenes enormes o análisis masivo en muchas imágenes, puede requerir hardware potente y optimización.
- **Eficacia del carving**: si un archivo está fragmentado, carving básico puede fallar; reconstrucción manual o herramientas avanzadas necesarias.
- **Evidencia en la nube/móviles**: Autopsy integra ciertos módulos, pero a menudo necesitarás herramientas específicas (Mobile/Cloud forensic tools).
- **Actualización de artefactos**: navegadores y formatos cambian; es importante mantener Autopsy y módulos actualizados.

---

# Integraciones útiles

- **Plaso/Log2timeline** → para timelines más ricos.
- **Volatility / Rekall** → análisis de memoria (si tienes volcado RAM).
- **YARA** → búsqueda de firmas de malware.
- **VirusTotal API** → marcar artefactos sospechosos.

---

# Ejercicios prácticos para aprender (sugeridos)

1. Crear un caso en Autopsy y añadir una imagen `.dd` de prueba (pequeña).
2. Ejecutar ingest con: File Type Identification, Carving y Recent Activity.
3. Buscar la palabra “password” con Keyword Search y revisar artefactos relacionados.
4. Generar un reporte HTML y revisarlo.
5. Usar TSK (`fls`, `icat`) para extraer un archivo particular y compararlo con la salida de Autopsy.

---

# Herramientas alternativas y complementos

- **Autopsy + TSK** es excelente para casos locales.
- **FTK / EnCase** — suites comerciales con más integración y soporte.
- **Sleuth Kit CLI** — cuando prefieras scripting y automatización pura.
- **SIFT Workstation** — entorno forense que incluye Autopsy, Plaso, Volatility y más.

---

# Conclusión

**Autopsy** es una plataforma forense gráfica completa, ideal para investigar imágenes de disco, recuperar archivos, construir timelines, y preparar informes técnicos y legales. Es accesible para principiantes (flujo guiado) pero también potente para usuarios avanzados (usa TSK y permite automatización). Para máxima eficacia: mantén buenas prácticas forenses, actualiza módulos y complementa con herramientas especializadas (memoria, móvil, cloud) cuando sea necesario.

---

Si quieres, puedo:

- Prepararte un **mini-lab guiado** paso a paso con imágenes de ejemplo y comandos (crear caso, añadir imagen, extraer un archivo borrado).
- Proporcionarte **capturas de pantalla simuladas** y un **checklist forense** imprimible para usar con Autopsy en la práctica.
- Darte **ejemplos de scripts TSK/Autopsy** para automatizar tareas rutinarias.

¿Qué prefieres que haga ahora?

---

[🔼](#índice)

---

## **439. Análisis de disco con Autopsy**

### 1) Preparación y buenas prácticas forenses (imperativo)

Antes de tocar cualquier medio:

- **Obtener autorización legal**. Sin permiso, el análisis puede ser ilegal.
- **Trabajar sobre copias**: NUNCA trabajes directamente sobre el disco original; crea una imagen bit-a-bit.
- **Usar write-blocker** si vas a leer directamente desde el disco físico.
- **Registrar la cadena de custodia**: quién, cuándo, por qué; anotar identificadores físicos, hashes y acciones.
- **Verificar integridad**: calcular MD5/SHA1/SHA256 del original y de la imagen.
- **Etiquetar y documentar** cada paso (capturas, logs).

### 2) Herramientas necesarias (ejemplos)

- **Autopsy** (GUI).
- **The Sleuth Kit (TSK)**: `mmls`, `fls`, `icat`, `istat`, `ils`, etc.
- **ddrescue / dc3dd / dd** para crear imágenes.
- **md5sum / sha1sum / sha256sum** para hashes.
- Opcionales: **Plaso (log2timeline/psort)** (timeline), **Volatility** (memoria), **Wireshark** (red).

Ejemplo instalación (Ubuntu):

```bash
sudo apt update
sudo apt install sleuthkit autopsy libewf-tools plaso
```

(ajusta según distribución y versión).

### 3) Crear una imagen forense (paso clave)

Ejemplo usando `ddrescue` (resiliente a errores):

```bash
# identificar disco (ej. /dev/sdb)
lsblk

# crear imagen
sudo ddrescue -n /dev/sdb /mnt/evidence/disk_image.dd /mnt/evidence/ddrescue.log

# calcular hashes
md5sum /mnt/evidence/disk_image.dd > /mnt/evidence/disk_image.md5
sha1sum /mnt/evidence/disk_image.dd > /mnt/evidence/disk_image.sha1
```

Salida ejemplo (hash):

```
d41d8cd98f00b204e9800998ecf8427e  /mnt/evidence/disk_image.dd
```

Documenta estos valores en la cadena de custodia.

### 4) Inspeccionar particiones / offsets (usar TSK)

Averigua particiones y offsets con `mmls`:

```bash
mmls /mnt/evidence/disk_image.dd
```

Salida ejemplo simplificada:

```
DOS Partition Table
Offset Sector: 0
Slot  Start      End        Length     Description
  1  63          206847     206785     NTFS
  2  206848      976773119 976566272  Linux LVM
```

Si necesitas montar una partición para inspección rápida (solo lectura):

```bash
# calcular offset = StartSector * 512 (si sector=512 bytes)
sudo mount -o ro,loop,offset=$((206848*512)) /mnt/evidence/disk_image.dd /mnt/mountpoint
```

Pero la práctica forense recomienda trabajar con Autopsy/TSK en vez de montar y alterar timestamps.

### 5) Crear caso en Autopsy y agregar la imagen

1. Abrir Autopsy → **New Case** → completar metadatos (investigador, caso).
2. **Add Data Source** → seleccionar `Disk Image or VM file` → elegir `disk_image.dd`.
3. Seleccionar _Ingest Modules_ a ejecutar (explico abajo cuáles y por qué).
4. Iniciar ingest; Autopsy procesará y generará artefactos indexados.

**Módulos de ingest recomendados (ejemplo):**

- File Type Identification (clasifica por tipo).
- File Carving (recuperación por firma).
- Web Browsers / Web History.
- Recent Activity (Windows artefacts).
- Keyword Search (si proporcionas lista).
- Hash Lookup (compara contra NSRL, malware or set personalizado).
- Email Parser (si aplica).

> Consejo: para grandes imágenes ejecuta ingest en servidor con recursos suficientes; o ejecuta módulos selectivos (evita todo a la vez si el tiempo es crítico).

### 6) Navegar resultados en Autopsy (qué mirar)

Tras el ingest encontrarás secciones como:

- **File Types** → ver archivos por tipo (imagenes, docs, ejecutables).
- **Extracted Content / Carved Content** → archivos recuperados por carving.
- **Interesting Items** → items marcados por reglas (hash matches, keywords).
- **Web History / Cookies** → actividad de navegador.
- **Recent Activity** → prefetched apps, USB insertions, etc.
- **Hash Lookup** → archivos marcados como conocidos (buenos/maliciosos).
- **Deleted Files** → archivos borrados listados (si TSK pudo recuperarlos).

Ejemplo: seleccionar un archivo JPG borrado → Autopsy muestra preview, metadata, offset, MFT entry (en Windows), status (deleted), y permite **Extract** para salvar una copia.

### 7) Recuperar archivo borrado (ejemplo práctico con Autopsy y TSK)

#### Con Autopsy (GUI)

- En _File Types_ o _Deleted Files_ filtrar `jpg` o `docx`.
- Seleccionar archivo marcado como `deleted` → botón **Extract** → guardar en carpeta de evidencia.

#### Con TSK CLI (paralela y reproducible)

1. Listar archivos (incluyendo borrados) con `fls`:

```bash
fls -r -m / /mnt/evidence/disk_image.dd > files.txt
# buscar archivos borrados
grep 'r/r' files.txt | head
```

Salida ejemplo (formato `fls`):

```
r/r 128:   Documents/secret.docx
d/d 130:   Pictures
r/r 131:   Pictures/photo001.jpg
```

`r/r` significa archivo regular (deleted) — detalles en la manpage de fls.

2. Extraer por inode (o por número mostrado por fls) con `icat`:

```bash
# supongamos inode=131 (o el número que muestra fls)
icat /mnt/evidence/disk_image.dd 131 > recovered_photo001.jpg
```

3. Ver metadata con `istat`:

```bash
istat /mnt/evidence/disk_image.dd 131
```

Salida ejemplo:

```
Inode: 131
Mode: rw-r--r--   UID: 1000   GID: 1000
Size: 204800
Mac times: mtime: 2025-09-15 02:12:10
```

Documenta y calcula hash del archivo extraído:

```bash
sha256sum recovered_photo001.jpg
```

### 8) Timeline y correlación de eventos

Autopsy tiene un módulo de **Timeline** (o puedes integrar Plaso para más poder).

#### Con Plaso (ejemplo CLI)

1. Crear storage de Plaso:

```bash
log2timeline.py plaso.dump /mnt/evidence/disk_image.dd
```

2. Procesar/filter con `psort`:

```bash
psort.py -o L2tcsv -w timeline.csv plaso.dump "date>=2025-09-14 and date<=2025-09-16"
```

`timeline.csv` contendrá eventos ordenados; puedes abrirlo en Excel para análisis.

En Autopsy: ver sección _Timeline_ para ordenar eventos por hora y filtrar por tipo (file create, exec, web visit). Por ejemplo, si el documento `secret.docx` fue copiado a USB a las `02:12`, y el usuario inició sesión a `02:10`, correlacionar ambos sugiere acción humana.

### 9) Carving (recuperación por firma)

Autopsy/TSK puede recuperar archivos que no aparecen en sistema de archivos por su firma (e.g., JPEG, PNG, DOCX, ZIP).

- En Autopsy: activa _Carving_ en ingest; después revisa _Carved_ o _Extracted Content_.
- Con `foremost` o `photorec` (complemento) para carving masivo:

```bash
foremost -t jpg,pdf -i /mnt/evidence/disk_image.dd -o /mnt/evidence/carved_output
```

Nota: archivos fragmentados pueden no recuperar correctamente; carving simple asume archivo contiguo.

### 10) Búsqueda por palabras clave y YARA

- **Keyword Search**: sube una lista (emails, nombres, frases) y ejecuta búsqueda full-text en Autopsy; marca resultados.
- **YARA**: buscar firmas de malware dentro de archivos/imagenes (si Autopsy soporta plugin o integrar YARA externamente).

Ejemplo de búsqueda regex en Autopsy (Keyword Search): `pass(word|phrase)|contraseña` — Autopsy listará matches con contexto.

### 11) Hash sets y detección (NSRL / malwares)

- Cargar conjuntos de hashes (NSRL goodware, o sets de IOCs) permite que Autopsy marque archivos como _known good_ o _known bad_.
- En Autopsy: **Hash Lookup** module; añade tu set y los archivos se comparan automáticamente durante ingest.

### 12) Exportar evidencia y generar reportes

Autopsy permite:

- **Exportar archivos** recuperados (con metadatos y hashes).
- **Generar reportes** en HTML, CSV o Excel con todas las evidencias seleccionadas, incluyendo timestamps y hashes.
- **Incluir capturas** y notas del analista.

Buen formato de entrega: incluir imagen original (o referencia), hashes, procedimiento, screenshots y items exportados.

### 13) Manejar discos grandes o análisis a gran escala

- Usa un **servidor** (más CPU/RAM/SSD) para Autopsy cuando las imágenes son grandes.
- Ejecuta ingest por fases (p. ej. primero File Type + Hash Lookup + Recent Activity; después Carving si necesitas más tiempo).
- Para múltiples imágenes, automatiza con TSK + Plaso scripts en lote y luego importa resultados a Autopsy o a BI para revisión.

### 14) Casos especiales y limitaciones

- **Discos cifrados** (BitLocker, LUKS): sin claves/contraseñas no podrás ver contenido; Autopsy mostrará contenedor cifrado. Necesitas credenciales, volcado de memoria con claves, o recuperación legal.
- **Sistemas con snapshots / deduplicación**: puede requerir pasos adicionales.
- **Archivos fragmentados**: carving puede fallar; reconstrucción manual o herramientas avanzadas necesarias.
- **Evidencia viva (RAM)**: Autopsy no genera volcados de RAM; usa herramientas especializadas para capturar memoria y luego analiza con Volatility + Autopsy si conviertes datos a formatos compatibles.

### 15) Comandos TSK y ejemplos de uso (resumen)

```bash
# ver particiones
mmls disk_image.dd

# listar archivos (incluidos borrados) con rutas
fls -r -m / disk_image.dd > files.txt

# extraer archivo por inode
icat disk_image.dd 131 > file_from_inode.bin

# metadatos de inode
istat disk_image.dd 131

# listar inodes en un directorio (Linux/EXT)
ils disk_image.dd

# comparar dos imágenes (byte a byte)
md5sum disk_image.dd other_image.dd
```

### 16) Ejemplo de flujo completo (resumido)

1. **Identificación**: registrar y fotografiar medio.
2. **Imagen**: `ddrescue /dev/sdb disk_image.dd ddrescue.log`
3. **Hashes**: `md5sum`, `sha1sum`.
4. **Importar a Autopsy**: New Case → Add Data Source → seleccionar `disk_image.dd` → seleccionar módulos.
5. **Ingest**: ejecutar y esperar.
6. **Examinar**: File Types → Deleted → Carving → Web History → Recent Activity.
7. **Recuperar y exportar**: extraer archivos relevantes con evidencia y hashes.
8. **Timeline**: usar Autopsy/Plaso para correlacionar eventos.
9. **Reportar**: generar reporte con pruebas y cadena de custodia.

### 17) Consejos finales y errores comunes

- **No omitas hashes**: verificaciones garantizan integridad.
- **Documenta TODO**: paso a paso; si es para corte, la documentación es crucial.
- **Evita editar la imagen**: trabaja sobre copias de trabajo.
- **Atención a husos horarios**: los timestamps pueden estar en UTC; clarifica en tu informe.
- **Si Autopsy no muestra algo** prueba con TSK CLI (más granular) o con herramientas especializadas (Volatility, Plaso, photorec).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 13**            | **Siguiente 15**          |
| ------------------ | ----------------------- | ------------------------- |
| [🏠](../README.md) | [⏪](./13_13_winhex.md) | [⏩](./13_15_ipconfig.md) |
