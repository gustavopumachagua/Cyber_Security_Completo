| **Inicio**         | **atrás 7**          | **Siguiente 9**      |
| ------------------ | -------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./13_7_tail.md) | [⏩](./13_9_head.md) |

---

## **Índice**

| Temario                                  |
| ---------------------------------------- |
| [426. hping](#426-hping)                 |
| [427. ¿Qué es hping?](#427-qué-es-hping) |

# **hping**

## **426. hping**

![hping](/img/13_Tools_for_Incident_Response_and_Discovery/hping.png "hping")

**Resumen rápido:** `hping` / `hping3` es un generador y analizador de paquetes IP en línea de comandos. Es como un `ping` avanzado: además de ICMP puede crear y enviar paquetes **TCP, UDP y raw-IP**, manipular flags, campos IP (TTL, ID, TOS), fragmentación y payloads, y soporta funciones tipo _traceroute_, _scan_ y scripting. Es una herramienta muy útil para diagnóstico de redes, pruebas de firewall y aprendizaje, pero **también puede usarse para ataques** — por eso hay que **usar sólo en redes/hosts que controles o para los que tengas permiso**.

### 1) ¿Para qué sirve `hping` (casos de uso legítimos)?

- Probar reglas de firewall (ver si un puerto/flag pasa o es bloqueado).
- Hacer pruebas finas de conectividad (por ejemplo, enviar un SYN personalizado para ver si llega respuesta SYN/ACK).
- Diagnóstico de path-MTU (fragmentación).
- Traceroute sobre TCP/UDP/ICMP (ver dónde se filtra tráfico).
- Aprender/mostrar comportamiento del stack TCP/IP (flags, seq/ack, window).
- Generar tráfico controlado en laboratorio para medir rendimiento o comportamiento.

> **Aviso legal / ético:** No uses `hping` contra hosts de terceros sin permiso explícito. Muchas opciones permiten spoofing o floods — eso puede ser ilegal e interrumpir servicios. Si necesitas evaluar resiliencia de una red que no te pertenece, coordina con el dueño y usa un entorno de pruebas.

### 2) Instalación básica

En Debian/Ubuntu y derivadas:

```bash
sudo apt update
sudo apt install hping3
```

En Kali suele venir preinstalado; en otras distros busca el paquete `hping3` o compílalo desde su repositorio. Comprobar versión:

```bash
hping3 --version
```

(estas instrucciones son estándar en documentación y repositorios).

### 3) Sintaxis mínima

```
hping3 [opciones] <objetivo>
```

- `<objetivo>` puede ser un nombre DNS o IP.
- Muchas opciones controlan protocolo, flags TCP, puertos, intervalos y payloads. (Ver abajo las opciones más útiles).

### 4) Opciones y flags más importantes (resumen práctico)

> Nota: en la documentación oficial / cheat-sheets encontrarás una lista extensa; aquí resumo las más usadas:

- **Selección de protocolo**

  - `-1` → ICMP (equivalente a `ping`).
  - `-2` → UDP mode.
  - (por defecto hping usa TCP si no se indica).

- **Flags TCP** (poniendo la letra en mayúscula):

  - `-S` → SYN
  - `-A` → ACK
  - `-F` → FIN
  - `-R` → RST
  - `-P` → PSH
  - `-U` → URG
    (puedes combinar/usar la que necesites).

- **Puertos y direcciones**

  - `-p <port>` → puerto destino.
  - `-s <port>` → puerto origen (source port).
  - `-a <ip>` / `--spoof <ip>` → spoof de dirección origen (peligroso; requiere permisos / entorno controlado).
  - `--rand-source` → origen aleatorio (muy peligroso fuera de laboratorio).

- **Control de cantidad y ritmo**

  - `-c <count>` → enviar `count` paquetes.
  - `-i <interval>` → intervalo entre paquetes (`u` para microsegundos, ej. `-i u1000`).
  - `--flood` → envía lo más rápido posible (peligroso; **no usar contra terceros**).

- **Payload / tamaño**

  - `-d <bytes>` → tamaño del payload.
  - `-E <file>` → usar el contenido de un fichero como payload.

- **Escaneo y traceroute**

  - `--scan <rango>` o modo sweep (`-8 <rango>`) → escaneo de puertos en rango.
  - `--traceroute` → realiza traceroute usando el paquete y puerto elegido (útil para ver dónde se filtra).

- **Salida/depuración**

  - `-V` / `-v` verbose; `-q` quiet; `-S` etc. (ver `hping3 --help`).

### 5) Ejemplos **seguros** y explicados (ejecuta SOLO en tu host o laboratorio)

> **Regla de oro:** pon `-c 1` o un `-c <n>` pequeño cuando pruebes por primera vez, y evita `--flood` o `--rand-source` excepto en entornos controlados.

#### a) ICMP (equivalente a `ping`) — local/propio

Enviar 4 ICMP echo requests a localhost:

```bash
hping3 -1 -c 4 127.0.0.1
```

**Qué hace:** `-1` selecciona ICMP, `-c 4` envía 4 paquetes. Útil para comparar comportamiento frente a `ping`.

#### b) Enviar un SYN sencillo (comprobar respuesta en un puerto que controles)

```bash
sudo hping3 -S -p 8080 -c 1 127.0.0.1
```

**Qué hace:** crea y envía un **SYN** al puerto 8080 de `127.0.0.1`. Si en tu máquina hay un servicio que responde con SYN/ACK obtendrás esa respuesta; si no, verás RST o nada. Esto te ayuda a verificar reglas locales del firewall o si un servicio está escuchando. **Sólo contra hosts que controles.**

#### c) Traceroute hacia un puerto (ver dónde se filtra)

```bash
sudo hping3 --traceroute -S -p 80 ejemplo-lab.local
```

**Qué hace:** realiza un traceroute usando paquetes TCP-SYN al puerto 80; te muestra el salto donde dejan de llegar respuestas o donde cambia el comportamiento. Útil para diagnosticar filtros por puerto (firewalls).

#### d) Escaneo de rango (modo seguro: solo contra tu VM)

```bash
sudo hping3 --scan 20-1024 -S 192.168.56.101
```

**Qué hace:** intenta una exploración SYN del rango 20–1024 en la IP indicada. **Haz esto únicamente contra máquinas/VM que poseas y en cuyo permiso de pruebas tengas.** (Alternativa: `-8 1-1024 -S` en versiones que soporten sweep).

### 6) Interpretación básica de la salida

`hping3` imprime por cada paquete enviado y recibido: flags, TTL, ID, window, longitud, y a veces el contenido del payload.

- Respuesta **SYN/ACK** ante un SYN → puerto **probablemente abierto**.
- Respuesta **RST** → puerto **cerrado**.
- **Sin respuesta** → filtrado por firewall o drop.
- En traceroute verás el salto (router) después del cual no recibes respuestas — indica bloqueo/filtrado en ese punto. (Interpretación clásica de probing).

### 7) Funciones avanzadas y scripting

- `hping3` puede usarse con scripts (Tcl) para automatizar pruebas complejas y análisis; la versión 3 incluye capacidades de scripting para describir paquetes y procesos más complejos. Si vas a automatizar, registra resultados, timestamps y ejecuta sólo en ambientes controlados.

### 8) Herramientas complementarias (para uso defensivo)

- **Wireshark/tcpdump** → capturar y analizar los paquetes que generes con `hping`.
- **iptables/nftables** → probar reglas y respuestas.
- **nmap/nping** → alternativas para escaneo y pruebas (nping es complementario a nmap).
- **ddrescue / iperf** → otras pruebas de red y recuperación.

### 9) Riesgos y buenas prácticas

- **Riesgo:** opciones como `--flood` o `--rand-source` permiten generar ataques tipo DoS o spoofing; no publiques ni ejecutes esto en redes públicas.
- **Buenas prácticas:**

  - Ejecuta solo en un entorno de laboratorio o con permiso escrito.
  - Usa `-c` pequeño para pruebas iniciales.
  - Documenta y guarda salidas para auditoría.
  - Para pruebas de carga o estrés coordina con el equipo de red y usa ventanas de mantenimiento.
  - Para tests de resiliencia, considera servicios y proveedores que ofrecen entornos controlados.

### 10) Recursos y lectura recomendada

- Página de la herramienta / repositorios y la manpage: `hping3 --help` y `man hping3`.
- Cheatsheets y guías prácticas (opciones extenso): ver cheat-sheet / tutoriales de hping3.

### Conclusión breve

`hping3` es una **herramienta poderosa y flexible** para crear, enviar y analizar paquetes IP (TCP/UDP/ICMP/raw). Excelente para diagnóstico avanzado, pruebas de firewall y aprendizaje del stack TCP/IP — **pero** exige responsabilidad: **solo úsala en redes y hosts que controlas o con permiso explícito**.

---

[🔼](#índice)

---

## **427. ¿Qué es hping?**

### 1 — Definición breve: ¿Qué es Hping?

**Hping** (habitualmente `hping3`) es una utilidad de línea de comandos que permite **construir, enviar y analizar paquetes IP personalizados** (ICMP, TCP, UDP o raw-IP). Es un _packet crafter_ y _packet sniffer/sender_: no es sólo un `ping` mejorado, sino una herramienta para generar tráfico con control fino sobre flags TCP, puertos fuente/destino, TTL, ID, payload, fragmentación, intervalos, etc.

En pocas palabras: **hping te deja crear paquetes “a la carta”** y observar la respuesta de la red o el host, lo que la hace muy útil para pruebas de conectividad, validación de reglas de firewall, diagnóstico de filtrado y educación sobre TCP/IP.

### 2 — Definición detallada (qué hace y características)

- **Genera paquetes**: ICMP (`-1`), UDP (`-2`), TCP (por defecto con flags como `-S` SYN, `-A` ACK, etc.) y raw-IP.
- **Personaliza cabeceras**: puedes controlar flags TCP, puerto origen/destino, tamaño de payload, TTL, identificación IP, fragmentación.
- Soporta **traceroute** basado en distintos protocolos (útil para saber dónde se filtra tráfico).
- Permite **sweep/scan** y pruebas (por ejemplo detectar si un firewall filtra SYN vs ACK).
- Tiene opciones para controlar ritmo y conteo (`-c`, `-i`), integrar payloads desde archivos, y producir salidas interpretables para análisis.
- Puede combinarse con **Wireshark/tcpdump** para inspeccionar a bajo nivel lo que ocurre en la red.

### 3 — ¿Para qué se usa Hping en seguridad? (usos legítimos)

- **Probar reglas de firewall**: enviar paquetes con distintos flags para verificar si el firewall deja pasar SYN pero bloquea ACK, o si filtra por puerto/TTL.
- **Diagnóstico de conectividad**: comprobar si un servicio responde en niveles distintos (ICMP vs TCP).
- **Traceroute avanzado**: identificar el salto donde un tipo de paquete es filtrado.
- **Simulación y entrenamiento**: crear tráfico malformado o inusual en un laboratorio para entrenar detección (IDS/IPS).
- **Medición de Path MTU y fragmentación**: probar si paquetes fragmentados se pasan o se descartan.
- **Validación de seguridad**: comprobar que reglas de bloqueo funcionen como se espera (ej. que no haya respuestas inesperadas a paquetes spoofeados o con flags raros).

> Importante: todos los usos descritos deben realizarse **solo** contra equipos/redes que controles o para los que tengas permiso explícito.

### 4 — ¿Cómo lo usan los piratas informáticos (concepto, no instrucciones)?

A nivel conceptual, atacantes pueden usar `hping` o herramientas similares para:

- **Reconocimiento activo**: sondear puertos y medir respuestas para encontrar servicios accesibles.
- **Mapeo de filtros**: distinguir entre puertos cerrados y puertos “filtrados” por firewall (p. ej. detectar qué filtros aplican a SYN, ACK, UDP).
- **Evasión simple**: variar flags, tamaños y timing para intentar evadir reglas de firewall o detección superficial.
- **Generación de tráfico malicioso** (floods, spoofing) — _esto es dañino y muchas veces ilegal_.

Eso significa que `hping` **puede ser usado por atacantes**, pero la capacidad de crear paquetes también lo convierte en una herramienta valiosísima para defensores que quieren reproducir y detectar esos mismos patrones en laboratorio.

### 5 — ¿Pueden los comandos Hping ayudar a comprender a los piratas informáticos?

Sí — **absolutamente**, con matices:

**Por qué sí ayuda**

- **Aprendizaje técnico**: verás exactamente qué paquetes envía un SYN, un ACK, cómo cambia el comportamiento del host ante flags raros, o cómo responde un firewall a UDP vs ICMP. Ese conocimiento es la base para entender reconocimiento activo y fingerprinting.
- **Simulación controlada**: en laboratorio puedes reproducir técnicas de reconocimiento (sin explotarlas) para entrenar detección y respuesta.
- **Mejorar defensas**: con `hping` puedes afinar reglas de filtrado, configurar umbrales de IDS/IPS y validar políticas de red.

**Limitaciones y precauciones**

- Comprender la teoría no te convierte automáticamente en “pirata”, pero sí te da las mismas herramientas conceptuales; por esto la ética y el ámbito son esenciales.
- No es necesario (ni ético/ legal) replicar ataques reales contra terceros para aprender; todo conocimiento práctico debe aplicarse en entornos de laboratorio o con permiso.

### 6 — Ejemplos DIDÁCTICOS (seguros) — _solo en tu lab / hosts autorizados_

A modo ilustrativo (ejemplos benignos; **no** los ejecutes contra equipos ajenos):

- Probar ICMP (como `ping` pero con hping):

```
hping3 -1 -c 3 127.0.0.1
```

- Enviar un SYN a un puerto local para ver respuesta (útil para comprobar firewall local):

```
sudo hping3 -S -p 8080 -c 1 127.0.0.1
```

- Hacer un traceroute TCP (ver en qué salto un tipo de paquete es bloqueado):

```
sudo hping3 --traceroute -S -p 80 objetivo-lab.local
```

> Recalco: estos ejemplos son **educativos** y sirven para aprender cómo cambian respuestas de red según el tipo de paquete. Úsalos únicamente en máquinas que controles o en un entorno de prueba.

### 7 — Cómo usar el aprendizaje con hping para defender mejor

- **Crear escenarios de prueba**: simula reconocimiento no destructivo para ver si IDS/IPS y logs detectan la actividad.
- **Tunning de reglas**: prueba distintas firmas (flags, pattern de payload) y ajusta reglas para reducir falsos positivos/negativos.
- **Formación de equipos**: ejercicios en laboratorio enseñan a equipos de SOC y operaciones a identificar patrones reales en capturas.
- **Combinación con captura**: usa `hping` + `tcpdump`/Wireshark para estudiar paquetes exactos y cómo los detectores los registran.

### 8 — Riesgos éticos y legales (resumen)

- `hping` permite técnicas que, usadas sin permiso, pueden ser ataques (reconocimiento agresivo, spoofing, floods).
- **Ley y política**: en la mayoría de jurisdicciones escanear o generar tráfico contra infraestructuras ajenas sin autorización puede ser delito o violación de políticas.
- **Buenas prácticas**: documentación de pruebas, acuerdos de autorización, ventanas de mantenimiento y uso de redes/VMs aisladas para pruebas.

### 9 — Recursos y siguientes pasos (recomendados)

- Monta un **laboratorio local** (VMs en VirtualBox/VMware, redes host-only) para practicar.
- Aprende a capturar/analisar con **tcpdump/Wireshark** y correlaciona con lo que genera `hping`.
- Practica detección: genera tráfico “sospechoso” controlado y verifica alertas en IDS (Snort/Suricata).
- Lee la **manpage** `man hping3`, guías de seguridad defensiva y cursos de redes TCP/IP.

### 10 — Resumen — respuesta directa a tu pregunta final

- **Definición:** Hping es un generador/analizador de paquetes que permite crear tráfico IP/TCP/UDP/ICMP personalizado; es una herramienta de sécurité/diagnóstico muy potente.
- **¿Ayuda a comprender a los piratas informáticos?** Sí: practicar con `hping` en un entorno controlado ayuda a entender las técnicas y el comportamiento de reconocimiento y evasión que pueden usar los atacantes, y por tanto **mejora la capacidad defensiva**. Pero siempre con ética, en laboratorios o con permiso — no contra terceros.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**          | **Siguiente 9**      |
| ------------------ | -------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./13_7_tail.md) | [⏩](./13_9_head.md) |
