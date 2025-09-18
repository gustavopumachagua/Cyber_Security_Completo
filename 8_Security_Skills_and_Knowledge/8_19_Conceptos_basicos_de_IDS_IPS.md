| **Inicio**         | **atrás 18**                                        | **Siguiente 20**                              |
| ------------------ | --------------------------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_18_Understand_the_Concept_of_Isolation.md) | [⏩](./8_20_Autenticacion_vs_Autorizacion.md) |

---

## **Índice**

| Temario                                                                                                                    |
| -------------------------------------------------------------------------------------------------------------------------- |
| [232. ¿Qué es un sistema de prevención de intrusiones?](#232-qué-es-un-sistema-de-prevención-de-intrusiones)               |
| [233. ¿Qué son los sistemas de detección de intrusiones (IDS)?](#233-qué-son-los-sistemas-de-detección-de-intrusiones-ids) |
| [234. Sistema de prevención de intrusiones (IPS)](#234-sistema-de-prevención-de-intrusiones-ips)                           |

# **Conceptos básicos de IDS e IPS**

## **232. ¿Qué es un sistema de prevención de intrusiones?**

![IDS e IPS](/img/8_Security_Skills_and_Knowledge/IDS_IPS.png "IDS e IPS")

### 1) ¿Qué es un sistema de prevención de intrusiones?

Un **IPS (Intrusion Prevention System)** es una solución de seguridad en red que **monitorea el tráfico en tiempo real, detecta actividades maliciosas y las bloquea de forma automática** antes de que puedan afectar al sistema o la red.

👉 A diferencia de un IDS (Intrusion Detection System), que solo alerta, un **IPS actúa de inmediato** (bloquea, descarta paquetes, reinicia conexiones, etc.).

**Ejemplo:**

- Un atacante intenta explotar una vulnerabilidad en un servidor web.
- El IPS analiza la petición HTTP, detecta que coincide con la firma de un ataque SQL Injection y automáticamente bloquea el tráfico antes de que llegue al servidor.

### 2) ¿Cómo funcionan los sistemas de prevención de intrusiones?

Un IPS funciona en línea (inline), es decir, **entre el firewall y la red interna**.

Su funcionamiento básico es:

1. **Captura el tráfico** → intercepta paquetes de datos en tiempo real.
2. **Analiza y compara** → usa firmas, heurísticas, machine learning o análisis de comportamiento.
3. **Detecta anomalías o ataques conocidos** → exploits, malware, DoS, escaneo de puertos, etc.
4. **Toma acción preventiva**:

   - Bloquear la dirección IP atacante.
   - Rechazar paquetes sospechosos.
   - Terminar sesiones activas.
   - Alertar al administrador de seguridad.

### 3) Tipos de sistemas de prevención de intrusiones

Existen diferentes categorías de IPS según el enfoque:

1. **NIPS (Network-based IPS)**

   - Se despliega en la red para analizar tráfico.
   - Protege contra ataques como escaneos, DoS/DDoS, exploits en protocolos.
   - Ejemplo: Cisco Firepower, Snort en modo inline.

2. **HIPS (Host-based IPS)**

   - Instalado en un host específico (servidor o endpoint).
   - Monitorea procesos, registros, integridad de archivos.
   - Ejemplo: OSSEC, Symantec HIPS.

3. **WIPS (Wireless IPS)**

   - Diseñado para redes Wi-Fi.
   - Detecta puntos de acceso falsos, ataques de desautenticación, sniffing inalámbrico.
   - Ejemplo: AirMagnet WIPS.

4. **NBA (Network Behavior Analysis)**

   - Basado en el análisis de comportamiento de la red.
   - Identifica anomalías como tráfico masivo de botnets o comportamientos extraños.

### 4) Los beneficios de los sistemas de prevención de intrusiones

- 🚨 **Bloqueo automático de amenazas** en tiempo real.
- 🛡 **Protección frente a ataques conocidos y desconocidos** (según motor de detección).
- 📊 **Visibilidad completa del tráfico de red**.
- 🔒 **Refuerzo de cumplimiento normativo** (PCI DSS, HIPAA, ISO 27001).
- ⚡ **Reducción de la carga del SOC** → menos falsos positivos comparado con un IDS.
- 🌐 **Defensa contra ataques modernos** como exploits de día cero (si usan ML/IA).

### 5) Características críticas de un IPS

Un buen IPS debe incluir:

- **Detección por firmas**: identifica patrones de ataques conocidos.
- **Análisis de anomalías**: detecta comportamientos extraños (zero-day, APTs).
- **Prevención activa**: bloquear, reiniciar conexiones o aislar nodos.
- **Integración con SIEM**: centralizar logs y correlación de eventos.
- **Actualización constante**: firmas y modelos actualizados frente a nuevas amenazas.
- **Escalabilidad**: capaz de manejar tráfico de alta velocidad sin latencia.
- **Soporte para cifrado**: inspección de tráfico TLS/SSL.

### 6) Aprendizaje profundo para la detección de amenazas evasivas

Hoy en día, los atacantes usan técnicas para evadir firmas tradicionales.
Aquí entra el **deep learning aplicado al IPS**:

- **Redes neuronales** entrenadas con tráfico benigno y malicioso → detectan anomalías en tiempo real.
- **Modelos de comportamiento** → aprenden qué es “normal” en una red y marcan lo que se sale de lo común.
- **Ejemplo práctico:**

  - Un malware polimórfico cambia su firma para evadir detección.
  - El IPS con IA detecta patrones en el flujo de datos (frecuencia, tamaño, timing de paquetes) y bloquea la conexión aunque la firma no exista.

### 7) Preguntas frecuentes sobre IPS

**🔹 ¿En qué se diferencia un IPS de un firewall?**

- El firewall controla accesos según reglas (IP, puertos, protocolos).
- El IPS analiza contenido profundo del tráfico (payload) para detectar ataques.

**🔹 ¿IPS reemplaza al IDS?**

No, lo complementa. Muchos sistemas actuales integran IDS + IPS en un solo dispositivo (IDS para monitoreo pasivo, IPS para acción activa).

**🔹 ¿IPS puede detener ataques de día cero?**

Depende: los basados en firmas no, pero los basados en comportamiento, heurística o IA sí pueden detectar patrones sospechosos.

**🔹 ¿Es necesario un IPS si ya tengo antivirus?**

Sí. El antivirus protege endpoints, el IPS protege la red en tránsito. Son capas distintas de defensa.

**🔹 ¿Dónde se coloca un IPS?**

En línea (inline) entre el firewall y la red interna, de modo que pueda inspeccionar y bloquear tráfico en tiempo real.

---

[🔼](#índice)

---

## **233. ¿Qué son los sistemas de detección de intrusiones (IDS)?**

### 1) ¿Qué es un sistema de detección de intrusiones (IDS)?

Un **IDS (Intrusion Detection System)** es una herramienta de ciberseguridad que **monitorea el tráfico de red o la actividad en un host**, con el objetivo de **detectar comportamientos maliciosos, accesos no autorizados o violaciones de políticas**.

👉 A diferencia de un **IPS (Intrusion Prevention System)**, el IDS **solo alerta**, no bloquea automáticamente.

**Ejemplo:**

Si alguien lanza un ataque de **fuerza bruta SSH** contra un servidor, el IDS lo detecta y genera una alerta al administrador, pero no detiene la conexión por sí mismo.

### 2) ¿Qué es una intrusión en ciberseguridad?

Una **intrusión** es cualquier acceso, intento de acceso o acción no autorizada dentro de un sistema, red o aplicación, ya sea para:

- Robar información.
- Alterar datos.
- Instalar malware.
- Interrumpir servicios (DoS/DDoS).

Ejemplos de intrusiones:

- Inyección SQL en un sitio web.
- Usuario externo que accede a la red interna.
- Instalación de un keylogger en un endpoint.

### 3) Tipos de sistemas de detección de intrusiones (IDS)

Existen varios tipos, según su ubicación y método de detección:

1. **NIDS (Network-based IDS)**

   - Se coloca en un punto estratégico de la red para analizar tráfico.
   - Detecta ataques como escaneos de puertos, DoS, exploits en protocolos.
   - Ejemplo: Snort, Suricata.

2. **HIDS (Host-based IDS)**

   - Instalado en un host (servidor, endpoint).
   - Monitorea procesos, registros, integridad de archivos.
   - Ejemplo: OSSEC, Tripwire.

3. **IDS híbrido**

   - Combina NIDS + HIDS para mayor cobertura.

4. **Basado en firmas**

   - Detecta ataques conocidos mediante patrones predefinidos.
   - Ejemplo: detectar un payload de malware.

5. **Basado en anomalías**

   - Detecta comportamientos extraños (ej. tráfico inusual de un servidor a medianoche).
   - Útil contra ataques de día cero.

### 4) ¿Cómo funciona un sistema de detección de intrusos? ¿Cuáles son sus usos?

#### Funcionamiento:

1. **Captura datos** → paquetes de red o registros de host.
2. **Analiza tráfico/actividad** usando firmas, heurística o modelos estadísticos.
3. **Detecta anomalías o ataques conocidos**.
4. **Genera alertas** → notifica al SOC, SIEM o administrador.

#### Usos:

- Detección de intrusiones externas (hackers, malware).
- Monitoreo de actividades internas sospechosas (insiders).
- Análisis forense (logs históricos de ataques).
- Cumplimiento normativo (ej. PCI DSS requiere monitoreo de seguridad).

### 5) ¿Por qué son importantes los sistemas de detección de intrusiones (IDS)?

- **Detección temprana de amenazas** antes de que se concreten.
- **Visibilidad en la red/hosts** para identificar comportamientos sospechosos.
- **Cumplimiento legal y normativo**.
- **Soporte al análisis forense** (qué pasó, cuándo, desde dónde).
- **Complemento al firewall** → detectan ataques que pasan por reglas de firewall.

### 6) Beneficios de los sistemas de detección de intrusiones

- 🚨 **Alertas en tiempo real**.
- 🔍 **Monitoreo continuo** de red y sistemas.
- 🧠 **Aprendizaje de ataques nuevos** mediante anomalías.
- 📊 **Información valiosa para el SOC y SIEM**.
- 🛡 **Protección contra insiders** (no solo externos).

### 7) Desafíos de los sistemas de detección de intrusiones (IDS)

- **Falsos positivos**: alertas por comportamientos legítimos.
- **Falsos negativos**: ataques que pasan desapercibidos.
- **Sobrecarga de alertas**: demasiada información para el SOC.
- **Requiere ajuste constante** de reglas y firmas.
- **No previene por sí solo** → necesita integrarse con IPS, firewalls y SIEM.

### 8) IDS vs IPS (Detección vs Prevención)

| Característica        | IDS                 | IPS                       |
| --------------------- | ------------------- | ------------------------- |
| **Función**           | Detectar y alertar  | Detectar y bloquear       |
| **Modo de operación** | Pasivo              | Activo (inline)           |
| **Acción**            | Genera notificación | Bloquea tráfico malicioso |
| **Ejemplo**           | Snort en modo IDS   | Snort en modo IPS inline  |

### 9) Diferencia entre un firewall y un IDS

- 🔹 **Firewall**: controla accesos por reglas estáticas (IP, puertos, protocolos). No detecta ataques dentro del tráfico permitido.
- 🔹 **IDS**: analiza el contenido del tráfico en detalle (payload), detecta patrones maliciosos y anomalías.

👉 **Ejemplo práctico:**

- Un firewall permite el puerto 80 (HTTP) para acceso web.
- Un atacante lanza un **SQL Injection** a través de HTTP.
- El firewall no lo detecta porque la regla solo permite/deniega puertos.
- El IDS analiza la petición HTTP y detecta el ataque.

### 10) Preguntas frecuentes sobre IDS

**🔹 ¿IDS reemplaza al firewall?**

No, se complementan. El firewall filtra acceso básico, el IDS detecta ataques avanzados.

**🔹 ¿IDS puede detener ataques?**

No directamente, solo alerta. Para detener, se necesita un IPS.

**🔹 ¿IDS detecta ataques de día cero?**

Sí, pero solo si usa detección por anomalías o machine learning.

**🔹 ¿Dónde se instala un IDS?**

- NIDS: en un switch/router estratégico, conectado al tráfico de la red.

- HIDS: directamente en servidores/hosts críticos.

**🔹 ¿Es obligatorio tener IDS en una empresa?**

No siempre, pero en sectores regulados (financiero, salud, gobierno) suele ser un requisito de cumplimiento.

---

[🔼](#índice)

---

## **234. Sistema de prevención de intrusiones (IPS)**

Un **IPS (Intrusion Prevention System)** es una solución de seguridad que **monitorea el tráfico en tiempo real** y **actúa automáticamente** para bloquear o mitigar actividades maliciosas antes de que lleguen a los activos protegidos. Piensa en un IPS como un IDS (detección) que además puede **interrumpir** la conexión o aplicar contramedidas.

### 1 — ¿Cómo y dónde se despliega un IPS? (modos de colocación)

- **Inline (en línea / bump-in-the-wire / bridge)**: el IPS está en la ruta del tráfico; puede bloquear paquetes directamente.
- **Fallo / bypass**: configuraciones típicas: _fail-open_ (si falla, deja pasar tráfico) o _fail-close_ (si falla, bloquea). Elegir según criticidad.
- **Modo TAP + control-plane**: tráfico se duplica a nivel de TAP al IPS (modo pasivo) y el IPS ordena al firewall bloquear (usando API) si detecta algo — útil si no quieres latencia directa.
- **Host-based (HIPS)**: IPS instalado en servidores/hosts para monitorear procesos, llamadas al sistema, integridad de ficheros y bloquear acciones locales.
- **Cloud / virtual appliances**: appliances virtuales en VPCs (security groups + IPS inline o como servicio gestionado).

ASCII rápido de ejemplo (simplificado):

```
Internet --> Firewall edge --> [IPS inline] --> Switch --> Servidores
                     \---> (TAP) --> IPS(passive) -- alerts --> Firewall API blocks
```

### 2 — Principios de detección (motores del IPS)

1. **Por firmas (signature-based)**

   - Comparación del payload con patrones conocidos (firmas). Muy eficaz para ataques conocidos y exploits con payloads repetibles.

2. **Basado en anomalías / comportamiento (anomaly-based)**

   - Modela “lo normal” y alerta/bloquea desviaciones (útil contra zero-day y ataques polimórficos).

3. **Análisis de protocolo / stateful inspection**

   - Comprueba que el uso del protocolo se ajuste a la especificación (evita abusos del protocolo).

4. **Heurística / correlación**

   - Combina múltiples indicadores (p. ej. escaneo + payload sospechoso).

5. **Modelos ML / Deep Learning**

   - Clasificadores que detectan patrones complejos (timing, tamaños, secuencias de paquetes) para amenazas evasivas.

### 3 — Acciones que puede tomar un IPS

- **Alertar** al SOC / SIEM.
- **Bloquear o descartar paquetes** (drop).
- **Resetear la sesión TCP** (RST) para cortar la conexión.
- **Bloquear la IP** en un firewall o en la tabla del IPS.
- **Rate-limit** o throttling.
- **Aislar o “quarantine”** el host (integración con NAC/EDR).
- **Ejecutar playbook** en SOAR (automatización de respuesta).

### 4 — Ejemplos prácticos (reglas y snippets)

#### Ejemplo de regla conceptual (Suricata/Snort — modo inline)

```text
drop tcp any any -> $HOME_NET 80 (msg:"SQL Injection attempt - blocked"; flow:to_server,established; http_uri; content:"union select"; nocase; sid:1000001; rev:1;)
```

- `drop` solo funciona si el motor está corriendo en modo inline/NFQUEUE/bridge.
- `msg` explica la alerta; `sid` es identificador de la firma.

#### Cómo poner tráfico por el IPS (Linux + NFQUEUE, esquema simplificado)

```bash
# enviar tráfico FORWARD a la cola 0 (lab)
sudo iptables -I FORWARD -j NFQUEUE --queue-num 0

# arrancar Suricata en modo NFQUEUE (ejemplo)
suricata -q 0 -c /etc/suricata/suricata.yaml
```

> En producción usar configuraciones más robustas y testing previo. NFQUEUE permite que Suricata tome decisiones (accept/drop) en espacio usuario.

#### Ejemplo HIPS (host-based) — regla conceptual OSSEC/Wazuh (XML)

```xml
<rule id="100001" level="10">
  <if_host_resume>yes</if_host_resume>
  <decoded_as>sshd</decoded_as>
  <description>Multiple failed SSH login attempts</description>
  <group>authentication_failures</group>
  <match>Failed password for</match>
</rule>
```

- En HIPS se pueden configurar acciones como bloquear una IP localmente, ejecutar un script o notificar.

### 5 — Detectar amenazas evasivas: técnicas de evasión y contramedidas

**Técnicas de evasión (resumido, alto nivel):**

- Fragmentación de paquetes (evitar firmas que miran payloads no reensamblados).
- Encriptación (cifrar C2 o payloads en HTTPS).
- Polimorfismo / cifrado del payload.
- Low-and-slow (ataques lentos para evitar umbrales).
- Uso de protocolos legítimos (DNS tunneling, cloud services).

**Contramedidas que debe aplicar un IPS:**

- **Normalización y reensamblado de streams** antes de la inspección (TCP reassembly).
- **Inspección TLS/SSL** (terminación o “man-in-the-middle” controlado) o, cuando no sea posible, uso de metadatos (SNI, JA3 fingerprinting, certificados) y telemetría endpoint.
- **Análisis de comportamiento** y ML para detectar patrones temporales y secuenciales.
- **Correlación con EDR / logs de endpoints** para confirmar actividad interna.
- **Reputación y threat intel** (blocklists, IoCs).
- **Sandboxing** de artefactos sospechosos (vínculos / adjuntos) y análisis dinámico.

> Nota: explicar técnicas de evasión detalladas podría ayudar a atacantes; por eso las describo a alto nivel y centrándome en mitigaciones defensivas.

### 6 — Rendimiento, escalabilidad y disponibilidad

- **Latencia:** IPS inline añade latencia; hay que dimensionarlo para throughput esperado.
- **Hardware acceleration:** appliances usan ASIC/FPGA/NPUs para inspección a línea de wire-rate.
- **High-availability / clustering:** balanceo y réplica para tolerancia a fallos.
- **Fail-open vs fail-close:** elegir según riesgo operativo. En entornos críticos suele preferirse _fail-open_ para no interrumpir servicios, pero con monitorización estricta.
- **Single-pass inspection vs multi-pass:** diseñar para que la inspección sea eficiente (menos coste CPU por paquete).

Métricas a monitorear: paquetes por segundo, sesiones por segundo, porcentaje de paquetes descartados, CPU/latency por interfaz, tasas de falsos positivos.

### 7 — Tuning y operación (evitar demasiados falsos positivos)

- **Modo IDS primero:** empezar con el sistema en modo pasivo/alert-only hasta que reglas estén afinadas.
- **Whitelisting de tráfico legítimo.**
- **Baselining**: aprender patrones normales antes de bloquear.
- **Escalas de severidad & suppressions**: ajustar qué genera acción vs solo alerta.
- **Feedback loop con SOC:** cuando una regla dispara falsos positivos, afinar o excepcionar y documentarlo.
- **Pruebas periódicas con tráfico de referencia** (test suites) y red-team en entorno controlado.

### 8 — Integración con el ecosistema de seguridad

- **SIEM**: enviar eventos para correlación, hunting y persistencia histórica.
- **SOAR**: orquestar respuestas automáticas (bloquear IP, aislar host, ejecutar scripts).
- **Firewall / NGFW**: el IPS puede orquestar reglas en el firewall para bloqueos sostenidos.
- **EDR / NAC**: aislar endpoints sospechosos y bloquear lateral movement.
- **Threat Intelligence Feeds:** actualizar reglas y reputaciones.

### 9 — Casos de uso y ejemplos concretos (qué suele bloquear un IPS)

- **SQL Injection** detectado en HTTP → IPS drop + bloquear IP atacante.
- **Brute-force SSH** → IPS detecta patrón y bloquea IP o solicita captchas (en apps).
- **C2 beaconing** (trafico de comando/control) → detectar por comportamiento / dominios conocidos y bloquear.
- **Malware download** → bloquear la URL y aislar host.
- **Escaneo de puertos** → detectar y rate-limit o bloquear el origen.

Ejemplo: un IPS detecta una petición con payload `union select` en `POST /login` → regla drop + crear bloque de la IP en firewall por 1 hora + alerta al SOC.

### 10 — Limitaciones y buenas prácticas

**Limitaciones:**

- No detecta todo: evasión, cifrado extremo o uso de servicios legítimos dificulta detección.
- Posibles falsos positivos con impacto operativo.
- Coste operativo y necesidad de tuning continuo.

**Buenas prácticas:**

1. **Defensa en profundidad**: IPS complementa firewall, WAF, EDR, segmentación.
2. **Prueba en laboratorio** antes de poner reglas en producción.
3. **Empezar en modo IDS** y pasar a IPS cuando las reglas estén afinadas.
4. **Monitoreo continuo** de rendimiento/latencia.
5. **Integración SOC/SIEM/SOAR** con runbooks claros.
6. **Actualizaciones regulares** de firmas y modelos ML (con control de cambios).
7. **Auditoría y registro** de todas las acciones tomadas por el IPS (para forense/legal).

### 11 — Implementación rápida de laboratorio (idea práctica)

1. Monta una VM con **Suricata** (open-source) y configúrala en modo NFQUEUE.
2. Define una regla simple `alert` y otra `drop` (solo cuando tests en lab).
3. Envíate tráfico de prueba (curl con payloads controlados) y observa alertas en logs.
4. Mide latencia y verifica que el sistema no produce falsos positivos en tráfico normal.
5. Integra con ELK/Wazuh para ver eventos centralizados.

### 12 — Recomendación final (acción práctica)

- Para empezar: **evalúa tu exposición** (qué servicios están expuestos), pon IPS en **modo monitor** durante 2–4 semanas, recolecta datos, ajusta reglas y después activa bloqueo en capas críticas.
- Integra con EDR y SIEM para efectividad real.
- Automatiza respuestas (SOAR) con cuidado y revisa runbooks para evitar bloqueos legítimos.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 18**                                        | **Siguiente 20**                              |
| ------------------ | --------------------------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_18_Understand_the_Concept_of_Isolation.md) | [⏩](./8_20_Autenticacion_vs_Autorizacion.md) |
