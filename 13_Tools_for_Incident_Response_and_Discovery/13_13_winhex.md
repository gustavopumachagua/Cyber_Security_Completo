| **Inicio**         | **atrás 12**             | **Siguiente 14**         |
| ------------------ | ------------------------ | ------------------------ |
| [🏠](../README.md) | [⏪](./13_12_tracert.md) | [⏩](./13_14_autopsy.md) |

---

## **Índice**

| Temario                                    |
| ------------------------------------------ |
| [436. WinHex](#436-winhex)                 |
| [437. ¿Qué es WinHex?](#437-qué-es-winhex) |

# **Winhex**

## **436. WinHex**

**WinHex** es un editor hexadecimal profesional para Windows orientado a edición binaria, recuperación de datos y análisis forense. Permite ver y modificar bytes crudos de archivos, discos y memorias, crear imágenes forenses, extraer datos por firmas (carving), calcular hashes y mucho más. Es una herramienta potente —y delicada— pensada para administradores, forenses, y técnicos en recuperación de datos.

### 1) ¿Qué es WinHex? — definición clara

WinHex es una aplicación de **edición a bajo nivel** que muestra el contenido de un archivo o dispositivo en formato **hexadecimal + ASCII**. Con ella puedes:

- Abrir y examinar _archivos_, _dispositivos de bloque_ (discos físicos, particiones), _imágenes_ (raw), y volcar memorias.
- Modificar bytes (patching), buscar patrones, extraer datos binarios.
- Realizar **imágenes forenses** (copias bit a bit) y calcular/guardar huellas (MD5/SHA1/SHA256).
- Recuperar archivos borrados por reconocimiento de firmas (carving).
- Analizar estructuras como MBR, tablas de particiones, cabeceras de archivos (JPEG/PNG/ZIP), y más.

Aunque WinHex tiene interfaz gráfica (GUI) muy completa, también ofrece opciones de automatización/scripting para procesos repetitivos.

### 2) Principales características (resumidas)

- **Editor hex/ASCII**: edición de bytes en offsets concretos.
- **Soporte de dispositivos**: abrir discos físicos en modo lectura/escritura o sólo lectura.
- **Imaging**: crear copias bit a bit (raw) de discos/particiones.
- **Búsqueda avanzada**: cadenas ASCII/Unicode, hex, expresiones regulares, búsquedas por patrones.
- **Carving**: localizar archivos por firmas (p. ej. JPEG, PNG, DOC) y extraerlos.
- **Análisis de estructuras**: MBR, GPT, FAT, NTFS (ver entradas de directorio, timestamps, etc.).
- **Cálculo de hashes**: MD5, SHA1, SHA256 para verificación.
- **Fill / Insert / Delete / Swap / Endian**: utilidades para manipular datos.
- **Scripting / automatización**: repetir tareas en lotes (según versión).
- **Herramientas para RAID y recuperación** (funciones avanzadas para reconstrucción lógica).

### 3) Casos de uso típicos

- Recuperación de archivos borrados desde una partición (carving).
- Crear imagen forense de un disco para análisis sin tocar el original.
- Analizar cabeceras corruptas (p. ej. identificar tipo de archivo por bytes mágicos).
- Edición puntual en binarios (p. ej. corregir un offset), parcheo de archivos en desarrollo (con cuidado).
- Investigación forense: extraer artefactos de sistemas de archivos y volcar evidencia.
- Revisar sectores de arranque (MBR) y verificar tabla de particiones.
- Inspección de memoria volcada (RAM) para búsqueda de cadenas y artefactos.

### 4) Interfaz y conceptos clave (cómo se ve y qué significan)

Cuando abres algo en WinHex verás típicamente:

```
Offset (hex)   00 01 02 03 04 05 06 07 ...   ASCII column
00000000h      4D 5A 90 00 03 00 00 00 ...   MZ......
00000010h      00 00 00 00 40 00 00 00 ...   ....@...
```

- **Offset**: posición en bytes (en hexadecimal y/o decimal).
- **Columna hex**: cada byte mostrado en hex.
- **Columna ASCII**: representación imprimible (si no imprimible, se ve `.`).
- **Selección**: puedes seleccionar un rango de bytes para copiar/exportar/modificar.
- **Bookmarks / Anotaciones**: marcar offsets importantes para referencia.

### 5) Ejemplos prácticos paso a paso

> **Regla de oro**: **NUNCA** trabajes directamente sobre un disco original sin imagenar primero. Usa siempre una copia o una imagen (o un hardware write-blocker si necesitas leer desde el original).

#### Ejemplo A — Abrir un archivo y editar bytes (uso básico)

**Objetivo:** cambiar la palabra `secret` por `public` dentro de un fichero de texto binario.

1. Abrir el archivo con WinHex (`File → Open` → seleccionar el archivo).
2. Buscar la cadena ASCII `secret` (menú _Search_ → _Find text_).
3. WinHex te mostrará la posición (offset) donde aparece.
4. Selecciona los bytes correspondientes y escribe los nuevos bytes en la vista hex o en la vista ASCII:

   - `secret` → bytes hex: `73 65 63 72 65 74`
   - `public` → bytes hex: `70 75 62 6C 69 63`

5. Guardar con `File → Save As` (preferible crear copia nueva para no perder original).

**Nota:** Si la sustitución cambia la longitud del archivo, debes tener cuidado (si sobrescribes en lugar de insertar, mantén la longitud). Para cambios que alteran tamaños, haz una copia/reestructuración adecuada.

#### Ejemplo B — Crear imagen forense de un disco y verificar hash

**Objetivo:** crear imagen RAW del disco físico y guardar su hash.

1. Con hardware write-blocker conectado (recomendado), abre WinHex y elige `Tools → Open Disk` (o similar) en modo **read-only**.
2. Escoge la opción _Create Disk Image_ (o _Clone Disk to Image_). Selecciona formato RAW (.img) y el destino (archivo grande).
3. Inicia la captura. WinHex copiará sector por sector.
4. Tras finalizar, calcula hashes del original y de la imagen (WinHex permite calcular MD5/SHA1). Anota ambos y compáralos: deben coincidir para garantizar integridad.
5. Trabaja solo con la imagen para análisis y edición.

#### Ejemplo C — Recuperar una foto JPEG por carving

**Objetivo:** encontrar y extraer un JPEG borrado buscando su cabecera y final.

1. Abre la imagen de disco en WinHex.
2. Usa `Search → Find Hex Sequence` e introduce la cabecera JPEG típica:

   - **JPEG**: inicio `FF D8 FF E0` o `FF D8 FF E1` (mucha variedad)
   - También busca `FF D9` como final del JPEG.

3. Localiza el primer `FF D8 ...`. Marca ese offset como **start**.
4. Busca el siguiente `FF D9` después de ese start; marca como **end**.
5. Selecciona el rango start..end y **Export selection to file** → guarda como `.jpg`.
6. Abre el .jpg extraído con un visualizador; si quedó íntegro se visualizará.

> Si el archivo está fragmentado (no contiguo), el carving simple fallará; allí necesitarás herramientas más avanzadas o reconstrucción manual.

#### Ejemplo D — Leer MBR y entender la tabla de particiones

1. Abre el disco físico o la imagen. El MBR está en el primer sector (offset `0x0000` - 512 bytes).
2. Observa bytes al final de ese sector: **55 AA** (signature) en offsets `0x01FE-0x01FF`.
3. Tabla de particiones: empieza en offset `0x01BE` y contiene 4 entradas de 16 bytes cada una.

   - Cada entrada (16 bytes): `[BootFlag(1)] [CHSstart(3)] [Type(1)] [CHSend(3)] [LBAstart(4, little endian)] [NumSectors(4, little endian)]`.

4. Ejemplo de una entrada (hex):

   ```
   80 01 01 00 07 FE FF FF 00 08 00 00 20 0F 00 00
   ```

   - `80` → bootable flag (0x80 = activo).
   - `07` → partition type (0x07 = NTFS/exFAT).
   - `00 08 00 00` → LBA start = 0x00000800 (little endian → sector decimal 2048).
   - `20 0F 00 00` → size in sectors = 0x0F20 = 3872 (ejemplo).

Interpretar estos campos te permite identificar particiones y sus offsets para montarlas o extraer datos.

#### Ejemplo E — Buscar emails/strings y extraer

1. Abre la imagen/archivo.
2. `Search → Find Text` con patrón de correo (ej.: buscar `@example.com` o usar regex si WinHex lo permite).
3. Exporta las líneas/bytes encontradas a un archivo de texto para análisis.

#### Ejemplo F — Inspección RAM o volcado de memoria

WinHex puede abrir volcados de memoria (si tienes el archivo `.raw` de RAM). Entonces puedes:

- Buscar cadenas en claro (contraseñas, URLs, artefactos).
- Extraer estructuras (artefactos del navegador, sesiones).

### 6) Buenas prácticas y precauciones

- **Haz una imagen primero**. Nunca trabajes en el original (salvo lectura con write-blocker).
- **Documenta todo**: quién, cuándo, qué se hizo (registro de acciones) — crítico en forense.
- **Calcula y guarda hashes** (MD5/SHA1/SHA256) antes y después de acciones.
- **Usa modo lectura** cuando sólo inspecciones. Usa escritura con extrema cautela y preferiblemente en copias.
- **Pruebas en laboratorio**: antes de intentar recuperación real, practica en copias y aprende las consecuencias de cambios.
- **Legalidad**: respeta la legislación y políticas — acceder/alterar sistemas sin permiso puede ser delito.

### 7) Limitaciones y cuándo usar otras herramientas

- WinHex es muy potente para edición y análisis binario, pero **para recuperación masiva o análisis automatizado** podrías preferir otras herramientas (Autopsy, Sleuth Kit, PhotoRec, testdisk) o soluciones comerciales de análisis forense como X-Ways Forensics (derivado/ofrece más funcionalidades).
- Para recuperación en RAID complejos o fragmentados, a menudo necesitarás herramientas específicas o conocimientos avanzados de la geometría del RAID.

### 8) Alternativas y herramientas complementarias

- **HxD** — editor hex gratuito y liviano.
- **Bless**, **GHex** — editores hex en Linux.
- **Autopsy / The Sleuth Kit** — análisis forense orientado a discos y sistemas de archivos.
- **FTK Imager / EnCase** — herramientas forenses comerciales.
- **dd / dc3dd / ddrescue** — creación de imágenes en línea de comandos.
- **PhotoRec** — carving de archivos por firmas.

### 9) Recursos para aprender (sugerencias)

- Practica en discos virtuales / VMs.
- Monta un laboratorio con imágenes intencionalmente dañadas para practicar carving y recuperación.
- Lee documentación oficial y manuales forenses que expliquen MBR/GPT, FAT/NTFS y cabeceras de formatos (PNG, JPEG, PDF, ZIP, etc.).
- Cursos/guías de informática forense para entender cadena de custodia, hashing y presentación de evidencia.

### 10) Conclusión

WinHex es una herramienta **muy poderosa** para manipular y analizar datos a nivel de bytes. Es imprescindible en contextos forenses y de recuperación de datos, pero exige disciplina: **siempre trabaja sobre copias o con write-blockers**, documenta todo y respeta las normas legales. Con práctica, WinHex te permite recuperar archivos, investigar artefactos escondidos, y reparar o entender estructuras binarias complejas.

---

[🔼](#índice)

---

## **437. ¿Qué es WinHex?**

### 1) ¿Qué es WinHex?

**WinHex** es un editor hexadecimal y suite de trabajo para Windows orientada a:

- **Edición binaria** (bytes/offsets),
- **Recuperación de datos**,
- **Análisis forense** y
- **Manipulación de discos y archivos a bajo nivel**.

Muestra datos en columnas hex/ASCII, permite abrir archivos, imágenes y dispositivos físicos (discos/particiones), y dispone de herramientas para imaging, carving, hashing, búsqueda avanzada y automatización.

### 2) ¿Cómo puede ayudarme WinHex con la recuperación de datos?

WinHex ayuda en escenarios típicos de recuperación mediante varias funciones:

- **Carving por firmas**: busca cabeceras (por ejemplo `FF D8` para JPEG, `50 4B 03 04` para ZIP) y permite extraer el rango de bytes como archivo.
- **Recuperación de entradas de sistema de archivos**: leer estructuras NTFS/FAT para localizar archivos borrados y reconstruir su contenido si los clusters no fueron reutilizados.
- **Reparación de headers**: permite parchear cabeceras de archivo (p. ej. reconstruir un header JPEG o ZIP) para abrir archivos parcialmente dañados.
- **Análisis sector por sector**: examinar manualmente áreas donde residían datos y extraer manualmente segmentos útiles.
- **Imaging seguro**: generar imágenes bit-a-bit del medio (trabajar sobre la imagen en vez del original).

**Ejemplo de flujo** (recuperar una foto borrada):

1. Crear imagen RAW del disco (modo lectura).
2. Buscar firmas JPEG (`FF D8 .. FF D9`).
3. Exportar el rango encontrado como `.jpg`.
4. Abrir y verificar; si está fragmentado, intentar reconstrucción manual o usar herramientas complementarias.

### 3) ¿Cómo maneja WinHex archivos grandes o particiones de disco?

WinHex está diseñado para trabajar con datos a nivel de disco sin cargar todo en RAM. Técnicas que utiliza:

- **Acceso sectorial**: lee por bloques/sectors, no todo el fichero en memoria.
- **Memoria virtual / mapeo parcial**: mapea porciones para navegación fluida en archivos grandes.
- **Operaciones por rango**: permite seleccionar, exportar o procesar rangos concretos sin cargar el resto.
- **Modo imagen**: trabajar sobre una imagen `.img` permite tratar discos enteros eficientemente.

Resultado: puedes abrir discos de terabytes y examinar sectores concretos, exportar regiones y calcular hashes sin necesidad de RAM equivalente al tamaño del disco.

### 4) ¿Qué hace que WinHex se destaque entre editores hexadecimales?

- **Orientación forense y recuperación**, no sólo edición: herramientas nativas para imaging, hashing, carving y manejo de sistemas de archivos (NTFS/FAT/GPT/MBR).
- **Soporte de dispositivos**: abrir discos físicos y particiones en modo sólo-lectura o lectura/escritura (siempre con precaución o write-blocker).
- **Funciones avanzadas**: análisis de RAID, reparación de estructuras, reconstrucción de archivos fragmentados (en cierta medida), y utilidades de scripting para automatizar.
- **Interfaz y utilidades**: templates para estructuras, bookmarks, vista de estructuras de FS, cálculo de checksums integrado, comparar sectores/archivos, y exportación flexible.
- **Integración con workflows forenses**: generación de informes, registro de acciones y soporte para comprobar integridad (hashes).

### 5) ¿Qué características ofrece WinHex para el análisis forense?

- **Creación y verificación de imágenes forenses** (MD5/SHA hashes).
- **Acceso en modo sólo lectura / uso con write-blockers**.
- **Historial y registro de acciones** (cadena de custodia en la medida que la herramienta lo permite).
- **Análisis de estructuras** (MBR/GPT, FAT, NTFS, entradas de directorio, MFT).
- **Carving automatizado** para recuperar archivos por firma.
- **Comparación binaria**: sector vs imagen, detectar modificaciones.
- **Scripting** para repetir procesos y generar salidas reproducibles.
- **Exportación de evidencia**: extraer archivos, bloques y generar reportes.

> Buenas prácticas forenses: siempre crea la **imagen** del medio y trabaja sobre la copia; registra hashes antes y después; documenta los pasos.

### 6) ¿Se puede utilizar WinHex para recuperar contraseñas?

- **Limitación importante**: WinHex no es una “herramienta de cracking”.
- **Qué sí puede hacer**:

  - Extraer artefactos que **contengan credenciales en claro** (ficheros de configuración, registros, correos, memoria).
  - Extraer **hashes** (por ejemplo, recuperar el fichero SAM/NTDS donde están los hashes) para luego analizarlos con herramientas especializadas (Hashcat, John the Ripper).
  - Volcar memoria RAM donde a veces aparecen contraseñas de sesiones o credenciales temporales (siempre con permiso).

- **Qué no haré explicar**: no voy a dar pasos para romper contraseñas ni instrucciones para evadir autenticación.
- **Ética y legalidad**: recuperar contraseñas de sistemas que no controlas o sin autorización es ilegal. En entornos forenses autorizados se documenta y se procede con cadena de custodia.

### 7) ¿WinHex admite diferentes formatos de archivos?

Sí: WinHex trabaja con:

- **Archivos planos / binarios** (.bin, .img, .raw)
- **Imágenes de disco** (RAW, y en algunas versiones soporte para formatos comunes)
- **Soporte de sistemas de archivos**: NTFS, FAT, exFAT, ext (limitado en GUI), GPT/MBR estructuras.
- **Cabeceras y formatos de archivo**: reconoce signatures (JPEG, PNG, PDF, DOC, ZIP, MP3, etc.) para carving.
- **Volcados de memoria** (raw memory dumps).

No es un "visor" nativo para todos los formatos de archivo interpretados (por ejemplo abrir un .docx como documento estructurado), pero sí puede extraer y mostrar bytes y localizar cabeceras.

### 8) ¿Cuáles son algunas características avanzadas de WinHex?

- **Reconstrucción RAID**: ayudar a montar arreglos lógicos a partir de discos y offsets.
- **Carving avanzado** con firmas customizables.
- **Templates y record browser**: interpretar estructuras de datos y mostrarlas de forma estructurada.
- **Scripting** y ejecución por lotes (automatización de tareas repetitivas).
- **Comparación de imágenes** (byte a byte) y edición de sectores objetivos.
- **Soporte para discos virtuales y archivos grandes** (no cargar todo en RAM).
- **Herramientas de análisis de cadenas, timestamps, codificaciones**.

### 9) ¿Cómo gestiona WinHex la edición de discos?

- Permite **abrir un disco físico** (p. ej. `\\.\PhysicalDrive0`) y ver sectores.
- **Modo sólo-lectura**: opción crucial para análisis forense; evita modificaciones accidentales.
- Si escribes, debes tomar precauciones: **usar imágenes** o **hardware write-blocker**.
- Las operaciones de escritura pueden hacerse a nivel de bloque, con opciones para insertar, borrar o reemplazar bytes/sectors — siempre con posibilidad de daños o pérdida de datos si no se hace con cuidado.

**Ejemplo de uso seguro**:

1. Conectar write-blocker.
2. Crear imagen RAW con WinHex (Tools → Create Disk Image).
3. Cerrar disco y trabajar solo sobre la imagen.

### 10) ¿Puede WinHex ayudar a investigar evidencia digital?

Sí. Funciones clave para investigación:

- **Localizar artefactos** (emails, entradas de navegador, registros de chat, imágenes).
- **Extraer archivos** y metadatos (timestamps, atributos).
- **Recuperar archivos borrados** y reconstruir sesiones a partir de volcado de RAM y archivos temporales.
- **Generar informes**: exportar hallazgos, hashes y segmentos relevantes.
- **Análisis combinado**: usar WinHex para encontrar datos y exportarlos a suites forenses (Autopsy, EnCase) para análisis más profundo.

### 11) ¿Cómo gestiona WinHex la creación de imágenes de disco?

- Tiene función clara para **clonar disco a imagen** (sector por sector).
- Permite opciones: tamaño de bloque, verificación mediante hashing durante la operación, y registro de la operación.
- Al finalizar puede calcular MD5/SHA hashes del original y de la imagen para verificar integridad.
- Opciones avanzadas: compresión de la imagen (dependiendo de versión), y verificación en tiempo real.

**Ejemplo de proceso**:

1. Tools → Create Disk Image → seleccionar disco origen → elegir destino y opciones → iniciar.
2. Al terminar: generar MD5/SHA y guardar informe.

### 12) ¿Puede WinHex ayudar a recuperar datos de medios dañados o corruptos?

Sí, hasta cierto punto:

- **Sectores dañados**: WinHex puede saltar sectores defectuosos, leer los que queden accesibles y permitir guardar lo recuperable.
- **Errores de sistema de archivos**: extraer datos a partir de estructuras parcialmente dañadas.
- **Carving**: recuperar objetos por firmas incluso si la tabla de archivos está dañada.
- **Limitaciones**: si el disco tiene daño físico severo (head, placa electrónica), WinHex no “repara” hardware: en esos casos se requiere laboratorio especializado (recuperación física). Para daño lógico y sectores parcialmente legibles, WinHex funciona bien en manos expertas.

### 13) ¿WinHex admite scripts para automatización?

Sí. WinHex soporta:

- **Scripting / macros**: ejecutar tareas automatizadas (buscar firmas, extraer, calcular hashes).
- **Batch mode / línea de comandos**: versiones comerciales admiten ejecución desde línea de comandos para procesos repetidos.

Esto facilita procesar múltiples imágenes con el mismo flujo (por ejemplo: abrir imagen → calcular hash → buscar firmas → exportar resultados).

### 14) ¿Cómo se puede utilizar WinHex en el desarrollo de software?

- **Patching binario**: corregir bytes en binarios o recursos.
- **Inspección**: entender formatos binarios y ver offsets exactos.
- **Pruebas**: crear archivos corruptos a propósito para testing de robustez (fuzzing básico).
- **Generar fixtures**: extraer fragmentos binarios para pruebas unitarias.

> Cuidado: parchear ejecutables puede invalidar firmas y contratos; usar en entornos controlados.

### 15) ¿Cómo soporta WinHex la creación de datos?

- **Generación de archivos con patrones**: rellenar regiones con valores específicos (`00`, `FF`), crear archivos de tamaño X (útil para pruebas de sistemas).
- **Insertar/llenar**: herramientas para llenar sectores con un patrón, insertar datos en offsets concretos y crear estructuras fake para pruebas.

**Ejemplo**:

- Crear archivo de 100 MB de ceros: `Create new file` → Fill with `00` → size 100 MB.

### 16) ¿Se puede utilizar WinHex para analizar y editar la memoria de acceso aleatorio (RAM)?

Sí:

- Si dispones de un **volcado de RAM** (memory dump), WinHex lo abre y permite búsqueda y extracción de cadenas, procesos, credenciales en memoria, artefactos en texto claro y estructuras.
- WinHex **no** es una herramienta para crear volcados de memoria por sí mismo (se requiere un dumper como `DumpIt`, `WinPMem` u otras utilidades); pero una vez tienes el volcado, WinHex es muy útil para analizarlo.
- En investigación forense, el análisis de RAM puede revelar credenciales, claves en memoria, sesiones, y artefactos transitorios.

### 17) ¿Se puede utilizar WinHex para editar y modificar archivos ejecutables?

Sí, pero con consideraciones:

- **Edición HEX**: puedes abrir un `.exe` y modificar bytes concretos (patching). Útil para pruebas de compatibilidad o corrección puntual.
- **Riesgos**:

  - Modificar ejecutables puede invalidar firmas digitales (Windows Authenticode) y romper dependencias.
  - Cambiar offsets, tablas de importación o checksum puede inutilizar el binario.

- **Uso legítimo**: debugging, corregir headers o recursos en desarrollo, análisis de malware en laboratorio (solo en entornos controlados).
- **Alternativas**: para tareas estructurales (recompilar, cambiar imports), herramientas especializadas (IDA Pro, Ghidra, CFF Explorer) son más adecuadas.

### Buenas prácticas y consideraciones éticas/legalidad

- **Trabaja sobre copias**: nunca edites el original sin imagenar.
- **Documenta**: registra pasos, fechas, hashes y herramientas usadas.
- **Cadena de custodia**: en contextos forenses sigue procedimientos.
- **Permiso**: no hagas análisis o recuperación en sistemas que no te pertenecen sin autorización.
- **Formación**: practica en imágenes y VMs antes de manipular evidencias reales.

### Resumen rápido / Cheat-sheet (acciones comunes con ejemplos breves)

- **Crear imagen RAW**: Tools → Create Disk Image → seleccionar disco → destino `.img` → calcular MD5/SHA.
- **Buscar firmas (carving)**: Search → Find Hex Sequence → introducir signature → export selection → guardar como `.jpg/.zip`.
- **Extraer texto**: Search → Find Text / Extract strings → export results.
- **Comparar discos**: Tools → Compare Files / Drives → generar reporte de diferencias.
- **Scripting**: Automation → grabar/ejecutar script para repetir flujo en varias imágenes.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 12**             | **Siguiente 14**         |
| ------------------ | ------------------------ | ------------------------ |
| [🏠](../README.md) | [⏪](./13_12_tracert.md) | [⏩](./13_14_autopsy.md) |
