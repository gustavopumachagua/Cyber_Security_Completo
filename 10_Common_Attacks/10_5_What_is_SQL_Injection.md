| **Inicio**         | **atrás 4**              | **Siguiente 6**     |
| ------------------ | ------------------------ | ------------------- |
| [🏠](../README.md) | [⏪](./10_4_Spoofing.md) | [⏩](./10_6_XSS.md) |

---

## **Índice**

| Temario                                                                              |
| ------------------------------------------------------------------------------------ |
| [306. PortSwigger - Inyección SQL](#306-portswigger---inyección-sql)                 |
| [307. Las inyecciones SQL son aterradoras](#307-las-inyecciones-sql-son-aterradoras) |

# **¿Qué es la inyección SQL?**

## **306. PortSwigger - Inyección SQL**

![inyección SQL](/img/10_Common_Attacks/inyeccion_SQL.webp "inyección SQL")

### ¿Qué es la inyección SQL?

La **inyección SQL (SQLi)** es una vulnerabilidad que ocurre cuando una aplicación construye consultas SQL usando datos que vienen directamente del usuario sin controlarlos correctamente. Eso permite a un atacante **manipular** la consulta que la aplicación envía a la base de datos y, en consecuencia, leer, modificar o borrar datos que no debería, o hacer otras operaciones en la base.

### ¿Cuál es el impacto de la inyección SQL?

Impactos típicos (desde menor a crítico):

- **Divulgación de datos**: obtener registros de otros usuarios (PII, credenciales, etc.).
- **Modificación/eliminación de datos**: alterar precios, borrar transacciones, etc.
- **Bypass de autenticación**: iniciar sesión como otro usuario si el login es vulnerable.
- **Ejecución de operaciones administrativas** en la BD (crear/alterar tablas, ejecutar comandos administrativos).
- **Daño a la disponibilidad / ransomware / pérdida de integridad** en escenarios severos.
- **Consecuencias legales y reputacionales**: multas por incumplimiento y pérdida de confianza.

  Estos riesgos son bien documentados en guías de seguridad y whitepapers sobre web apps.

### Detección de vulnerabilidades de inyección SQL

Métodos defensivos / de testing (en orden de uso típico):

1. **Revisión de código (code review)**: buscar concatenaciones de strings que incorporen entrada del usuario en consultas.
2. **Pruebas manuales guiadas**: revisar formularios, parámetros GET/POST, cabeceras, cookies, campos JSON; comprobar manejo de errores y respuestas inusuales.
3. **Fuzzing / inputs malformados**: enviar valores de prueba que cambien la estructura de la consulta (esto debe hacerse solo en entornos autorizados).
4. **Escaneo automatizado (DAST)**: herramientas como OWASP ZAP o Burp Suite pueden detectar patrones comunes.
5. **Pruebas de caja blanca + SAST**: análisis estático para detectar concatenaciones peligrosas.
6. **Revisión de logs y WAF**: analizar logs para patrones de intento de explotación y ajustar reglas del WAF.

   Para pruebas estructuradas y checklists, revisa la guía de testing de OWASP (WSTG).

### Ejemplos (conceptuales y seguros)

> Nota: muestro **ejemplos de código vulnerables** y **cómo corregirlos**. No doy instrucciones para explotar sistemas reales; los ejemplos sirven para aprender cómo defender.

**Ejemplo vulnerable (Node.js / pseudo-MySQL concatenando strings):**

```js
// VULNERABLE: concatena la entrada del usuario directamente
const query = `SELECT * FROM users WHERE username = '${username}' AND password = '${password}'`;
db.query(query, (err, rows) => { ... });
```

**Problema:** si `username` o `password` contienen sintaxis SQL controlada por un atacante, la lógica de la consulta puede cambiar.

**Versión segura (prepared statements / parámetros):**

```js
// SEGURO: parámetros enlazados (placeholders)
const query = 'SELECT * FROM users WHERE username = ? AND password = ?';
db.query(query, [username, password], (err, rows) => { ... });
```

Lo mismo aplica en Python, PHP, Java, etc.: **usar declaraciones preparadas** o el binding que el driver/ORM provea evita que la entrada del usuario cambie la estructura de la consulta.

### Tipos de inyección SQL (resumen)

- **Error-based (basada en errores):** el atacante provoca errores útiles en la BD para obtener información.
- **Union-based (UNION):** agrega una consulta extra con `UNION SELECT ...` para obtener resultados adicionales combinados con la consulta legítima.
- **Blind SQLi (inyección ciega):** la aplicación no devuelve el contenido de la consulta; el atacante infiere información por respuestas booleanas o por tiempos de respuesta (boolean-based y time-based).
- **Stacked queries (consultas apiladas):** cuando el motor permite múltiples sentencias en una sola petición (no todos lo permiten).
- **Out-of-band:** usar canales alternativos fuera de la respuesta HTTP para exfiltrar datos.

  PortSwigger y OWASP describen estos vectores con detalle y ejercicios.

### Examinando la base de datos — desde la defensa

Cuando se habla de “examinar la base de datos” en contexto defensivo, se refiere a:

- **Auditar privilegios:** comprobar qué cuentas/roles tienen SELECT/INSERT/UPDATE/DELETE/EXECUTE y reducir privilegios innecesarios.
- **Revisar metadatos y exposición:** asegurarse de que vistas o endpoints no expongan información de `information_schema` o catálogos del SGBD públicamente.
- **Logs y trazas:** habilitar y revisar logs de consultas y accesos para detectar patrones anómalos.
- **Pruebas en entornos controlados:** usar entornos de laboratorio para simular enumeración y ver qué información podría exponerse.

  Como regla: **si el atacante puede ver los nombres de tablas/columnas, la defensa falla en el principio de menor privilegio**.

### Ataques de la UNIÓN (concepto)

Un ataque UNION intenta combinar el resultado de la consulta original con otra consulta controlada por el atacante. Para que funcione, el número y tipos de columnas deben coincidir; el objetivo es que la respuesta al cliente incluya filas que el atacante controle, revelando así datos. Es un vector común en pruebas de extracción de datos y por eso las aplicaciones deben **no** devolver fragmentos de la consulta o resultados sin filtrar y deben usar consultas parametrizadas.

### Inyección SQL ciega (concepto)

Cuando la aplicación **no** muestra errores ni resultados del SELECT, el atacante puede aún inferir información:

- **Boolean-based blind:** cambia una condición y observa si la página cambia (true/false).
- **Time-based blind:** provoca operaciones que tardan más si cierta condición es verdadera (obs.: uso en pruebas controladas).

  La mitigación es la misma: parámetros, validación y reducir la superficie de exposición. Para aprender la diferencia y practicar, los labs tipo TryHackMe/PortSwigger tienen ejercicios dedicados.

### Cómo prevenir la inyección SQL (prácticas recomendadas)

Lista priorizada — implementa estas **en este orden**:

1. **Declaraciones preparadas / parámetros (bind variables)** — la defensa más efectiva.
2. **ORMs con queries parametrizadas** — si usas ORM, usa su API para evitar concatenaciones manuales.
3. **Validación de entrada por whitelist** (tipo y formato) — para campos como IDs, emails, fechas.
4. **Principio de menor privilegio** en cuentas de BD (evitar que la app use un super-usuario).
5. **Evitar exposición de errores detallados** en producción; registrar internamente, mostrar mensajes genéricos al usuario.
6. **Escaneo y pruebas regulares** (SAST + DAST + pentesting autorizados).
7. **WAF** y reglas bien configuradas como capa adicional (no sustituye código seguro).
8. **Cifrado en reposo y en tránsito** y políticas de rotación de credenciales.

   OWASP mantiene un cheat-sheet muy completo con detalles y recomendaciones técnicas.

### Hoja de trucos (cheat-sheet rápida)

- Nunca concatenes input en consultas.
- Usa siempre **prepared statements** o bind parameters.
- Valida por **whitelist** (no por blacklist).
- Usa cuentas de BD con **los mínimos permisos necesarios**.
- Revisa y filtra logs para patrones sospechosos.
- Desactiva mensajes de error detallados en producción.
- Ten un proceso de parcheo para el motor de BD y bibliotecas.
- Practica en entornos legales (labs) antes de hacer tests en producción.

### Recursos y laboratorios legales para practicar (recomendados)

Practica siempre en entornos controlados / autorizados. Aquí tienes plataformas y proyectos populares:

- **PortSwigger Web Security Academy — sección SQL Injection y labs interactivos** (varios laboratorios para login bypass, blind SQLi, UNION, etc.).
- **OWASP Juice Shop** — aplicación intencionalmente vulnerable para realizar ejercicios a gran escala (incluye inyección y muchas otras fallas).
- **DVWA (Damn Vulnerable Web Application)** — clásico para practicar SQLi en diferentes niveles de seguridad (ejecutable en VM/local).
- **WebGoat (OWASP)** — lecciones prácticas sobre inyección y otras vulnerabilidades.
- **TryHackMe** — tiene módulos y rooms sobre SQL Injection y ataques de inyección en general.

### Recomendaciones éticas y legales (muy importantes)

- Practica **solo** en entornos que sean tuyos o explícitamente autorizados (labs oficiales, VMs locales, máquinas con permiso).
- No intentes explotar vulnerabilidades en sitios de terceros sin permiso: es ilegal y dañino.
- Usa lo aprendido para **mejorar la seguridad** (hardening, auditorías, educación de equipos).

---

[🔼](#índice)

---

## **307. Las inyecciones SQL son aterradoras**

### Resumen rápido

La **inyección SQL (SQLi)** permite que datos de entrada maliciosos cambien la estructura de una consulta a la base de datos. Eso puede conducir a **exfiltración de datos**, **manipulación** o **borrado** de información, **bypass de autenticación** y, en casos extremos, a compromisos mayores de la infraestructura. Lo que la hace especialmente peligrosa es que suele explotarse sobre errores básicos de programación (concatenación de strings) y puede pasar desapercibida.

### ¿Por qué son tan aterradoras? (puntos clave)

1. **Impacto directo y masivo**: un único fallo puede exponer millones de registros (PII, credenciales, tarjetas, etc.).
2. **Fácil de introducir**: la causa típica es una concatenación de la entrada del usuario en la consulta; es un error común en código legado.
3. **Dificultad para detectarlas**: muchas inyecciones son “ciegas” (no muestran datos en la respuesta), por lo que se infieren mediante cambios de comportamiento.
4. **Automatización y scan masivo**: bots y scanners explotan sitios en masa, por lo que una vulnerabilidad pública se descubre rápido.
5. **Pivot y escalado**: con acceso a la BD un atacante puede obtener credenciales, crear puertas traseras o llegar a la red interna.
6. **Consecuencias no técnicas**: multas por regulaciones, pérdida de clientes, daño reputacional y costes legales/forenses elevados.

### Ejemplos educativos (vulnerable → seguro)

> **Nota:** los ejemplos muestran _cómo se comete el error_ y _cómo corregirlo_. No están pensados para atacar sistemas reales.

#### Node.js (mysql / mysql2)

Vulnerable (concatenación):

```js
// VULNERABLE
const query = `SELECT * FROM users WHERE username = '${username}' AND password = '${password}'`;
db.query(query, (err, rows) => { ... });
```

Seguro (prepared statements / parámetros):

```js
// SEGURO (placeholders)
const sql = 'SELECT * FROM users WHERE username = ? AND password = ?';
db.execute(sql, [username, password], (err, rows) => { ... });
```

#### Python (psycopg2 / PostgreSQL)

Vulnerable (interpolación directa):

```py
# VULNERABLE
cur.execute(f"SELECT * FROM users WHERE id = {user_id}")
```

Seguro (binding de parámetros):

```py
# SEGURO
cur.execute("SELECT * FROM users WHERE id = %s", (user_id,))
```

#### PHP (mysqli)

Vulnerable:

```php
// VULNERABLE
$query = "SELECT * FROM users WHERE email = '" . $_POST['email'] . "'";
$result = $mysqli->query($query);
```

Seguro (prepared statement):

```php
// SEGURO
$stmt = $mysqli->prepare("SELECT * FROM users WHERE email = ?");
$stmt->bind_param("s", $_POST['email']);
$stmt->execute();
```

#### ORM (ej. SQLAlchemy)

Usar el ORM correctamente evita construir SQL manualmente:

```py
# SEGURO: SQLAlchemy
user = session.query(User).filter_by(email=given_email).first()
```

### Tipos importantes y por qué importan

- **Error-based (basada en errores):** la app devuelve mensajes útiles; el atacante aprende estructura y nombres.
- **Union-based:** combina resultados de la consulta legítima con otra consulta controlada por el atacante (exfiltra filas).
- **Blind SQLi (ciega):** la app no muestra datos; el atacante infiere información por cambios booleanos o por tiempos de respuesta. Muy sigilosa.
- **Stacked queries:** ejecutar múltiples sentencias en una sola petición (no todos los motores lo permiten).
- **Out-of-band:** usar otro canal (DNS, HTTP) para extraer datos si la respuesta HTTP no revela nada.

### ¿Cómo se detectan? (desde la defensa)

- **Revisión de código (code review):** buscar concatenaciones con entradas externas, uso de `exec`, interpolaciones peligrosas.
- **SAST (análisis estático):** detectar patrones de riesgo (f-strings, string concatenation en contextos SQL).
- **DAST / pruebas de caja negra autorizadas:** escanear endpoints para respuestas anómalas, errores o cambios en longitud/tiempo.
- **Logs y telemetría:** buscar consultas inusuales, parámetros que provocan errores o picos de latencia.
- **Pentesting controlado:** pruebas autorizadas que intenten enumerar la BD en entornos de staging.

### Cómo prevenir (lista priorizada — implementar en este orden)

1. **Prepared statements / parámetros** (la defensa principal).
2. **ORMs o query builders** que eviten SQL manual cuando sea posible.
3. **Validación por whitelist** (tipos, longitudes, formatos) — p. ej. UUIDs, enteros positivos.
4. **Principio de menor privilegio**: la cuenta que usa la app solo debe tener los permisos necesarios.
5. **Evitar mensajes de error detallados** al usuario; registrar los errores internamente.
6. **Escaneo y pruebas automáticas** en CI (SAST + DAST).
7. **WAF** como capa adicional (no sustituye código seguro).
8. **Auditoría de accesos y rotación de credenciales** periódica.
9. **Revisión de dependencias y parches** del motor de BD y librerías.

### Hoja de trucos rápida (cheat-sheet)

- No concatenes input en SQL.
- Usa siempre parámetros/prepared statements.
- Valida entradas por whitelist.
- Limita privilegios de la cuenta DB.
- Registra queries lentas y atípicas.
- Desactiva la exposición de metadatos sensibles.
- Integra SAST/DAST en el pipeline.
- Practica en entornos controlados (Juice Shop, WebGoat, PortSwigger labs, TryHackMe).

### Ejemplo conceptual de por qué es peligroso (sin dar “payloads”)

Imagina una consulta para comprobar login que incorpora el texto que el usuario envía en lugar de parametrizarlo. Si un atacante consigue que la estructura de la consulta cambie (por ejemplo, con texto que cierre una cadena y añada otra condición lógica), la cláusula `WHERE` podría volverse siempre verdadera y permitir el acceso sin credenciales. Ese simple cambio estructural es lo que hace que la vulnerabilidad sea potente: no necesita bugs complejos del motor, solo la posibilidad de alterar la sintaxis SQL.

### Qué hacer si encuentras una vulnerabilidad (pasos prácticos, defensivos)

1. **No explotar en producción.** Reproducir en un entorno controlado.
2. **Revisar alcance:** qué datos/roles/tables se pudieron ver.
3. **Rotar credenciales** de conexión si hay sospecha de acceso.
4. **Aplicar parche inmediato** (usar prepared statements donde haya concatenación).
5. **Auditoría forense:** revisar logs y conexiones para estimar impacto.
6. **Notificar a equipos legales y de gestión** según políticas.
7. **Desplegar monitoreo adicional** y pentest posterior para confirmar mitigación.

### Recursos y laboratorios (para practicar de forma legal)

- **PortSwigger Web Security Academy** (labs interactivos).
- **OWASP Juice Shop**, **WebGoat**, **DVWA** (máquinas/aplicaciones intencionalmente vulnerables).
- **TryHackMe** (rooms sobre SQLi y websec).
  Practica solo en entornos propios o autorizados.

### Conclusión — ¿Por qué debes tomarlo en serio?

Porque la causa suele ser un error humano sencillo y las consecuencias pueden ser catastróficas (datos expuestos, fraude, multas, reputación). La buena noticia: la prevención es clara y práctica — **parámetros + validación + menos privilegios + testing** — y eso hace que la mayoría de las inyecciones sean evitables si se aplican buenas prácticas desde el desarrollo.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**              | **Siguiente 6**     |
| ------------------ | ------------------------ | ------------------- |
| [🏠](../README.md) | [⏪](./10_4_Spoofing.md) | [⏩](./10_6_XSS.md) |
