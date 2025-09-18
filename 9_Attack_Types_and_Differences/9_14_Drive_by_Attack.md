| **Inicio**         | **atrás 13**                         | **Siguiente 15**              |
| ------------------ | ------------------------------------ | ----------------------------- |
| [🏠](../README.md) | [⏪](./9_13_Watering_Hole_Attack.md) | [⏩](./9_15_Typosquatting.md) |

---

## **Índice**

| Temario                                                           |
| ----------------------------------------------------------------- |
| [292. Que es un Drive-By Attack?](#292-que-es-un-drive-by-attack) |
| [293. Drive-By Download attack](#293-drive-by-download-attack)    |

# **Drive-by Attack**

## **292. Que es un Drive-By Attack?**

![Drive-by Attack](/img/9_Attack_Types_and_Differences/Drive_by_Attack.png "Drive-by Attack")

Un **Drive-by Attack** ocurre cuando un usuario visita un sitio web legítimo (pero comprometido) o malicioso y, **sin hacer clic en nada**, su equipo descarga e instala malware de manera automática gracias a vulnerabilidades en el navegador, plugins o el sistema operativo.

👉 La idea es que el usuario “pasa por ahí” (como un auto que pasa por un lugar y recibe un impacto), y ya queda infectado.

### 🔧 Métodos de ataque desde un Drive-by

Los atacantes utilizan distintas técnicas para inyectar malware:

1. **Explotación de vulnerabilidades en navegadores y plugins**

   - Flash, Java, ActiveX, PDF Readers, extensiones inseguras.
   - Ejemplo: entras a una página de noticias, pero esta contiene un **exploit kit** que aprovecha tu navegador desactualizado.

2. **Inyección de código malicioso en sitios legítimos**

   - Hackean un sitio confiable e insertan scripts o iframes invisibles que redirigen a servidores maliciosos.

3. **Publicidad maliciosa (Malvertising)**

   - Anuncios en páginas legales que redirigen a malware.
   - Ejemplo: un banner de “descarga gratis” en un periódico online.

4. **Exploit kits en servidores ocultos**

   - Kits automatizados como Angler o Neutrino que detectan vulnerabilidades en el navegador de la víctima y lanzan el ataque adecuado.

5. **Descargas disfrazadas de actualizaciones**

   - Pop-ups falsos que dicen “actualiza tu Flash/Chrome ahora” cuando en realidad instalan un troyano.

### 🛑 Cómo detectar un ataque Drive-by

Detectarlos en tiempo real es difícil porque son **silenciosos**, pero hay señales:

- **Tráfico anómalo**: conexiones a dominios desconocidos después de visitar un sitio.
- **Cambios en el navegador**: nuevas extensiones que no instalaste, páginas de inicio modificadas.
- **Procesos extraños** en el sistema tras navegar.
- **Alertas de antivirus/EDR** por intentos de exploit en el navegador.
- **Logs de proxy/firewall** con redirecciones sospechosas.
- **Alta CPU o ralentización** justo después de visitar un sitio web.

### 🛡️ Cómo prevenir los ataques Drive-by

#### 🔹 A nivel personal

- ✅ Mantener **navegador y plugins actualizados**.
- ✅ Usar navegadores con **sandbox** (Chrome, Edge, Firefox).
- ✅ Instalar **bloqueadores de scripts/anuncios** (uBlock Origin, NoScript).
- ✅ Evitar descargar software de ventanas emergentes o enlaces dudosos.
- ✅ Activar **antivirus con protección web en tiempo real**.
- ✅ Usar **cuentas sin privilegios de administrador** para navegar.

#### 🔹 A nivel empresarial

- 🔒 Implementar un **Secure Web Gateway / Proxy** con inspección de tráfico.
- 🔒 Desplegar **EDR/XDR** con protección contra exploits.
- 🔒 Mantener parches de sistemas y navegadores al día.
- 🔒 Aplicar **lista blanca de dominios** en redes críticas.
- 🔒 Usar **segmentación de red** para limitar impacto en caso de infección.
- 🔒 Capacitar a empleados en higiene digital (no confiar en “actualizaciones” vía pop-up).
- 🔒 Monitorear con un **SIEM** los eventos y correlaciones de tráfico web.

### ✅ Conclusión

Un **Drive-by Attack** es peligroso porque **no requiere interacción del usuario**: basta con visitar un sitio comprometido para infectarse.
Para **detectar** estos ataques hay que vigilar el tráfico, procesos sospechosos y usar defensas avanzadas (EDR, SIEM).
La **prevención** depende de **actualizaciones constantes**, **navegación segura** y **controles en red** para bloquear exploits antes de que lleguen al usuario.

---

[🔼](#índice)

---

## **293. Drive-By Download attack**

### 🚗💻 ¿Qué es un _Drive-By Download Attack_?

Un **Drive-By Download Attack** (ataque por descarga invisible) es un tipo de ciberataque en el que el simple hecho de **visitar una página web comprometida o maliciosa** provoca que se descargue y ejecute malware en el dispositivo de la víctima **sin que esta haga clic ni acepte nada**.

👉 Es como “pasar en coche junto a una trampa y caer en ella sin detenerse”: basta con estar expuesto un instante para resultar infectado.

### ⚙️ ¿Cómo funciona un ataque Drive-By Download?

1. **Selección del objetivo**

   - El atacante elige un sitio web popular o relacionado con la víctima (por ejemplo, un portal de noticias, un foro o un sitio de proveedores).

2. **Compromiso del sitio web**

   - El atacante inyecta código malicioso (JavaScript, iframes ocultos, exploit kits).

3. **Explotación automática**

   - Cuando el usuario visita la página, el navegador analiza el contenido.
   - Si detecta una vulnerabilidad (en el navegador, plugins o sistema operativo), la explota automáticamente.

4. **Descarga e instalación**

   - Se descarga el malware (troyanos, ransomware, spyware, keyloggers) sin que el usuario lo note.

5. **Persistencia y ataque final**

   - El malware roba información, instala backdoors o cifra los archivos de la víctima.

### 🔎 Ejemplos de Drive-By Download

#### 📌 Ejemplo 1: Sitio de noticias comprometido

Un diario digital legítimo es hackeado.

- El atacante inserta un script que redirige a un servidor malicioso.
- Los visitantes con navegadores desactualizados descargan sin saberlo un **troyano bancario**.

#### 📌 Ejemplo 2: Publicidad maliciosa (Malvertising)

Un banner de “oferta exclusiva” en un portal confiable contiene un exploit.

- Sin hacer clic, al cargarse el anuncio se ejecuta código que instala spyware.

#### 📌 Ejemplo 3: Falsa actualización

Un pop-up en un sitio comprometido muestra: “⚠️ Actualiza tu Flash Player ahora”.

- La víctima acepta, creyendo que es real, pero en realidad descarga un **ransomware**.

#### 📌 Ejemplo 4: Wi-Fi pública

Un atacante manipula el portal cautivo de una red Wi-Fi gratuita (como la de un aeropuerto).

- Cuando la víctima abre el navegador, se descarga un archivo infectado automáticamente.

### 🎯 ¿Por qué son peligrosos los ataques Drive-By Download?

- **No requieren interacción**: basta visitar un sitio.
- **Explotan vulnerabilidades desconocidas** (Zero-Day).
- **Difíciles de detectar**: parecen navegación normal.
- **Escalables**: un solo sitio infectado puede afectar a miles de usuarios.

### 🛑 Cómo detectar un Drive-By Download

1. **Comportamiento extraño en el sistema**

   - Procesos desconocidos tras navegar.
   - Consumo elevado de CPU/memoria de repente.

2. **Alertas en seguridad**

   - Antivirus o EDR detectan intentos de exploit en el navegador.
   - IDS/IPS detectan tráfico hacia servidores de comando y control (C2).

3. **Logs sospechosos**

   - Proxy/firewall registran redirecciones a dominios poco comunes.
   - Eventos de ejecución de binarios descargados sin interacción del usuario.

### 🛡️ Cómo prevenir un ataque Drive-By Download

#### 🔹 A nivel usuario

- ✅ Mantener navegadores y plugins siempre **actualizados**.
- ✅ Usar bloqueadores de scripts y anuncios (**uBlock Origin, NoScript**).
- ✅ Activar la **navegación segura** de Google Chrome / Edge.
- ✅ Usar **antivirus con protección web**.
- ✅ No descargar “actualizaciones” desde pop-ups en páginas web.
- ✅ Navegar con **cuentas sin permisos de administrador**.

#### 🔹 A nivel empresarial

- 🔒 Implementar **Secure Web Gateway** con análisis de tráfico.
- 🔒 Desplegar **EDR/XDR** para detectar comportamientos anómalos.
- 🔒 Monitorear logs con un **SIEM** para correlacionar eventos.
- 🔒 Aplicar **parches regulares** en navegadores, sistemas y software.
- 🔒 Usar **segmentación de red** para contener infecciones.
- 🔒 Realizar **simulaciones de incidentes** para entrenar a empleados.

### ✅ Conclusión

Un **Drive-By Download Attack** es un ataque silencioso y muy efectivo porque aprovecha la **confianza en sitios legítimos** y la **falta de actualización de los usuarios**.

La defensa requiere una combinación de:

- **Buenas prácticas del usuario** (actualizaciones, bloqueo de scripts, antivirus).
- **Medidas empresariales avanzadas** (EDR, SIEM, proxies seguros, segmentación).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 13**                         | **Siguiente 15**              |
| ------------------ | ------------------------------------ | ----------------------------- |
| [🏠](../README.md) | [⏪](./9_13_Watering_Hole_Attack.md) | [⏩](./9_15_Typosquatting.md) |
