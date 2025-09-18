| **Inicio**         | **atrás 22**                 | **Siguiente 24**             |
| ------------------ | ---------------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./12_22_Preparation.md) | [⏩](./12_24_Containment.md) |

---

## **Índice**

| Temario                                                                                                              |
| -------------------------------------------------------------------------------------------------------------------- |
| [398. Cómo identificar vulnerabilidades de ciberseguridad](#398-cómo-identificar-vulnerabilidades-de-ciberseguridad) |
| [399. ¿Qué es un sistema de detección de intrusiones?](#399-qué-es-un-sistema-de-detección-de-intrusiones)           |

# **Identification**

## **398. Cómo identificar vulnerabilidades de ciberseguridad**

![Identification](/img/12_Secure_vs_Unsecure_Protocols/identificar_vulnerabilidades.png "Identification")

Las **vulnerabilidades** son fallos en software, hardware, configuraciones o procesos que pueden ser explotados por atacantes para comprometer un sistema. Identificarlas a tiempo es clave para **prevenir incidentes**.

Se pueden descubrir mediante **técnicas pasivas**, **activas**, **simulaciones humanas** y **evaluaciones integrales**.

### 1️⃣ Seguimiento pasivo de vulnerabilidades

Se trata de **recopilar información sin alterar el sistema**.

Ejemplo:

- Revisar bases de datos públicas como **NVD (National Vulnerability Database)**, **CVE** o **Exploit-DB**.
- Usar **herramientas de gestión de parches** (ej. WSUS, Qualys, Nessus) que alertan sobre versiones obsoletas.
- Monitorizar foros de seguridad o avisos de fabricantes.

👉 Ejemplo práctico:

Encuentras que tu servidor usa **Apache 2.4.49** y ves en el NVD que tiene un **CVE (CVE-2021-41773)** que permite ejecución remota de código. Aunque no lo hayas probado aún, ya sabes que **tu sistema es vulnerable**.

### 2️⃣ Pruebas de vulnerabilidad activas

Aquí sí se **interactúa directamente con los sistemas** para detectar debilidades.

Herramientas comunes:

- **Nmap** → escaneo de puertos y servicios expuestos.
- **Nessus/OpenVAS** → análisis automatizado de vulnerabilidades.
- **Nikto** → pruebas de servidores web.

👉 Ejemplo práctico:

Con **Nmap**, descubres que un servidor expone el puerto 3389 (RDP). Luego, con Nessus, detectas que esa versión de RDP es vulnerable a **BlueKeep (CVE-2019-0708)**. Esto confirma que existe un riesgo explotable.

### 3️⃣ Simulaciones de ingeniería social

Evalúan **el factor humano**, que muchas veces es la mayor vulnerabilidad.

Tipos:

- **Phishing simulado:** correos falsos enviados a empleados para medir si hacen clic en enlaces maliciosos.
- **Vishing:** llamadas telefónicas falsas para obtener información.
- **Tailgating:** intentar entrar físicamente a una oficina sin autorización.

👉 Ejemplo práctico:

Una empresa envía un correo de prueba con asunto “Actualiza tu contraseña aquí”. Si el 40% de empleados cae en la trampa, eso revela una **vulnerabilidad humana** que requiere capacitación.

### 4️⃣ Evaluaciones de seguridad

Son **análisis completos** que combinan pruebas técnicas y de procesos:

- **Pentesting (test de penetración):** simular ataques reales para explotar vulnerabilidades.
- **Auditorías de seguridad:** revisar configuraciones, accesos y cumplimiento de políticas.
- **Revisión de código:** buscar fallos de seguridad en aplicaciones.

👉 Ejemplo práctico:

Un pentester descubre que una aplicación web no valida correctamente las entradas de usuario → permite un **ataque SQL Injection** (`' OR '1'='1`). Esto es una vulnerabilidad crítica.

### 5️⃣ Consejos para realizar pruebas de forma más eficaz

✅ Define un **alcance claro** (qué sistemas probar, en qué horarios).

✅ Usa tanto **herramientas automáticas** como **análisis manual**.

✅ Clasifica las vulnerabilidades por **severidad (CVSS)**.

✅ Documenta hallazgos con **pruebas claras** (capturas, logs, PoC).

✅ Realiza pruebas de manera periódica, no solo una vez al año.

### 6️⃣ Cómo puede ayudar el “efecto de campo”

El **efecto de campo** se refiere a que las vulnerabilidades no existen de forma aislada: su impacto se multiplica según el **contexto del sistema**.

Ejemplo:

- Una contraseña débil en una cuenta de prueba puede parecer menor.
- Pero si esa cuenta tiene acceso a la red interna, un atacante podría **moverse lateralmente** y escalar privilegios.

👉 Esto significa que no basta con mirar vulnerabilidades una por una; hay que entender **cómo interactúan en un ataque real** (cadena de ataque).

### ✅ Resumen

Para identificar vulnerabilidades:

- Haz **seguimiento pasivo** (bases CVE, avisos de fabricantes).
- Realiza **pruebas activas** (escáneres y exploits controlados).
- Considera el **factor humano** (phishing, ingeniería social).
- Ejecuta **evaluaciones de seguridad integrales** (pentesting, auditorías).
- Clasifica y prioriza vulnerabilidades según **riesgo real** (efecto de campo).

---

[🔼](#índice)

---

## **399. ¿Qué es un sistema de detección de intrusiones?**

Un **IDS (Intrusion Detection System)** es una herramienta o sistema de seguridad que **monitorea el tráfico de red o la actividad de los sistemas** para detectar comportamientos sospechosos, ataques conocidos o violaciones de políticas de seguridad.

👉 Piensa en el IDS como una **alarma de seguridad**: no bloquea el ataque (en su versión básica), pero **avisa** cuando detecta algo extraño.

Ejemplo: si alguien intenta hacer un escaneo de puertos contra tu servidor, el IDS puede generar una alerta basada en esa actividad.

### ⚙️ ¿Cómo funcionan los sistemas de detección de intrusiones?

Los IDS funcionan principalmente de dos maneras:

1. **Detección basada en firmas** 📝

   - Compara el tráfico o eventos con una base de datos de patrones conocidos (similar a un antivirus).
   - Ejemplo: detectar un exploit de **SQL Injection** porque coincide con una firma existente (`' OR '1'='1`).
   - Limitación: no detecta ataques nuevos (zero-day).

2. **Detección basada en anomalías** 📊

   - Crea un modelo del tráfico normal en la red y genera alertas cuando detecta algo fuera de lo común.
   - Ejemplo: un usuario que normalmente descarga 10 MB al día y de pronto transfiere 5 GB.
   - Ventaja: puede detectar ataques desconocidos.
   - Desventaja: puede generar **falsos positivos**.

### 🛠️ Tipos de IDS

Según dónde se ubiquen y qué analicen:

1. **NIDS (Network IDS)**

   - Monitorea el tráfico en la red.
   - Ejemplo: **Snort**, **Suricata**.
   - Se coloca en un punto estratégico de la red (ej. entre switch y firewall).

2. **HIDS (Host IDS)**

   - Monitorea la actividad de un host específico (servidor o PC).
   - Ejemplo: **OSSEC**, **Wazuh**.
   - Revisa logs, integridad de archivos, procesos.

3. **Híbridos**

   - Combinan NIDS + HIDS para mayor cobertura.

### 🔐 Tipos de sistemas de prevención de intrusiones (IPS)

Un **IPS (Intrusion Prevention System)** va un paso más allá: no solo detecta, también **bloquea**.

Tipos:

- **IPS basado en red (NIPS):** inspecciona tráfico y bloquea paquetes maliciosos.
- **IPS basado en host (HIPS):** protege un host individual, bloqueando procesos sospechosos.
- **WIPS (Wireless IPS):** detecta y bloquea intrusiones en redes Wi-Fi.

Ejemplo:

- IDS → detecta que alguien intenta explotar una vulnerabilidad en Apache y genera alerta.
- IPS → además de alertar, **bloquea automáticamente el tráfico** del atacante.

### 🕵️‍♂️ Tácticas de evasión de IDS

Los atacantes conocen cómo funcionan los IDS y usan técnicas para **evadir detección**:

- **Fragmentación de paquetes:** dividir ataques en fragmentos pequeños para que no coincidan con una firma.
- **Obfuscación de payloads:** usar codificación (Base64, URL encoding).
- **Tunneling:** ocultar tráfico malicioso dentro de protocolos legítimos (HTTP, DNS).
- **Low-and-slow attacks:** ataques lentos y distribuidos para no generar alertas.
- **Polimorfismo:** malware que cambia de forma constantemente.

Ejemplo: en lugar de lanzar 1000 intentos de login en 1 minuto (que dispara alertas), un atacante prueba 1 intento cada 10 minutos desde distintas IPs.

### 🔗 IDS y otras soluciones de seguridad

Un IDS se complementa con otras defensas:

- **Firewall:** controla qué tráfico entra/sale, pero no detecta ataques sofisticados.
- **SIEM:** centraliza logs y correlaciona alertas del IDS con otras fuentes (servidores, aplicaciones).
- **EDR/XDR:** detectan intrusiones en endpoints (más allá de la red).
- **SOAR:** permite automatizar la respuesta a alertas del IDS.

Ejemplo de integración:

- Un **Snort IDS** detecta un ataque de SQL Injection.
- El **SIEM** correlaciona la alerta con logs de la base de datos.
- Un **SOAR** genera un playbook automático → bloquea la IP en el firewall.

✅ **En resumen:**

Un **IDS** es un sistema clave de ciberseguridad que **vigila tu red y tus sistemas** en busca de comportamientos sospechosos. Puede ser de **red (NIDS)** o de **host (HIDS)**. Cuando evoluciona a **IPS**, además de detectar, **previene ataques**. Los atacantes usan técnicas para evadirlos, por lo que siempre deben complementarse con **firewalls, SIEM, EDR y automatización**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 22**                 | **Siguiente 24**             |
| ------------------ | ---------------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./12_22_Preparation.md) | [⏩](./12_24_Containment.md) |
