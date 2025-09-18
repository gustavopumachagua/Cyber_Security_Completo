| **Inicio**         | **atrás 15**                   |
| ------------------ | ------------------------------ |
| [🏠](../README.md) | [⏪](./10_15_Pass_the_Hash.md) |

---

## **Índice**

| Temario                                                                                                                                |
| -------------------------------------------------------------------------------------------------------------------------------------- |
| [329. Artículo de OWASP sobre la travesía de rutas](#329-artículo-de-owasp-sobre-la-travesía-de-rutas)                                 |
| [330. Guía de Portswigger sobre la navegación de rutas de archivos](#330-guía-de-portswigger-sobre-la-navegación-de-rutas-de-archivos) |
| [331. Artículo de Acunetix sobre la navegación de directorios](#331-artículo-de-acunetix-sobre-la-navegación-de-directorios)           |

# **Directory Traversal**

## **329. Artículo de OWASP sobre la travesía de rutas**

![Directory Traversal](/img/10_Common_Attacks/Directory_Traversal.jpeg "Directory Traversal")

### 1. Descripción general — ¿qué es el recorrido de ruta (path traversal)?

El **recorrido de ruta** (path traversal, directory traversal) es una vulnerabilidad que permite a un atacante **acceder a archivos o directorios fuera del árbol de ficheros que la aplicación debería permitir**.
Esto ocurre cuando la aplicación construye rutas de archivo usando datos controlados por el usuario sin normalizarlos ni validar su contenido.

Efectos típicos:

- Lectura de archivos sensibles (`/etc/passwd`, archivos de configuración, claves, backups).
- Descarga de ficheros privados.
- Inclusión de archivos locales (LFI) que pueden llevar a ejecución remota (RFI/LFI → RCE) en algunos contextos.
- Revelación de estructura interna del servidor.

### 2. ¿Cómo surge técnicamente?

La app acepta un parámetro de archivo (por ejemplo `GET /download?file=report.pdf`) y concatena ese valor a una ruta base, p. ej. `base = "/var/www/files/"; path = base + file`. Si `file = "../../etc/passwd"`, la concatenación produce `/var/www/files/../../etc/passwd` que normalizada es `/etc/passwd` → acceso no autorizado.

Causas comunes:

- Concatenación de rutas sin canonicalización.
- Falta de whitelist/validación de nombres de fichero.
- Permisos de sistema de archivos demasiado laxos.
- Uso de APIs que aceptan rutas relativas sin restricciones.

### 3. Actividades de seguridad relacionadas

- **Revisión de código (SAST)**: buscar concatenaciones de rutas, usos de `open()`, `file_get_contents()`, `fs.readFile()` con input del usuario.
- **Pruebas dinámicas (DAST) / pentesting autorizado**: fuzzing de parámetros de fichero, probar encoding y traversal patterns.
- **WAF tuning**: detectar y bloquear patrones obvios (`../`, `..%2f`, `..\\`, etc.).
- **Revisión de permisos FS**: minimizar archivos accesibles por el proceso web.
- **Logs & monitoring**: alertas por accesos repetidos a archivos fuera del directorio permitido.
- **Code review checklist**: enforce uso de APIs seguras, canonicalización y validación por whitelist.

Herramientas comunes para pruebas defensivas: OWASP ZAP, Burp Suite (modo intruder/fuzzer), Nikto, scanners SCA y SAST. (Usar solo con autorización.)

### 4. Ejemplos — código vulnerable y correcciones

#### Ejemplo (Node.js — vulnerable)

```js
// VULNERABLE: concatena input del usuario
app.get("/download", (req, res) => {
  const base = "/var/www/files/";
  const file = req.query.file; // controllable por el usuario
  const fullPath = base + file;
  res.sendFile(fullPath); // puede enviar /etc/passwd si file contiene ../..
});
```

#### Corrección (Node.js — canonicalización + whitelist)

```js
const path = require("path");
app.get("/download", (req, res) => {
  const base = "/var/www/files/";
  const file = req.query.file;
  // No permitir null/empty
  if (!file) return res.status(400).send("file required");

  // Normalizar y resolver
  const resolved = path.resolve(base, file);

  // Comprobar que la ruta resuelta empieza por base (evita traversal)
  if (!resolved.startsWith(path.resolve(base) + path.sep)) {
    return res.status(403).send("Access denied");
  }

  // (Opcional) comprobar whitelist de nombres de fichero permitidos
  res.sendFile(resolved);
});
```

#### Ejemplo (PHP — vulnerable)

```php
// VULNERABLE
$base = '/var/www/files/';
$file = $_GET['file'];
readfile($base . $file); // vulnerable a ../../
```

#### Corrección (PHP — canonicalización + whitelist)

```php
$base = '/var/www/files/';
$file = $_GET['file'] ?? '';
$realBase = realpath($base);
$realPath = realpath($realBase . DIRECTORY_SEPARATOR . $file);
if ($realPath === false || strpos($realPath, $realBase) !== 0) {
    http_response_code(403); echo "Access denied"; exit;
}
// Opcional: validar que $file está en una lista permitida
readfile($realPath);
```

### 5. Cómo evitar vulnerabilidades de recorrido de ruta — prácticas recomendadas

#### A. Validación por **whitelist**

- Mantén una lista de ficheros permitidos (IDs o nombres cerrados). En vez de `?file=foo.txt` usa `?id=123` y resuelve `id -> filename` en servidor.

#### B. Normalizar y canonicalizar rutas

- Usa `realpath()` (PHP), `path.resolve()` (Node), `CanonicalizeFileName` o la API de tu lenguaje para resolver la ruta absoluta antes de usarla.
- Luego verifica que la ruta resuelta **esté dentro** del directorio base permitido.

#### C. Evitar concatenaciones manuales

- Usa funciones/APIs que gestionen rutas de forma segura y evitan haywire joins.

#### D. Minimizar superficie con permisos FS

- El proceso que atiende la web sólo debe tener acceso a la carpeta necesaria; no corras con permisos root.
- Archivos sensibles (config, keys) fuera del documento root y con permisos que no permita lectura por el usuario web.

#### E. Validar y sanear nombres

- Rechaza caracteres peligrosos (`../`, `\0`, `:`, etc.).
- Normaliza codificaciones (URL-encoding, UTF-8) antes de validar — los atacantes prueban encoded forms.

#### F. Evitar permitir rutas absolutas

- Rechaza valores que empiecen por `/` (Unix) o letra+`:\` (Windows) si la app no las necesita.

#### G. Limitar tipos y extensiones

- Solo servir tipos y extensiones esperadas (.pdf, .png) y validar MIME si procede.

#### H. Usar controles adicionales

- WAF con reglas específicas (pero como CAPA adicional, no única defensa).
- Logging y alertas: acceso a rutas fuera del base debe disparar alerta.

### 6. Detección y pruebas (defensivo)

- **DAST / fuzzing**: inyectar cadenas de traversal en parámetros sensibles (`../etc/passwd`, `..%2f..%2fetc%2fpasswd`, `..\\..\\windows\\system32\\drivers\\etc\\hosts`), pero **solo en entornos autorizados**.
- **SAST**: buscar patrones de concatenación con IO filesystem y falta de canonicalización.
- **Monitoreo**: alertar cuando hay `200` a URLs que deberían devolver 403 o cuando se accede a muchos ficheros en secuencia por la misma IP.
- **WAF logs**: revisar y ajustar falsos positivos.
- **Unit tests**: incluir tests que traten entradas con `..`, encoded sequences y rutas absolutas.

### 7. Variaciones de payloads/entradas que suelen usar los atacantes (para pruebas defensivas)

> Útil para configurar pruebas/fuzzing en entorno controlado — no para atacar sistemas externos.

Formas comunes y codificaciones que conviene comprobar:

- `../etc/passwd`
- `..%2fetc%2fpasswd` (URL-encoded `/`)
- `..%252fetc%252fpasswd` (double-encoded)
- `%2e%2e%2f` (encoded `.. /`)
- `..\\..\\Windows\\System32\\drivers\\etc\\hosts` (Windows backslashes)
- `..%5c..%5cWindows%5cSystem32%5cdrivers%5cetc%5chosts` (URL-encoded backslash)
- `/%2e%2e/%2e%2e/etc/passwd` (mixed)
- `....//....//etc/passwd` (evadir filtros simples)
- Variantes UTF-8/UTF-16 encoding y overlong encodings.
- Use of null byte injection in languages that are vulnerable historically (`%00`) — menor relevancia hoy, pero a revisar según plataforma.

Asegúrate de que tu validación **decodifique y normalice** las entradas antes de comparar con whitelist.

### 8. Respuesta ante incidente — si detectas un probable exploit

1. **Aislar la evidencia**: logs, request headers, payloads, IP.
2. **Bloquear la fuente** (temporal) y aplicar rate-limit.
3. **Revisar accesos a archivos** implicados y comprobar si hubo exfiltración.
4. **Corregir el código** (canonicalización + whitelist) y desplegar parche.
5. **Re-auditar** para ver si otros endpoints comparten la falla.
6. **Notificar** según políticas si hay exposición de datos sensibles.

### 9. Checklist rápido para desarrolladores

- [ ] ¿Usas whitelist (IDs) en lugar de nombres de fichero expuestos?
- [ ] ¿Canonicalizas rutas antes de usarlas? (`realpath`, `path.resolve`.)
- [ ] ¿Compruebas que la ruta resuelta empieza por el directorio base permitido?
- [ ] ¿Restringes extensiones y tipos MIME?
- [ ] ¿Tu proceso web no tiene permisos para leer archivos sensibles?
- [ ] ¿Tienes tests automatizados con inputs de traversal y encoding variations?
- [ ] ¿Tienes logging/alertas para accesos a rutas fuera del scope?

### 10. Recursos y herramientas (para defensa y pruebas autorizadas)

- **SAST tools**: SonarQube, Semgrep (reglas específicas de path traversal).
- **DAST & scanners**: OWASP ZAP, Burp Suite (fuzzer), Nikto — usar en staging o con permiso.
- **WAF**: ModSecurity, reglas OWASP CRS (complementa, no sustituye).
- **Librerías**: usar funciones estándar de path handling del lenguaje (no escribir tu propia normalización).

### 11. Conclusión

El **recorrido de ruta** es una vulnerabilidad frecuente y de alto impacto que suele surgir por decisiones simples (concatenar rutas con input de usuario). La defensa es igualmente simple cuando se aplica de forma sistemática: **normalizar+cercar (canonicalize + jail)**, **validación por whitelist**, **restrict permissions**, y pruebas continuas.

---

[🔼](#índice)

---

## **330. Guía de Portswigger sobre la navegación de rutas de archivos**

### 1) ¿Qué es el recorrido de ruta (path traversal)?

El **recorrido de ruta** (también _directory traversal_ o _path traversal_) es una vulnerabilidad que permite a un atacante acceder a archivos o directorios **fuera** del ámbito que la aplicación debería permitir.
Sucede cuando la aplicación construye rutas de archivos usando entrada controlada por el usuario sin normalizarla ni validarla, por ejemplo:

`/var/www/files/` + `../../etc/passwd` → `/etc/passwd`

El resultado es la **lectura (o a veces inclusión)** de ficheros sensibles del sistema.

### 2) Lectura de archivos arbitrarios mediante recorrido de ruta — ¿cómo se ve?

Conceptualmente, una app ofrece un endpoint para servir ficheros:

```
GET /download?file=report.pdf
```

Si el backend concatena `base + file` sin comprobar, el parámetro `file=../../etc/passwd` devuelve `/etc/passwd`. Esto permite **leer archivos arbitrarios** (configuraciones, claves, tokens, backups).

Ejemplo vulnerable (Node.js — ilustrativo):

```js
// VULNERABLE: no normaliza ni valida
app.get("/download", (req, res) => {
  const base = "/var/www/files/";
  const file = req.query.file; // usuario controla esto
  res.sendFile(base + file); // peligro: ../../etc/passwd
});
```

Ejemplo corregido (Node.js):

```js
const path = require("path");
app.get("/download", (req, res) => {
  const base = path.resolve("/var/www/files/");
  const file = req.query.file;
  if (!file) return res.status(400).send("file required");

  const full = path.resolve(base, file);
  if (!full.startsWith(base + path.sep))
    return res.status(403).send("Access denied");

  res.sendFile(full);
});
```

### 3) Obstáculos comunes que enfrentan los explotadores (y qué significan)

Cuando pruebas defensivamente o haces pentesting autorizado, verás que no siempre es trivial explotar path traversal. Los obstáculos habituales:

1. **Canonicalización / Normalización del servidor**

   - El servidor internaliza `..` o usa `realpath()` y detecta traversal → bloquea.

2. **Filtros y sanitización de entrada**

   - Filtros que eliminan `../`, `/`, `\`, o aplican whitelist reducen vectores simples.

3. **Codificación y double-encoding**

   - Se intentan variantes (`..%2f`, `%2e%2e%2f`, doble codificado) pero el servidor puede decodificar y rechazar.

4. **Null byte protections**

   - Antiguamente `foo%00bar` cortaba cadenas en C; modernas libs/FS suelen evitar esta clase de bypass.

5. **Chroot / jailed process / sandbox**

   - Si el proceso está enjaulado (chroot, container) no puede salir al FS del host.

6. **Permisos del sistema de archivos**

   - Incluso si llegas a la ruta, el proceso puede no tener permiso de lectura sobre archivos sensibles.

7. **Symlinks y enlaces simbólicos controlados**

   - Algunas defensas detectan o previenen el uso de symlinks para escapar del directorio.

8. **Rechazo de rutas absolutas**

   - La app niega valores que empiecen por `/` o `C:\`, evitando rutas absolutas.

9. **WAFs y reglas de IDS**

   - Reglas activas que detectan patrones `../` o accesos repetidos bloquean intentos masivos.

10. **Entrada no expuesta directamente**

- Parámetros pasan por APIs intermedias o ID-mapping (por ejemplo `?id=123`), lo que hace más difícil manipular la ruta.

### 4) Variantes/payloads (útiles para pruebas defensivas en entornos autorizados)

Para pruebas en laboratorio, estas son las variaciones que conviene incluir en tus fuzzers para comprobar robustez (normaliza/decodifica primero en tu detector):

- `../../etc/passwd`
- `..%2f..%2fetc%2fpasswd` (URL-encoded)
- `%2e%2e%2f%2e%2e%2fetc%2fpasswd` (double-encoded)
- `..\\..\\Windows\\System32\\drivers\\etc\\hosts` (Windows backslash)
- `..%5c..%5cWindows%5cSystem32%5cdrivers%5cetc%5chosts`
- `/%2e%2e/%2e%2e/etc/passwd` (mixed)
- `....//....//etc/passwd` (trick para evadir reglas ingenuas)
- Variantes con UTF-8/UTF-16 overlong encodings (dependiendo de la stack).
- Paths con `./` o combos `./../` para test de normalización.

> Importante: usa esta lista **solo** para pruebas en entornos que controles o en laboratorios autorizados.

### 5) Previniendo — recomendaciones prácticas y comprobables

Aplica varias defensas en capas; no confíes sólo en un WAF.

#### A. Validación por **whitelist** (mejor)

- En lugar de exponer nombres arbitrarios, expón identificadores (`?id=123`) y resuelve el id a un fichero conocido en el servidor.
- Si necesitas aceptar nombres, compara contra una lista de ficheros permitidos.

#### B. Canonicalizar y comprobar el path resuelto

- Usa `realpath()` / `path.resolve()` y verifica que la ruta resultante **esté dentro** del directorio base:

  ```js
  const realBase = path.resolve(base);
  const full = path.resolve(realBase, userInput);
  if (!full.startsWith(realBase + path.sep)) deny();
  ```

#### C. Rechazar rutas absolutas y caracteres peligrosos

- Rechaza entradas que comiencen con `/`, `\` o con drive-letter `C:\` si no son necesarias.
- Rechaza null bytes (`\0`) si aplica.

#### D. Limitar extensiones y chequear MIME

- Solo servir archivos con extensiones permitidas (.pdf, .jpg) y validar MIME cuando corresponda.

#### E. Minimizar permisos del proceso

- El proceso web debe correr con un usuario que **solo** pueda leer el directorio de contenido, no configuraciones ni `/etc`.
- Mantén archivos sensibles fuera del document root.

#### F. Usar mapeo estático o almacenamiento fuera de FS

- Servir ficheros desde un almacenamiento controlado (S3, blob) con permisos y URLs firmadas en vez de rutas del FS.

#### G. Logging y alertas

- Loguea intentos que contengan `..`, `%2e%2e`, rutas fuera del base; alerta para revisiones.
- Rate-limit y bloquear IPs con patrones agresivos.

#### H. Pruebas automáticas

- Incluye tests unitarios que pasen payloads de traversal y verifiquen que se devuelven 403/400.
- Integrar fuzzing en CI (ZAP/Burp scripts contra staging).

### 6) Checklist rápido para desarrolladores (práctico)

- [ ] ¿Usas whitelist (IDs) en vez de nombres de ficheros directos?
- [ ] ¿Canonicalizas rutas (`realpath`, `path.resolve`) y comparas con base?
- [ ] ¿Rechazas rutas absolutas y caracteres nulos?
- [ ] ¿Restringes extensiones MIME?
- [ ] ¿Proceso web corre con mínimos permisos y archivos sensibles fuera del root?
- [ ] ¿Tienes tests automatizados con payloads de traversal?
- [ ] ¿Registras intentos sospechosos y configuras alertas?

### 7) Ver todos los laboratorios de recorrido de caminos — recomendaciones (entornos legales y seguros)

Plataformas respetadas con labs para practicar path traversal (todos legales y orientados a aprendizaje):

- **PortSwigger Web Security Academy** — ejercicios interactivos de _Path Traversal_ y variantes (reflected, stored, file read).
- **OWASP Juice Shop** — incluye vulnerabilidades de lectura de archivos y LFI/LFD.
- **WebGoat (OWASP)** — lecciones interactivas sobre path traversal/dir traversal.
- **DVWA (Damn Vulnerable Web App)** — laboratorio clásico con diferentes niveles.
- **TryHackMe** — rooms sobre web exploitation que incluyen path traversal.
- **Hack The Box (HTB)** — máquinas y retos que, en algunos casos, exigen explotación de path traversal como paso inicial (si tienes cuenta y permisos).

> Para practicar, crea entornos locales (Docker), instancias de Juice Shop/WebGoat o usa las plataformas mencionadas. No realices pruebas en sistemas ajenos sin autorización.

### 8) Resumen y mejores prácticas finales

- El **path traversal** permite leer archivos arbitrarios si la app concatena rutas sin normalizar.
- Los atacantes prueban múltiples codificaciones y trucos; por eso la defensa debe **normalizar + restringir** (canonicalize + jail + whitelist).
- Protege también a nivel OS: **principio de menor privilegio**, archivos sensibles fuera del service account y auditoría constante.
- Practica en **labs autorizados** como PortSwigger, OWASP Juice Shop o WebGoat.

---

[🔼](#índice)

---

## **331. Artículo de Acunetix sobre la navegación de directorios**

### ¿Qué es un ataque de recorrido de directorio?

Un **ataque de recorrido de directorio** (también llamado _directory traversal_ o _path traversal_) permite a un atacante forzar a una aplicación web a **leer (o incluso ejecutar) archivos fuera** del directorio que la aplicación debería permitir. Lo típico es aprovechar parámetros de rutas/descargas que concatenan entrada del usuario con una ruta base sin validarla.

El vector clásico es usar `..` (o sus variantes codificadas) para subir carpetas, p. ej. `../../etc/passwd`.

### ¿Qué puede hacer un atacante si tu sitio web es vulnerable?

Dependiendo de permisos y contexto, un atacante podría:

- **Leer archivos sensibles**: `/etc/passwd`, `/etc/shadow` (si permisos lo permiten), `config.php`, `appsettings.json`, archivos con claves/API tokens, backups.
- **Obtener credenciales** u otros secretos (API keys, cadenas de conexión).
- **Exfiltrar datos de usuarios** almacenados en archivos.
- **Local File Inclusion (LFI)**: si la app incluye código del archivo leído, podría llevar a **Remote Code Execution (RCE)** en ciertos escenarios (por ejemplo, incluir logs con payloads, o usar wrappers que permitan ejecución).
- **Mapear la estructura del servidor** y encontrar otros vectores (logs, scripts, uploads).
- **Pivotar** hacia otros servicios si consigue credenciales o archivos de configuración.

### Ejemplo: ataque de recorrido de directorio **a través del código de una aplicación web**

**Escenario vulnerable (Node.js/Express — ilustrativo):**

```js
// VULNERABLE
app.get("/view", (req, res) => {
  const base = "/var/www/files/";
  const file = req.query.name; // controllable by user
  const full = base + file; // concatenación insegura
  fs.readFile(full, "utf8", (err, data) => {
    if (err) return res.status(404).send("Not found");
    res.type("text/plain").send(data);
  });
});
```

Un request: `GET /view?name=../../../../etc/passwd` puede devolver `/etc/passwd`.

**Corrección (Node.js — canonicalizar + whitelist):**

```js
const path = require('path');
app.get('/view', (req, res) => {
  const base = path.resolve('/var/www/files');
  const name = req.query.name || '';
  const target = path.resolve(base, name);             // normaliza .., symlinks relativos
  if (!target.startsWith(base + path.sep)) {           // evita escape fuera del base
    return res.status(403).send('Access denied');
  }
  // opcional: validar extensión o lista de ficheros permitidos
  fs.readFile(target, 'utf8', (err, data) => { ... });
});
```

### Ejemplo: ataque de recorrido de directorio **a través de un servidor web**

Algunos servidores mal configurados o con directorio público (document root) expuesto y **listado de directorios** activado permiten que un atacante navegue y descargue ficheros. Además, si el servidor tiene un endpoint tipo `/download?file=...` que concatena directamente el parámetro, es explotable.

**Comportamiento vulnerable del servidor**:

- Directory listing (índice) activado.
- Document root mal establecido (por ejemplo, incluye `/home/app/` con config).
- Permisos demasiado laxos (proceso web puede leer archivos que no debería).
- Módulos o scripts que permiten incluir archivos por nombre.

**Mitigación a nivel servidor**: desactivar directory listing, restringir document root, aplicar `AllowOverride`/`Require all denied` donde proceda, asegurar `open_basedir` en PHP, etc.

### Cómo comprobar vulnerabilidades de recorrido de directorios (defensivo, autorizado)

1. **Revisión de código (SAST):** busca concatenaciones con API de file I/O (`open`, `readFile`, `file_get_contents`, `sendFile`) que usan parámetros del usuario.
2. **Pruebas manuales en entorno de staging** (solo autorizado):

   - Probar payloads simples: `../../etc/passwd`, `..%2f..%2fetc%2fpasswd`.
   - Probar doble-encoding: `%252e%252e%252f...`.
   - Comprobar variantes Windows: `..\\..\\Windows\\System32\\drivers\\etc\\hosts`.
   - Verificar comportamiento frente a rutas absolutas (`/etc/passwd`) o nulas (`%00`).

3. **DAST / scanners**: herramientas como OWASP ZAP, Burp Suite, Nikto, Acunetix (mencionado por ti) pueden detectar patrones de traversal. Ejecútalos **solo** en entornos con permiso.
4. **Revisar logs**: accesos a ficheros fuera del scope o 200 responses a rutas sensibles.
5. **Tests unitarios**: crear casos de prueba en CI que lancen inputs con `..`, encoded forms y esperen 403/400.
6. **WAF / reglas**: usar WAF para bloquear intentos obvios, pero nunca confiar solo en él.

### Prevención de ataques de recorrido de directorios (prácticas recomendadas)

Aplica varias capas (principle of defense-in-depth):

1. **Whitelist por diseño**

   - Evita exponer nombres de fichero. Usa `?id=123` y resuelve internamente a un path seguro.
   - Si debes aceptar nombres, mantener lista de archivos permitidos.

2. **Canonicalizar y verificar la ruta absoluta**

   - Normaliza (`realpath`, `path.resolve`) la ruta resultante y asegúrate que **comience** por el directorio base permitido (jail).
   - Ejemplo: `if (!resolved.startsWith(base + path.sep)) deny()`.

3. **No concatenar rutas manualmente**

   - Usa funciones de la librería que "juntan" rutas correctamente y luego canonicaliza.

4. **Rechazar rutas absolutas y caracteres peligrosos**

   - Si tu aplicación no necesita rutas absolutas, recházalas. Rechaza null bytes y secuencias no esperadas.

5. **Limitar extensiones y validar MIME**

   - Solo servir tipos esperados (.pdf, .png). Rechazar .php/.config/.env accesibles.

6. **Minimizar permisos del proceso web**

   - El usuario del proceso web debe tener acceso **solo** al directorio de contenido; archivos sensibles fuera del scope y con permisos adecuados.

7. **Desactivar listado de directorios** y limitar exposición del document root.

8. **Usar almacenamiento gestionado** (S3, Blob) con URLs firmadas en lugar de exponer filesystem directo si es posible.

9. **Monitoreo y alertas** sobre requests con `..`, codificaciones sospechosas, o accesos a rutas fuera del base.

10. **Tests automatizados** y fuzzing controlado en CI/staging.

### Comprueba si tu sitio web es vulnerable con Acunetix

Acunetix es un scanner comercial que incluye detección de path traversal. Si tienes licencia y autorización:

- Ejecuta un escaneo en staging o entorno de pruebas.
- Revisa los hallazgos relacionados con _Directory Traversal / Local File Inclusion_.
- Analiza los requests y los payloads usados para replicar el hallazgo y corregirlo en el código.

> Nota: no escanees sistemas de terceros sin permiso; usa Acunetix o herramientas open-source (ZAP/Nikto) en entornos controlados.

### Preguntas frecuentes (FAQ)

**¿Qué es el recorrido de directorios y cómo funciona?**

Es una vulnerabilidad donde la app permite que un parámetro manipule la ruta de un archivo para salir del directorio permitido (usando `..`, codificaciones, symlinks). Funciona cuando la app concatena input del usuario con ruta base sin canonicalizar ni validar.

**¿Cuáles son las posibles consecuencias de un ataque de recorrido de directorios?**

Lectura de archivos sensibles, robo de secretos, escalamiento a RCE (en casos de LFI), exfiltración de datos, mapeo del sistema y pivot hacia otros sistemas.

**¿Cómo detectar vulnerabilidades de recorrido de directorios?**

Revisión de código, fuzzing/DAST en entornos seguros, análisis de logs, tests unitarios con payloads `..`/encoded, scanners como ZAP/Burp/Acunetix. Monitoriza 403/200 y patrones sospechosos.

**¿Cómo defenderse de los ataques de recorrido de directorio?**

Canonicalizar rutas, usar whitelist (IDs), restringir permisos del proceso, validar extensiones, desactivar listing, usar WAF como capa adicional e incluir tests automáticos en CI.

### Checklist rápido (implementable ya)

- [ ] Reemplazar endpoints que aceptan nombres de archivos por endpoints que usan IDs y mapeo interno.
- [ ] Canonicalizar `userInput` con `realpath`/`path.resolve` y verificar que la ruta resuelta esté dentro de `base`.
- [ ] Rechazar rutas absolutas y caracteres nulos.
- [ ] Limitar extensiones permitidas y validar MIME.
- [ ] Ejecutar tests automáticos con payloads de traversal.
- [ ] Configurar proceso web con permisos mínimos; mover configuraciones sensibles fuera del document root.
- [ ] Escanear staging con Acunetix/OWASP ZAP y corregir findings.
- [ ] Loguear e incluir alertas para intentos de traversal.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 15**                   |
| ------------------ | ------------------------------ |
| [🏠](../README.md) | [⏪](./10_15_Pass_the_Hash.md) |
