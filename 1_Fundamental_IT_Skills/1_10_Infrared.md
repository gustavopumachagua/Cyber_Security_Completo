| **Inicio**         | **atrás 9**         | **Siguiente 11**                       |
| ------------------ | ------------------- | -------------------------------------- |
| [🏠](../README.md) | [⏪](./1_9_WiFi.md) | [⏩](./1_11_Microsoft_Office_Suite.md) |

---

## **Índice**

| Temario                                                        |
| -------------------------------------------------------------- |
| [26. Definición de infrarrojos](#26-definición-de-infrarrojos) |
| [27. Infrarrojo](#27-infrarrojo)                               |

---

# **Infrared**

## **26. Definición de infrarrojos**

![Infrared](/img/1_Fundamental_IT_Skills/Infrared.jpg "Infrared")

### Definición de **infrarrojos (IR)**

Los **infrarrojos** son un tipo de **radiación electromagnética** con una longitud de onda mayor que la de la luz visible pero menor que la de las microondas.

- Rango aproximado: **700 nm a 1 mm**.
- No son visibles al ojo humano, pero se perciben como **calor**.
- Se usan en muchos campos: **telecomunicaciones (controles remotos)**, **sensores de movimiento**, **cámaras térmicas**, **fibra óptica infrarroja**, **medicina** y **astronomía**.

**Ejemplos de uso cotidiano:**

- El control remoto de tu TV que envía señales al televisor por un LED infrarrojo.
- Los sensores de proximidad de los celulares que apagan la pantalla cuando acercas el teléfono a la oreja.
- Cámaras térmicas que detectan calor para seguridad o medicina.

### Mal uso de los infrarrojos

Aunque los infrarrojos tienen aplicaciones útiles, su **mal uso o exposición excesiva** puede ser perjudicial o generar problemas:

1. **Exposición excesiva en la salud**

   - Los IR generan calor; en altas intensidades pueden **dañar tejidos**, causar **quemaduras** o **lesiones oculares** (por ejemplo, en trabajadores que se exponen a hornos industriales o lámparas IR sin protección).

2. **Uso indebido en vigilancia**

   - Cámaras infrarrojas pueden usarse para **vigilar sin consentimiento**, afectando la **privacidad**.

3. **Seguridad informática**

   - Algunos dispositivos con puertos de comunicación IR (antiguos celulares, PDAs, equipos industriales) eran vulnerables a **interceptación de datos** si no había cifrado.

4. **Interferencias o fallos**

   - En controles remotos IR, apuntar a varios equipos cercanos o saturar con luz intensa puede generar errores.
   - El mal diseño de sensores IR en sistemas de alarma o puertas automáticas puede causar **falsas activaciones** o fallos de seguridad.

✅ **En resumen:**

Los **infrarrojos** son radiación electromagnética usada en comunicación, detección y visión térmica. Su **mal uso** puede afectar la salud (exposición intensa), la **privacidad** (vigilancia no autorizada), la **seguridad informática** (interceptar datos) o la **confiabilidad** de sistemas (fallos en sensores).

---

[🔼](#índice)

---

## **27. Infrarrojo**

### 1. Comprender los infrarrojos en la ciberseguridad

Los **infrarrojos (IR)** son ondas electromagnéticas invisibles al ojo humano, muy usadas en **comunicación a corta distancia, detección de movimiento y transmisión de datos**.

En **ciberseguridad**, los IR se relacionan principalmente con:

- **Canales de comunicación** (p. ej., puertos infrarrojos en equipos antiguos o industriales).
- **Sensores de seguridad física** (detección de intrusos con barreras IR).
- **Cámaras térmicas** para **vigilancia perimetral** en infraestructuras críticas.
- **Riesgos de canales encubiertos** (_covert channels_), donde se pueden usar IR para exfiltrar datos sin ser detectados.

### 2. Implementación de infrarrojos y sus implicaciones

Al implementar IR en sistemas de seguridad:

✅ **Ventajas:**

- Comunicación sin cables en ambientes cerrados (no atraviesan paredes → mayor control físico).
- Bajo costo y bajo consumo de energía.
- Útiles en entornos donde Wi-Fi o Bluetooth pueden generar interferencias.

⚠️ **Implicaciones y riesgos:**

- **Limitado alcance** (pocos metros) y dependencia de línea de visión.
- **Intercepciones**: un atacante en el rango puede capturar señales IR si no están cifradas.
- **Puertos IR sin gestionar** en dispositivos antiguos pueden abrir vectores de ataque.
- **Falsas alarmas** o evasión en sistemas físicos (ej.: intruso que engaña sensores con espejos o calor).

### 3. Mejores prácticas al considerar IR en ciberseguridad

1. **Cifrado de datos** en transmisiones IR (evitar protocolos sin seguridad como IrDA clásico).
2. **Segmentación**: nunca mezclar comunicaciones críticas en enlaces IR con redes abiertas.
3. **Monitoreo de sensores IR** en seguridad física (alertas por manipulación o bloqueo).
4. **Políticas de seguridad física**: controlar acceso a áreas con sensores y cámaras IR.
5. **Pruebas periódicas** (pentesting físico + ciber) para detectar vulnerabilidades.

**Ejemplo:** en un hospital con dispositivos médicos que usan IR para comunicarse, se deben usar protocolos cifrados y restringir acceso físico para evitar fugas de datos de pacientes.

### 4. Gestión de infrarrojos en ciberseguridad

La **gestión** implica:

- **Inventario**: identificar dispositivos que usen IR (controles, sensores, cámaras, equipos industriales).
- **Parcheo o reemplazo** de tecnologías antiguas que usen IR sin cifrado.
- **Políticas de uso**: cuándo, dónde y cómo se emplea IR en la organización.
- **Monitoreo en tiempo real** de sistemas que dependen de sensores IR (ej. alarmas).
- **Respuesta ante incidentes**: plan para manipulación, bloqueo o ataque a sistemas basados en IR.

### 5. Explorando términos y conceptos relacionados

- **IrDA (Infrared Data Association):** estándar para transmisión IR en dispositivos (antiguo en laptops, móviles).
- **Covert Channels IR:** uso de IR para exfiltrar datos de forma encubierta.
- **Barreras IR:** sensores que detectan interrupciones en el haz (usados en alarmas).
- **Cámaras térmicas IR:** para vigilancia perimetral y detección de intrusos.
- **IR Jamming:** interferencia deliberada de señales IR.

### 6. Principales aplicaciones de IR en ciberseguridad

- **Vigilancia física:** cámaras térmicas para detectar intrusos en áreas críticas.
- **Protección perimetral:** barreras IR en edificios y centros de datos.
- **Autenticación biométrica:** algunos sistemas usan sensores IR para reconocimiento facial.
- **Comunicaciones seguras de corto alcance:** transmisión de datos en entornos con baja interferencia electromagnética.
- **Detección de manipulación:** sensores IR en racks de servidores o gabinetes.

### 7. Contribución de IR a la detección y prevención de amenazas

- **Prevención física:** detectar intrusos en zonas restringidas con sensores o cámaras IR.
- **Reducción de riesgo inalámbrico:** comunicaciones IR no atraviesan paredes, lo que limita fugas fuera de la sala (ej.: en salas de servidores).
- **Complemento en autenticación:** cámaras IR para detectar rostros falsos (anti-spoofing en biometría).
- **Forense digital físico:** cámaras térmicas IR ayudan a detectar dispositivos ocultos (ej.: un USB espía caliente en un servidor).

### 8. Desafíos comunes en implementación de IR en ciberseguridad

- **Limitado alcance y necesidad de línea de visión** (fácil de bloquear).
- **Obsolescencia de estándares** (IrDA inseguro en comparación con Wi-Fi/Bluetooth).
- **Posible evasión** de sensores (espejos, humo, manipulación).
- **Costos de mantenimiento** de cámaras y sensores IR especializados.
- **Falsa sensación de seguridad** si no se integran con otras medidas (ej.: IDS, SIEM).

### 9. Mantenerse actualizados en ciberseguridad infrarroja

- Participar en foros de seguridad (OWASP, IEEE, SANS).
- Seguir estándares de seguridad física y digital (NIST, ISO/IEC 27001).
- Capacitarse en **IoT Security** (muchos dispositivos IoT aún usan IR).
- Revisar boletines de vulnerabilidades (CVE) relacionados con hardware IR.

### 10. Papel de la formación en optimización del uso de IR

- **Capacitación técnica**: equipos de IT deben conocer riesgos de IR y protocolos de protección.
- **Conciencia en empleados**: cómo detectar manipulación de sensores o cámaras IR.
- **Pentesting físico**: formar personal que evalúe ataques reales con IR (bloqueo, exfiltración, manipulación).

### 11. Importancia de la formación colaborativa en protocolos de seguridad IR

- Fomenta la **coordinación entre áreas de ciberseguridad y seguridad física**.
- Permite responder mejor a incidentes donde un atacante explota debilidades en sensores o cámaras.
- Ayuda a que todos (IT, seguridad física, operaciones) comprendan cómo reaccionar ante ataques que involucran IR.

**Ejemplo:** en un centro de datos, si un sensor IR perimetral falla, el equipo de seguridad física debe notificar a IT, y ambos deben activar un protocolo coordinado de respuesta.

✅ **En resumen:**

Los **infrarrojos en ciberseguridad** son útiles para **detección de intrusos, comunicaciones seguras de corto alcance y autenticación biométrica**, pero presentan desafíos de seguridad y obsolescencia.
Su correcta gestión implica **inventario, monitoreo, cifrado, pruebas periódicas y formación colaborativa** entre áreas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 9**         | **Siguiente 11**                       |
| ------------------ | ------------------- | -------------------------------------- |
| [🏠](../README.md) | [⏪](./1_9_WiFi.md) | [⏩](./1_11_Microsoft_Office_Suite.md) |
