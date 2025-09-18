| **Inicio**         | **atrás 15**                | **Siguiente 17**                           |
| ------------------ | --------------------------- | ------------------------------------------ |
| [🏠](../README.md) | [⏪](./8_15_MFA_and_2FA.md) | [⏩](./8_17_Operating_System_Hardening.md) |

---

## **Índice**

| Temario                                                                                        |
| ---------------------------------------------------------------------------------------------- |
| [225. Cómo los honeypots ayudan a la seguridad](#225-cómo-los-honeypots-ayudan-a-la-seguridad) |
| [226. ¿Qué es un Honeypot?](#226-qué-es-un-honeypot)                                           |

# **Honeypots**

## **225. Cómo los honeypots ayudan a la seguridad**

![Honeypots](/img/8_Security_Skills_and_Knowledge/Honeypots.jpeg "Honeypots")

### 🐝 ¿Qué es un honeypot?

Un **honeypot (tarro de miel)** es un sistema o recurso **diseñado intencionalmente para atraer a atacantes**, simulando ser una parte vulnerable de la red o aplicación, con el objetivo de:

- Detectar ataques.
- Analizar técnicas de los atacantes.
- Desviar amenazas de los sistemas reales.

👉 Piensa en un honeypot como una **trampa digital** que aparenta ser un objetivo fácil.

### 📖 Definición de un honeypot

En términos técnicos:

Un **honeypot es un sistema de seguridad que imita vulnerabilidades o servicios atractivos para los atacantes**, pero que no tiene un propósito real para la organización, más allá de registrar y monitorear actividades maliciosas.

### ⚙️ Cómo funcionan los honeypots

1. **Implementación**: se coloca en una red o servidor simulando un servicio real (ejemplo: un servidor web con puertos abiertos o una base de datos falsa).
2. **Atracción**: el honeypot aparenta estar mal configurado o poco protegido.
3. **Interacción**: cuando un atacante lo explora o intenta explotarlo, el honeypot registra cada acción.
4. **Monitoreo y análisis**: los datos se almacenan y analizan para entender técnicas de ataque, malware, exploits y patrones de comportamiento.

### 🛠 Diferentes tipos de honeypot y cómo funcionan

Existen varios niveles según su complejidad e interacción:

1. **Baja interacción**

   - Simula servicios o sistemas básicos (ejemplo: un servidor SSH que solo acepta conexión).
   - Bajo riesgo porque no ejecuta código real.
   - Útil para detectar intentos de escaneo y fuerza bruta.

2. **Alta interacción**

   - Emula sistemas completos (ejemplo: un servidor Linux o Windows vulnerable real).
   - Permite al atacante interactuar como si fuera un sistema genuino.
   - Genera más datos, pero con mayor riesgo si no está bien aislado.

3. **Honeypots de investigación**

   - Usados en laboratorios o universidades.
   - Analizan malware, ataques avanzados y nuevas técnicas.

4. **Honeypots de producción**

   - Usados en empresas.
   - Sirven para distraer atacantes y proteger los activos reales.

5. **Honeynets**

   - Una red entera de honeypots conectados.
   - Simulan una infraestructura completa para estudiar ataques coordinados.

### ✅ Beneficios de usar honeypots

- **Detección temprana de ataques**.
- **Análisis de comportamiento del atacante**: técnicas, herramientas, exploits.
- **Protección indirecta**: desvían la atención de los sistemas críticos.
- **Mejora en la inteligencia de amenazas (Threat Intelligence)**.
- **Entrenamiento**: útiles para capacitar equipos de ciberseguridad.

### ⚠️ Peligros de los honeypots

- **Riesgo de compromiso**: si un honeypot de alta interacción no está bien aislado, un atacante puede usarlo como puente hacia la red real.
- **Recurso limitado**: solo detecta ataques dirigidos a él, no los que ignoran el honeypot.
- **Requiere mantenimiento constante**: si se descuida, puede volverse una puerta de entrada.
- **Posibles problemas legales**: en algunos países, registrar ciertas actividades de atacantes puede tener implicaciones legales.

---

[🔼](#índice)

---

## **226. ¿Qué es un Honeypot?**

### ¿Qué es un **honeypot**?

Un **honeypot** (literalmente “tarro de miel”) es un sistema, servicio o recurso **intencionadamente creado para parecer vulnerable o valioso** con el fin de **atraer, detectar y estudiar actividades maliciosas**. No sirve para proveer un servicio real a la organización: su propósito es ser un cebo que permite recopilar evidencia y aprender cómo atacan los adversarios.

### ¿Para qué sirve?

- **Detectar** actividad maliciosa temprana (escaneos, fuerza bruta, intentos de explotación).
- **Analizar** técnicas, herramientas y malware usados por atacantes.
- **Desviar** la atención de activos reales (efecto “cebo”).
- **Generar inteligencia de amenazas** (IoC: IPs, hashes, payloads, TTPs).
- **Entrenamiento** y pruebas para equipos de seguridad.

### ¿Cómo funciona? (flujo típico)

1. **Se despliega** el honeypot en la red simulando un servicio (ej.: SSH, web, base de datos, SCADA).
2. El honeypot **expone puertos/servicios** que parecen vulnerables.
3. Los atacantes (o bots) **escanean** la red y encuentran el honeypot.
4. Cuando interactúan, **todas sus acciones quedan registradas**: conexiones, comandos, payloads, ficheros subidos, paquetes.
5. Los analistas **revisan los registros** para entender el ataque y adaptar defensas (blocklists, reglas IDS, parches).

> El honeypot no debería contener datos reales ni credenciales válidas; es un entorno controlado para observación.

### Tipos principales (y ejemplos prácticos)

#### 1. Baja interacción

- Emula respuestas simples (p. ej. simular un servidor que responde a pings o acepta conexiones pero no ejecuta servicios reales).
- **Ventaja:** bajo riesgo y fácil de mantener.
- **Uso típico:** detectar escaneos y fuerza bruta masiva.
- **Ejemplo conceptual:** un puerto SSH que acepta la conexión y registra intentos de login, pero no deja acceso real.

#### 2. Media/Alta interacción

- Ejecuta servicios reales o entornos más completos (shells limitadas, servicios web reales).
- **Ventaja:** captura comportamiento del atacante con mucho detalle (comandos, herramientas ejecutadas, payloads).
- **Riesgo:** si no está aislado, el atacante podría usarlo para pivotar hacia la red real.
- **Software conocido:** _Cowrie_ (honeypot SSH/Telnet que emula shell), _Dionaea_ (captura malware), _Conpot_ (ICS/SCADA honeypot).

#### 3. Honeynet

- Una **red completa** de honeypots que simula una infraestructura entera (varios hosts, servicios, routers).
- **Uso:** estudiar ataques avanzados y movimientos laterales.

#### 4. Honeytokens / Canarytokens

- No son sistemas completos: son **señuelos digitales** (archivos, URLs, credenciales falsas) que alertan si alguien los usa.
- **Uso:** detectar filtración de datos o accesos internos no autorizados.

#### 5. Honeypots comerciales / “Canaries”

- Dispositivos o servicios listos para usar (ej.: Thinkst Canary) que levantan alarmas cuando se detecta interacción.

### Ejemplos reales y escenarios de uso

1. **SSH-brute honeypot (producción):**

   - Objetivo: identificar credenciales usadas en ataques automáticos.
   - Resultado típico: cientos de intentos automatizados con listas de usuario/contraseña; se bloquean IPs y se actualizan reglas de firewall.

2. **Captura de malware (investigación):**

   - Honeypot que simula servicios vulnerables y captura binarios que intentan propagarse.
   - Analistas obtienen muestras para análisis estático/dinámico y generación de indicadores (hashes, domains).

3. **Honeypot ICS (industrial):**

   - Se simula un PLC o SCADA para monitorizar intentos de intrusión en sistemas industriales.
   - Útil para detectar escaneos dirigidos a infraestructuras críticas.

4. **Honeytoken en un bucket S3 falso:**

   - Si alguien accede al enlace o descarga el archivo trampa, se genera una alerta inmediata.

### ¿Qué tipo de datos captura un honeypot?

- Dirección IP de origen y puertos.
- Timestamps y secuencia de eventos.
- Payloads y ficheros subidos (muestras de malware).
- Comandos ingresados por el atacante (en honeypots con shell emulado).
- Tráfico de red (pcaps) y metadatos.
- Huellas del atacante (user-agents, cadenas utilizadas por herramientas).

_(Estos datos alimentan SIEM/IDS para crear reglas y bloquear amenazas.)_

### Arquitectura y buenas prácticas (alto nivel)

- **Aislamiento estricto:** colocar el honeypot en VLAN separada o red “de cebo”, sin acceso directo a sistemas críticos.
- **Control de salida (egress):** bloquear o filtrar tráfico saliente para evitar que el honeypot sea usado para atacar a terceros o pivotar.
- **Registro centralizado:** enviar logs y pcaps a un servidor de análisis fuera del entorno del honeypot.
- **No colocar datos reales:** nunca almacenar credenciales reales ni información sensible.
- **Alertas en tiempo real:** integrar con SIEM para notificaciones.
- **Rotación y realismo:** actualizar los señuelos para que parezcan reales (servicios, banners, archivos).
- **Forensic readiness:** conservar pruebas con cadena de custodia para análisis posterior.

### Beneficios

- **Visibilidad** sobre amenazas reales dirigidas a tu entorno.
- **Inteligencia** para mejorar detección y bloqueo.
- **Ahorro en detección temprana** (muchas intrusiones se detectan primero en honeypots).
- **Entrenamiento** para equipos de SOC y respuesta a incidentes.

### Peligros y limitaciones

- **Riesgo de pivoting:** si no está correctamente aislado, un atacante puede usarlo para entrar a la red real.
- **Falsa sensación de seguridad:** los honeypots no detectan ataques que no los tocan; son complementarios a IDS/EDR.
- **Detección por parte del atacante:** adversarios sofisticados pueden identificar y evitar honeypots (fingerprinting).
- **Coste y mantenimiento:** requieren monitoreo, actualización y análisis de datos.
- **Implicaciones legales y de privacidad:** hay que revisar legislación local sobre captura y retención de datos de atacantes.

### Ejemplo ilustrativo (registro ficticio y simplificado)

```
2025-09-01T02:14:03Z SRC=203.0.113.45 DST=10.10.10.5 service=ssh action=login_attempt username=root password=admin123 result=failed
2025-09-01T02:14:17Z SRC=203.0.113.45 DST=10.10.10.5 service=ssh action=login_attempt username=admin password=123456 result=success
2025-09-01T02:15:02Z session=xyz shell_command="cat /etc/passwd" captured_file=/samples/etc_passwd.txt
```

> Con esos registros un analista puede: bloquear la IP, extraer las credenciales probadas, descargar y analizar cualquier fichero subido, y crear reglas en el IDS.

### Consideraciones legales y éticas

- **Revisa la legislación local** sobre interceptación de tráfico y retención de datos.
- **No uses honeypots para “atrapar” de forma que pueda considerarse entrapment** (esto varía por jurisdicción).
- **Protege la privacidad** y evita almacenar datos personales de terceros sin justificación legal.

### Resumen rápido

- Un honeypot es un **cebo controlado** para atraer y estudiar atacantes.
- **Útil** para detección, inteligencia y entrenamiento; **risky** si no se aísla correctamente.
- Existen desde implementaciones sencillas (low-interaction) hasta redes completas (honeynets) y honeytokens ligeros.
- **Complementan** otras medidas (IDS, EDR, firewalls), no las sustituyen.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 15**                | **Siguiente 17**                           |
| ------------------ | --------------------------- | ------------------------------------------ |
| [🏠](../README.md) | [⏪](./8_15_MFA_and_2FA.md) | [⏩](./8_17_Operating_System_Hardening.md) |
