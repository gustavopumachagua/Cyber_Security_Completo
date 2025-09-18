| **Inicio**         | **atrás 18**             | **Siguiente 20**     |
| ------------------ | ------------------------ | -------------------- |
| [🏠](../README.md) | [⏪](./13_18_memdump.md) | [⏩](./13_20_ISO.md) |

---

## **Índice**

| Temario                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------- |
| [447. Create Forensic Images with Exterro FTK Imager](#447-create-forensic-images-with-exterro-ftk-imager)             |
| [448. Creación de imágenes de un directorio con FTK Imager](#448-creación-de-imágenes-de-un-directorio-con-ftk-imager) |

# **FTK Imager**

## **447. Create Forensic Images with Exterro FTK Imager**

![FTK Imager](/img/13_Tools_for_Incident_Response_and_Discovery/FTK_Imager.jpg "FTK Imager")

FTK Imager (de Exterro / AccessData) es una herramienta muy usada para **adquirir (imager) copias forenses** bit-a-bit de discos físicos, particiones, volúmenes lógicos y ficheros. Permite crear imágenes en varios formatos (RAW/dd, E01, AD1, AFF, S01, L01), añadir metadatos de caso, fragmentar el archivo de imagen, aplicar compresión y **verificar la integridad** mediante hashes. También puede montar imágenes en modo sólo-lectura para examinar su contenido.

### Antes de empezar — preparación y buenas prácticas

1. **Autorización**: sólo imágeniza equipos que controles o para los que tengas permiso legal.
2. **No tocar el original**: siempre usa un **write-blocker hardware** si conectas el disco a tu estación forense. Si no es posible, documenta y minimiza acciones.
3. **Documentación**: anota quién, cuándo, cómo, identificadores físicos, modelo/serial del disco y captura fotos del estado físico.
4. **Destino con espacio suficiente**: calcula tamaño (al menos igual a la capacidad del disco original si haces RAW).
5. **Medir integridad**: generar hash (MD5/SHA1/SHA256) del original (si es posible) y de la imagen; guarda esos valores con la evidencia.
6. **Entorno aislado**: realizar adquisición desde una máquina forense fiable, con antivirus/monitorizado según política.

### Formatos de imagen que soporta FTK Imager

FTK Imager soporta —entre otros—: **Raw / dd**, **E01 (EnCase Evidence File)**, **S01**, **AFF**, **AD1 (AccessData)** y **L01**.

- **RAW/dd**: copia bit-a-bit sin metadatos.
- **E01**: formato común en forense que permite metadatos, compresión y fragmentación; es muy usado en entornos legales.
- **AD1**: formato nativo de AccessData/FTK para imágenes lógicas.
  Además, FTK Imager puede **montar** (read-only) imágenes E01, RAW, AFF, AD1, S01 y L01 para examinar su contenido.

### Flujo GUI — crear una imagen paso a paso (ejemplo típico)

1. **Conecta el disco** al sistema forense a través de un write-blocker y confirma que el OS lo detecta.
2. **Abrir FTK Imager** (ej. `FTK Imager.exe`) como Administrador.
3. **File → Create Disk Image**.
4. **Select Source**:

   - Choose Physical Drive → seleccionar `\\.\PhysicalDrive0` (u otro) — para imagen física.
   - O Logical Drive → elegir letra de unidad para imagen lógica (útil cuando un volumen está montado/desencriptado).
   - O Image File/Folder/Contents → para crear imagen a partir de archivos.

5. **Image Destination** → Add → elegir **Image Type** (E01, Raw/dd, AD1, etc.).
6. **Metadata de evidencia (si E01/AD1)**:

   - Case#/Evidence#, Examiner, Notes, Custodian. (esto se guarda dentro del E01).

7. **Opciones**:

   - **Compression**: nivel 0–9 (para E01).
   - **Fragment size**: fragmentar en trozos (ej. 2 GB) para facilitar transporte / grabación en medios.
   - **Encryption/password**: (si tu política lo permite y la versión la soporta).
   - **Verify image after creation**: activar para que calcule y compare hashes (MD5/SHA1/SHA256).

8. **Start** → FTK Imager mostrará progreso, velocidad y estimación de tiempo.
9. **Al finalizar**:

   - Guarda el log generado por FTK Imager (incluye hashes y detalles).
   - Anota la(s) suma(s) de verificación resultantes y adjunta al expediente.

> Ejemplo práctico: crear E01 fragmentado en 2 GB con verificación y compresión alta (GUI te presentará las opciones en la ventana de destino).

### Ejemplo con la interfaz de línea de comandos (CLI)

FTK Imager dispone de una versión CLI (`ftkimager.exe`) útil para automatizar adquisiciones. Un ejemplo de uso (ejemplo real usado frecuentemente por forenses):

```text
ftkimager.exe \\.\PhysicalDrive0 E:\EVIDENCE\case123\diskimage --e01 --frag 2G --compress 9 --verify
```

- `\\.\PhysicalDrive0` → origen físico en Windows.
- `E:\EVIDENCE\case123\diskimage` → ruta y nombre base de salida (FTK añadirá extensiones/fragmentos).
- `--e01` → crear formato E01.
- `--frag 2G` → fragmentar en trozos de 2 GB.
- `--compress 9` → compresión máxima.
- `--verify` → calcular y verificar hashes al terminar.

(Este tipo de sintaxis y opciones está documentada en guías y ejemplos del CLI de FTK Imager).

> **Nota**: los parámetros exactos del CLI pueden variar según versión; siempre revisa la ayuda (`ftkimager.exe --help`) o el manual oficial.

### ¿Qué parámetros elegir y por qué? — recomendaciones prácticas

- **Formato**:

  - Usa **E01** si quieres metadatos, compresión y fragmentación (bueno para entrega legal y ahorro de espacio).
  - Usa **RAW/dd** cuando necesites una copia bit-a-bit simple o compatibilidad máxima con otras herramientas.

- **Compresión**: compresión alta reduce espacio pero aumenta CPU y tiempo; en discos dañados puede ser preferible usar menos compresión para evitar lecturas adicionales.
- **Fragmentación**: usa fragmentos (1–4 GB) si vas a grabar en medios o transferir por unidades limitadas; fragmentos muy pequeños añaden overhead.
- **Hashes**: solicita **al menos MD5 y SHA1** (y SHA256 si tu política lo requiere). Verificación obligatoria.
- **Verificación**: activa `verify` para que FTK Imager calcule hash sobre la imagen creada y lo compare (evita errores silenciosos).
- **Captura lógica vs física**: si el volumen está cifrado y desbloqueado, puede ser rápido capturar **imagen lógica** (ficheros/particiones); para integridad forense completa captura **imagen física**.
- **Velocidad**: si la unidad tiene sectores malos, reduce la velocidad o usa `ddrescue`/herramientas tolerantes — FTK Imager intentará leer sectores pero no es la herramienta para rescatar hardware severamente dañado.

### Verificación y chain of custody

- Guarda el **log** que FTK Imager genera (incluye hashes y parámetros de adquisición).
- Calcula y registra **hashes** del archivo de imagen final por separado (por ejemplo `sha256sum image.E01`).
- Adjunta fotos, serial del disco, hora/fecha, nombre del operador y cualquier nota (por ejemplo errores de lectura).

### Montar y examinar la imagen

- FTK Imager permite **montar** una imagen (E01/RDD/AFF/AD1) como unidad en Windows en modo **sólo-lectura** para examinarla con el explorador o otras herramientas. Esto es útil para validar y extraer ficheros sin manipular la imagen fuente.

### Casos especiales y tips

- **Discos grandes (>2 TB)**: asegúrate de que tu destino y sistema de archivos soporten archivos grandes; fragmentar E01 puede ayudar.
- **Medios con sectores dañados**: considera herramientas especializadas (p. ej. `ddrescue`) que toleren errores y permitan reintentos controlados; después importa la imagen parcial a FTK para análisis.
- **Volcado de memoria**: FTK Imager puede capturar RAM en algunas versiones (ver manual); si necesitas volcado de RAM, herramientas como WinPmem/AVML/LiME son alternativas especializadas.
- **Imágenes lógicas (AD1)**: útiles para recopilar sólo ficheros seleccionados o eDiscovery (más ligeras).
- **Cifrado**: si la política requiere cifrado de las imágenes almacenadas, cifra las copias de transporte (por ejemplo con contenedores cifrados) y mantén la imagen original e imagen cifrada, documentadas.

#### Ejemplo práctico completo (resumen rápido — checklist de adquisición)

1. Fotografiar y etiquetar el dispositivo.
2. Conectar vía **hardware write-blocker** a estación forense.
3. Abrir FTK Imager → **Create Disk Image**.
4. Seleccionar **Physical Drive** como fuente.
5. Añadir destino → elegir **E01**, compresión 5–9, fragmento 2 GB. Rellenar metadatos de caso.
6. Activar **Verify** (MD5 + SHA1) y comenzar.
7. Al terminar: guardar log, copiar hashes a informe, cerrar sesión.
8. Montar la imagen en modo read-only para comprobar archivos, o analizar con FTK/EnCase/Autopsy/Volatility según el objetivo.

### Qué hacer si la ejecución falla o hay errores

- Revisa el **log** que FTK Imager generó y anota códigos de error.
- Si hay muchos sectores malos → cambia a una herramienta tolerantede errores (`ddrescue`) y documenta las razones para la desviación de procedimiento estándar.
- Si la unidad no es detectada → comprobarel cable/puerto, bios, y si sigue sin verse prueba conectar a otra estación forense.
- Si necesitas automatizar y repetir imageados en lote usa la **versión CLI** y scripts; recuerda capturar logs por cada ejecución.

### Recursos oficiales y lectura recomendada

- Manual y User Guide de FTK Imager (incluye formatos soportados, opciones y mounting).
- Guías prácticas / blogs con ejemplos paso a paso para CLI y parámetros (`ftkimager.exe \\.\PhysicalDrive0 ... --e01 --frag ... --verify`).

---

[🔼](#índice)

---

## **448. Creación de imágenes de un directorio con FTK Imager**

Imágenes de **directorio** (a veces llamadas _logical images_) consisten en copiar _archivos y metadatos_ desde un conjunto seleccionado (una carpeta, una unidad lógica, o un listado de ficheros) a un único contenedor forense (E01, AD1, RAW, etc.). No incluyen sectores no asignados ni slack space (para eso harías una imagen física/bit-a-bit). FTK Imager es muy usado para crear este tipo de imágenes porque soporta formatos forenses, verificación y metadatos del caso.

Abajo tienes **por qué**, **cuándo** usarlo, pasos GUI y CLI concretos, ejemplos prácticos, recomendaciones de integridad y gestión de errores.

### ¿Por qué crear una imagen de un directorio (vs. imagen física)?

- **Velocidad y tamaño**: copiar sólo los ficheros relevantes es mucho más rápido y requiere menos espacio.
- **E-Discovery / análisis lógico**: cuando sólo importan ficheros (documentos, correos exportados, logs).
- **Preservación de metadatos de fichero**: FTK Imager puede conservar timestamps, atributos NTFS y otra metadata.
- **Entregables legales**: entregar un AD1/E01 con sólo los ficheros solicitados.

**Limitación importante:** NO capturarás datos remanentes en el espacio libre ni información sobre sectores físicos (p. ej. archivos borrados no recuperados de la tabla de archivos). Para eso necesitas imagen física.

### Antes de empezar — preparación y buenas prácticas

1. **Autorización**: tener permiso/documento que autoriza la adquisición.
2. **Trabajar sobre copia cuando sea posible**: si el directorio está en un servidor productivo, copia primero a un volumen forense temporal si puedes; evita alterar producción.
3. **Documentar**: ruta origen, usuario que ejecuta, fecha/hora, hash del origen (si procede).
4. **Destino**: unidad con suficiente espacio y con formato que acepte ficheros grandes.
5. **Permisos**: ejecuta FTK Imager como Administrador para evitar “access denied”.
6. **Registrar**: guarda el log de FTK Imager y calcula hashes de la imagen final (MD5/SHA1/SHA256).
7. **Considerar archivos abiertos/locked**: si archivos están en uso, usa shadow copy o copia offline (VSS) o detén el servicio responsable cuando sea posible.

#### 1) Creación de imagen de un directorio — GUI paso a paso (Windows)

1. **Abrir FTK Imager** como Administrador.
2. Menú: **File → Create Disk Image** (sí, se usa la misma opción para discos y para contenidos/archivos).
3. En la ventana _Create Image_ → clic **Add...** → selecciona **Image Source Type**:

   - Elegir **Contents of a Folder** o **Logical Drive** (según versión puede aparecer exactamente como _Folder_ o _Content_).

4. **Browse** → selecciona la carpeta raíz que quieres imagear (p. ej. `C:\Investigacion\Caso123\Documents`). Pulsa **OK**.
5. **Destination** → clic **Add**:

   - **Image Type**: elige `E01` (recomendado para forense) o `AD1` (Logical Evidence) o `Raw`/`dd` si prefieres.
   - **File Name / Destination Folder**: p. ej. `E:\EVIDENCE\case123\case123_dir.E01`.
   - **Fragment Size** (opcional): p.ej. `2 GB` si necesitas fragmentar.
   - **Compression (E01)**: elegir nivel (0–9).
   - **Hashing**: selecciona MD5 y/o SHA1 y/o SHA256 para que FTK Imager calcule al terminar.
   - **Metadata**: en E01 puedes rellenar campos (Case Number, Examiner, Evidence Number, Notes). Hazlo para la cadena de custodia.

6. Opciones adicionales: **Verify image after creation** (activar).
7. Pulsar **Start**. FTK Imager te mostrará progreso en porcentaje y velocidad.
8. Al finalizar: guarda el **log**, anota las sumas de verificación que FTK Imager muestra y comprueba el archivo E01/AD1 creado.

#### 2) Ejemplo práctico — GUI (imaginemos)

Origen: `C:\Investigacion\Caso123\Documents`
Destino: `E:\EVIDENCE\case123\case123_documents.E01`
Opciones elegidas: E01, fragmento 2GB, compresión 6, hashes MD5+SHA1, verificar.

**Resultado esperado (resumen de log)**:

```
Image created: E:\EVIDENCE\case123\case123_documents.E01
Fragments: case123_documents.E01, case123_documents.E01-001, ...
MD5:  d41d8cd98f00b204e9800998ecf8427e
SHA1: 2fd4e1c67a2d28fced849ee1bb76e7391b93eb12
Verified: OK (computed hashes match)
Notes: Evidence: Case123; Examiner: JPerez
```

#### 3) Creación desde línea de comandos — ejemplos (CLI)

FTK Imager ofrece `ftkimager.exe` (según versión). La sintaxis puede variar entre versiones: **siempre** ejecuta `ftkimager.exe --help` para confirmar.

**Ejemplo (comando ilustrativo — adapta rutas y flags a tu versión):**

```bat
:: Windows CMD (ejecutar como Administrador)
ftkimager.exe "C:\Investigacion\Caso123\Documents" "E:\EVIDENCE\case123\case123_documents" --e01 --fragment 2G --compress 6 --hash md5,sha1 --verify --case "CASE123" --examiner "J. Pérez"
```

- Primer argumento: ruta del directorio a imagear.
- Segundo argumento: ruta/nombre base del fichero de salida (FTK añadirá extensión).
- `--e01` crea E01; `--fragment 2G` fragmenta; `--compress 6` compresión; `--hash` indica algoritmos; `--verify` pide verificación; `--case` y `--examiner` son ejemplos de campos de metadatos (pueden variar según versión).

**Nota:** la sintaxis exacta de los flags depende de la versión del CLI; revisa `ftkimager.exe --help`.

#### 4) Qué formatos elegir y por qué (resumen rápido)

- **E01** — formato forense rico en metadatos, fragmentación y compresión; ideal para cadena de custodia legal.
- **AD1** — formato lógico nativo de AccessData (útil para eDiscovery y conservar estructura de ficheros).
- **RAW / dd** — máxima portabilidad y simplicidad (no contiene metadatos).
  Elige según política, compatibilidad con herramientas posteriores y requisitos legales.

#### 5) Integridad y verificación (imprescindible)

- **FTK Imager puede calcular hashes al crear la imagen**; activa MD5+SHA1 o SHA256 según política.
- **Comprobar**: después de crear la imagen, compara los hashes mostrados en el log con los calculados offline:

  - Windows PowerShell:

    ```powershell
    Get-FileHash E:\EVIDENCE\case123\case123_documents.E01 -Algorithm SHA256
    ```

  - Linux:

    ```bash
    sha256sum case123_documents.E01
    ```

- Guarda ambos valores en tu informe y en el expediente.

#### 6) Qué metadata deberías rellenar al crear E01 (buena práctica)

- Case Number
- Evidence Number / Item Number
- Examiner
- Notes / Description
- Location / Chain-of-custody fields
  Estos datos se almacenan dentro del E01 y facilitan el rastreo.

#### 7) Montar y validar la imagen (post-adquisición)

- FTK Imager permite **mount** de E01/AD1 como volumen en Windows en modo read-only: `File → Image Mounting` (o botón “Mount Image”).
- Monta en modo sólo lectura y navega con el Explorador para validar que los ficheros esperados están presentes.
- No modifiques; extrae copias para análisis (Export).

#### 8) Casos especiales y tips prácticos

##### Archivos "locked" (en uso)

- Si un archivo está abierto por un servicio, la copia directa puede fallar con _access denied_. Opciones:

  - Crear una **Shadow Copy** (VSS) y crear la imagen desde allí; o
  - Parar el servicio temporalmente (si es aceptable); o
  - Copiar el archivo con una herramienta que use VSS (vssadmin / DiskShadow) y después imagear esa copia.

##### Rutas largas / nombres UNC / permisos de red

- Si el directorio está en una ruta UNC (`\\server\share\folder`), copia localmente antes de imagear o asegúrate de que FTK Imager tenga acceso (credenciales).
- Para long paths en Windows (más de 260 caracteres), copia a ruta con nombre corto o usa prefijo `\\?\` si tu versión soporta.

##### Alternate Data Streams (ADS) en NTFS

- FTK Imager, si imagea un directorio/logical, suele capturar ADS como parte de los ficheros; revisa política y extracción de ADS si necesitas preservarlos.

##### Symlinks / Junctions

- Decide si quieres seguir enlaces simbólicos o no; documenta comportamiento. Si seguir symlinks puede duplicar datos, tenlo en cuenta.

#### 9) Exportar y compartir (entrega de evidencia)

- Entrega: imagen E01 (o AD1) + archivo de log + fichero con hashes + documentación (fotos, notas).
- Si envías por red, cifra el transporte (SFTP o contenedor cifrado). Si grabs en USB, considera cifrar la unidad y anotar permisos.

#### 10) Comprobaciones y errores comunes (y cómo resolverlos)

- **Access denied / permission error**: ejecutar FTK Imager como Admin; comprobar permisos NTFS; usar VSS para archivos en uso.
- **Espacio insuficiente**: fragmenta la E01 o usa otra unidad con suficiente espacio.
- **Errores de I/O / bad sectors** (si la fuente está en disco físico con problemas): para lectura resiliente usa `ddrescue` y luego importa resultado a FTK Imager; documenta la desviación del procedimiento normal.
- **Hashes no coinciden al verificar**: no uses la imagen; repite la adquisición; documenta incongruencia y conserva todos los logs.

#### 11) Ejemplo de script (Windows .bat) — automatizar creación E01 de una carpeta

_(Ajusta rutas y la sintaxis de `ftkimager.exe` según tu versión / help output)_

```bat
@echo off
set SRC=C:\Investigacion\Caso123\Documents
set DEST=E:\EVIDENCE\case123\case123_documents
set FTK=C:\Program Files\AccessData\FTK Imager\ftkimager.exe

:: Ejecutar como admin
"%FTK%" "%SRC%" "%DEST%" --e01 --fragment 2G --compress 6 --hash MD5,SHA1 --verify --case "CASE123" --examiner "JPerez" > "%DEST%_ftk_log.txt"

:: Calcular hash adicional con PowerShell (SHA256)
powershell -Command "Get-FileHash '%DEST%.E01' -Algorithm SHA256 | Format-List" >> "%DEST%_ftk_log.txt"

echo Done. Log in %DEST%_ftk_log.txt
```

#### 12) Flujo recomendado resumido (checklist rápido)

1. Autorizar y documentar.
2. Conectar destino/crear espacio.
3. Ejecutar FTK Imager (preferible Admin) → Create Disk Image → Contents of Folder.
4. Seleccionar formato (E01/AD1/raw), fragmento, compresión, hashes.
5. Llenar metadatos del caso.
6. Start → esperar y verificar.
7. Guardar logs y hashes; montar read-only para comprobar.
8. Transferir/archivar de forma segura y generar informe.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 18**             | **Siguiente 20**     |
| ------------------ | ------------------------ | -------------------- |
| [🏠](../README.md) | [⏪](./13_18_memdump.md) | [⏩](./13_20_ISO.md) |
