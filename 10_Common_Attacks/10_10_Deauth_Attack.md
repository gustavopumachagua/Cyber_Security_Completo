| **Inicio**         | **atrás 9**                                                    | **Siguiente 11**               |
| ------------------ | -------------------------------------------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./10_9_DNS_Poisoning_DNS_Spoofing_DNS_Cache_Poisoning.md) | [⏩](./10_11_Replay_Attack.md) |

---

## **Índice**

| Temario                                                                              |
| ------------------------------------------------------------------------------------ |
| [318. Ataque de desautenticación de Wi-Fi](#318-ataque-de-desautenticación-de-wi-fi) |
| [319. Ataques de desautenticación](#319-ataques-de-desautenticación)                 |

# **Ataque mortal**

## **318. Ataque de desautenticación de Wi-Fi**

![Ataque mortal](/img/10_Common_Attacks/Ataque.jpg "Ataque mortal")

### ¿Qué es un ataque de desautenticación (deauth)?

Un **ataque de desautenticación Wi-Fi** consiste en que un atacante envía deliberadamente tramas de _deauthentication_ (un tipo de trama de gestión del estándar IEEE 802.11) para forzar que uno o varios clientes se desconecten de un punto de acceso (AP). Es una forma de **denegación de servicio** sobre el enlace inalámbrico y una táctica muy usada como paso previo a otros ataques (por ejemplo, forzar re-conexiones a un “evil-twin” o capturar handshakes).

### Fundamento técnico — ¿qué es exactamente una trama de desautenticación?

En Wi-Fi hay varios tipos de tramas: gestión, control y datos. Las **tramas de gestión** incluyen _authentication_, _association_, _disassociation_ y _deauthentication_. La trama de **deauthentication** (subtipo 12 / 0x0c) es una notificación que indica a la otra parte que la autenticación ha terminado; por diseño tradicional estas tramas **no estaban protegidas**, por lo que cualquiera que pueda enviar una trama con la dirección MAC adecuada puede intentar desconectar a un cliente.

### ¿Cómo funciona el ataque (concepto, no pasos)?

De forma conceptual el atacante:

1. Observa la red y aprende las direcciones MAC del AP y del cliente objetivo (estas MAC están visibles “on the air”).
2. Construye y **transmite tramas de desautenticación** falsificadas, aparentando venir del AP (o del cliente).
3. El cliente (o AP) recibe la trama y, al ser una notificación válida por el protocolo, suele desconectarse y quitar la asociación.
4. Dependiendo del objetivo, esto causa:

   - **Interrupción del servicio** (DoS) para el usuario o para toda la SSID.
   - **Forzar reintentos de conexión** que el atacante aprovecha (p. ej. para desplegar un AP falso/Evil Twin o intentar capturar el 4-way handshake).

     Porque estas tramas históricamente no iban firmadas ni autenticadas, el vector es trivial desde el punto de vista conceptual.

### Ejemplos de impacto (casos reales y didácticos)

- **Cámara IP desconectada**: un atacante mantiene desconectada una cámara de seguridad enviando deauths repetidos → pérdida de videovigilancia.
- **Forzar a los usuarios a un Evil Twin**: se desconecta a los clientes del AP legítimo; cuando vuelven a conectarse, algunos aceptan un AP falso con el mismo SSID.
- **Interrupción en eventos**: en conferencias o locales concurridos, un ataque masivo de deauth puede degradar toda la experiencia Wi-Fi.

### Cómo detectar un ataque de desautenticación

(estrategias defensivas, herramientas de monitorización)

- **Patrón obvio:** alto volumen de tramas de deauthentication (subtipo 12) en un periodo corto, especialmente dirigidas a muchos clientes. Esto es anómalo. (filtro Wireshark: `wlan.fc.type_subtype == 0x0c` / `wlan.fc.type_subtype == 12`).
- **Fuentes múltiples o MACs extrañas:** deauths que provienen de MACs que no son APs autorizados o que cambian rápidamente.
- **Clientes que se desconectan y reconectan en oleadas**: revisa los logs del AP (association/deauth events).
- **Herramientas de monitoring Wi-Fi (IDS/monitoring):** soluciones como nzyme u otros sistemas de Wi-Fi monitoring pueden generar alertas cuando detectan ráfagas de management frames sospechosos.

_(Detectar no equivale a “probar” — hazlo sólo en tus redes y en entornos de laboratorio controlados.)_

### Cómo defender redes y dispositivos (prácticas recomendadas)

Estas mitigaciones reducen mucho la efectividad del ataque o facilitan su detección:

#### 1. Protege las _management frames_ — habilita **Protected Management Frames (PMF / IEEE 802.11w)**

- PMF (802.11w) añade integridad/autenticidad y protección de replay a ciertos management frames (incluidas deauthentication/disassociation robustas). Cuando está **requerido** en la SSID, clientes y AP rechazan tramas de gestión no protegidas. Esto mitiga directamente la mayoría de ataques de deauth simples.

#### 2. Usa WPA2/WPA3 y configura PMF como _required_ cuando sea posible

- WPA3 integra PMF de forma más estándar; en WPA2/enterprise se puede habilitar 802.11w. Revisa que clientes y AP soporten PMF y que la política de red lo exija.

#### 3. Monitoreo y detección continua

- Deploy de sondas en modo monitor, logging centralizado de eventos del AP, y SIEM/IDS que correlacione ráfagas de deauths y MACs sospechosas. Herramientas como nzyme o sensores Wi-Fi empresariales ayudan a detectar y alertar.

#### 4. Endurecer APs y firmwares

- Mantén firmware actualizado, desactiva características innecesarias de administración en radios públicos y usa listas blancas de BSSIDs/APs en infraestructuras críticas.

#### 5. Políticas de cliente y UX

- Evita autoconexión automática en redes abiertas; educa a usuarios para que no acepten APs con certificados/portales inesperados. En entornos sensibles, contempla el uso obligatorio de VPN para cifrar tráfico incluso después de la autenticación.

#### 6. Contención y respuesta

- Si detectas un ataque: aumentar logging, identificar MAC origen (si es posible), aislar radios afectados, notificar a personal de red y, en casos graves, cambiar SSID/chan o forzar migración a canales alternativos hasta mitigar. (estas son acciones operativas — siempre en el marco de políticas internas).

### ¿Protege PMF por completo?

PMF reduce significativamente la efectividad del deauth spoofing dirigido a clientes que soporten PMF. No obstante:

- **Compatibilidad**: algunos clientes antiguos no implementan 802.11w, por lo que hay que planificar una migración y políticas de compatibilidad.
- **Ataques físicos o a la capa física**: un atacante con potencia suficiente puede seguir causando interferencia o jamming (otro tipo de DoS) — PMF no evita jamming.

### Ejemplo ilustrativo de detección (registro simulado)

_(ejemplo ficticio para entender qué buscar — no es instrucción para atacar)_

```
2025-09-10 14:02:12 AP-1 EVENT: Deauth frame received -> src=aa:bb:cc:dd:ee:ff dest=11:22:33:44:55:66 reason=3
2025-09-10 14:02:12 AP-1 EVENT: Client 11:22:33:44:55:66 deauthenticated
2025-09-10 14:02:12 AP-1 EVENT: Client 11:22:33:44:55:66 re-association attempt
2025-09-10 14:02:13 AP-1 ALERT: Spike in deauth frames (count=120 in 30s) -> possible deauth attack
```

Al ver patrones así (muchas deauths en pocos segundos, MAC de origen no esperada), se debe activar el playbook de respuesta para redes inalámbricas. Herramientas de monitorización generan exactamente ese tipo de alertas.

### Buenas prácticas para usuarios finales

- **Evita redes Wi-Fi públicas no confiables**; si las usas, mantén VPN encendida.
- **Desactiva la conexión automática** a redes abiertas en tu dispositivo.
- **Mantén el sistema y drivers Wi-Fi actualizados.**
- **Si tu videollamada o cámara se cae repetidamente**, sospecha de interferencia o ataques de deauth y conéctate por otro medio (celular/tethering) para continuar. ([U.S. Department of War][5])

### Nota legal y ética

Describir la técnica ayuda a defenderse. La **creación, envío o reproducción de tramas de desautenticación contra redes ajenas es ilegal en muchas jurisdicciones** y puede causar daños. Usa esta información **solo** para proteger tus redes, para pruebas autorizadas en laboratorios o en ejercicios con permiso explícito.

### Resumen práctico — checklist para administradores

- [ ] Comprobar soporte PMF en APs y clientes; habilitar 802.11w/PMF (required) cuando sea posible.
- [ ] Implementar monitoreo dedicado a management frames y alertas de ráfagas de deauth.
- [ ] Mantener firmware y drivers actualizados; aplicar parches.
- [ ] Educar usuarios: no autoconectar en redes abiertas y usar VPN.
- [ ] En entornos críticos, usar WPA3/Enterprise y 802.1X, y minimizar soporte a clientes legacy.

WLC"

---

[🔼](#índice)

---

## **319. Ataques de desautenticación**

### 1. Introducción

Las redes Wi-Fi son hoy la forma más común de conectarnos a internet. Sin embargo, su diseño original incluye debilidades que los atacantes pueden explotar. Una de las más conocidas es el **ataque de desautenticación (deauthentication attack)**, que se aprovecha de cómo funcionan las tramas de gestión del estándar Wi-Fi (IEEE 802.11).

Este ataque es **fácil de ejecutar** en redes sin protecciones modernas y puede causar desde interrupciones de servicio hasta la apertura de puertas a ataques más graves como el robo de credenciales mediante un **Evil Twin**.

### 2. Ataques de desautenticación: definición y motivos

#### Definición

Un **ataque de desautenticación** consiste en enviar tramas falsas de _deauthentication_ o _disassociation_ a un cliente Wi-Fi o a un punto de acceso (AP), con el objetivo de forzar que se desconecten.

Por diseño, estas tramas son mensajes de “notificación” que dicen:

> “Ya no estás autenticado, corta la conexión”.

Históricamente no estaban cifradas ni autenticadas, lo que las hace vulnerables a la suplantación.

#### Motivos por los que un atacante los realiza

- **Denegación de servicio (DoS):** dejar a un usuario o a toda una red sin conexión.
- **Forzar reconexiones:** para capturar el **4-way handshake** (usado en auditorías de WPA/WPA2).
- **Apoyo a ataques Evil Twin:** desconectar usuarios del AP legítimo para que se conecten al AP falso del atacante.
- **Sabotaje:** cortar la comunicación de dispositivos críticos (ej. cámaras IP, sensores IoT).

### 3. ¿Cómo funcionan los ataques de desautenticación?

1. **El atacante escanea la red** y obtiene las direcciones MAC del AP y del cliente.
2. **Crea tramas falsificadas de deauthentication** indicando que provienen del AP o del cliente.
3. **Envía las tramas al aire** (ya que Wi-Fi es un medio compartido, cualquiera puede transmitir).
4. **El cliente recibe la trama y la acepta** como si fuera legítima.
5. **El cliente se desconecta del AP**, perdiendo conexión.

👉 Ejemplo ilustrativo (simplificado):

- Usuario: conectado al Wi-Fi de la oficina.
- Atacante: envía tramas de desautenticación diciendo “este AP ya no te acepta”.
- Resultado: la laptop del usuario se desconecta. Si esto se repite, el usuario no logra mantener conexión.

### 4. ¿Cómo protegerse contra ataques de desautenticación?

#### A nivel de infraestructura

- **Activar Protected Management Frames (PMF / 802.11w):** cifra y autentica tramas de gestión, evitando que las deauth falsificadas sean aceptadas.
- **Usar WPA3 siempre que sea posible:** incluye soporte obligatorio de PMF.
- **Mantener APs actualizados:** muchos fabricantes lanzan parches para fortalecer la gestión de tramas.
- **Monitoreo y detección:** sistemas de IDS inalámbricos (ej. nzyme, WIDS en controladores Wi-Fi) pueden alertar de ráfagas de deauths sospechosas.

#### A nivel de usuario

- **Preferir redes que soporten WPA3 o WPA2 con PMF habilitado.**
- **Evitar redes Wi-Fi abiertas sin cifrado.**
- **Usar VPN** en redes públicas: aunque no evita la desconexión, protege el tráfico si el usuario es redirigido a un Evil Twin.
- **Desactivar conexión automática a redes abiertas**, reduciendo la posibilidad de caer en trampas.
- **Mantener actualizado el sistema y drivers Wi-Fi.**

### 5. Conclusión

Los **ataques de desautenticación Wi-Fi** son un recordatorio de que el estándar 802.11, en sus versiones antiguas, fue diseñado sin considerar amenazas modernas. Aunque simples, siguen siendo muy usados porque permiten **interrupciones rápidas de servicio** y abren la puerta a ataques más avanzados como **Evil Twin o robo de credenciales**.

La defensa real está en **migrar a WPA3**, **habilitar PMF** en infraestructuras existentes y **educar a los usuarios** sobre las limitaciones de Wi-Fi, especialmente en redes públicas.

En resumen:

- Son ataques de denegación de servicio que explotan tramas no protegidas.
- Su impacto puede ir de molesto (cortes de conexión) a grave (phishing y robo de datos).
- La solución técnica más efectiva hoy es **PMF obligatorio en WPA2/WPA3**, complementado con **monitorización y buenas prácticas de seguridad**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 9**                                                    | **Siguiente 11**               |
| ------------------ | -------------------------------------------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./10_9_DNS_Poisoning_DNS_Spoofing_DNS_Cache_Poisoning.md) | [⏩](./10_11_Replay_Attack.md) |
