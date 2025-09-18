| **Inicio**         | **atrás 10**                       | **Siguiente 12**              |
| ------------------ | ---------------------------------- | ----------------------------- |
| [🏠](../README.md) | [⏪](./9_10_Social_Engineering.md) | [⏩](./9_12_Impersonation.md) |

---

## **Índice**

| Temario                                                                                              |
| ---------------------------------------------------------------------------------------------------- |
| [287. ¿Qué es el reconocimiento cibernético?](#287-qué-es-el-reconocimiento-cibernético)             |
| [288. ¿Qué es el reconocimiento de ciberseguridad?](#288-qué-es-el-reconocimiento-de-ciberseguridad) |

# **Reconocimiento**

## **287. ¿Qué es el reconocimiento cibernético?**

![Reconocimiento](/img/9_Attack_Types_and_Differences/reconocimiento_cibernetico.jpg "Reconocimiento")

El **reconocimiento cibernético** es el proceso de **recopilar información sobre un objetivo digital (empresa, sistema, red o persona)** antes de lanzar un ataque.

Su objetivo es descubrir **puntos débiles** que puedan explotarse después.

👉 En términos simples: es como espiar una casa antes de entrar, observando ventanas abiertas, cámaras de seguridad o la rutina de los dueños.

### 📜 Breve historia del reconocimiento cibernético

- **Años 80–90**: el reconocimiento se hacía de forma manual con escaneos básicos de red y llamadas (_war dialing_).
- **Años 2000**: aparecen herramientas automatizadas como Nmap, que permiten mapear redes enteras en minutos.
- **Hoy**: el reconocimiento combina técnicas automáticas (OSINT, escaneo de vulnerabilidades, fingerprinting) y sociales (ingeniería social, redes sociales, perfiles profesionales).

Los grupos de **Amenaza Persistente Avanzada (APT)** suelen invertir semanas o meses en reconocimiento antes de atacar.

### ⚙️ Entendiendo cómo funciona el reconocimiento cibernético

El proceso de reconocimiento puede dividirse en dos fases principales:

#### 1. **Reconocimiento pasivo**

- El atacante observa sin interactuar directamente con el objetivo.
- Ejemplos:

  - Revisar redes sociales (LinkedIn, Facebook, Twitter).
  - Buscar documentos públicos (PDFs con metadatos).
  - Analizar el sitio web de la empresa y su infraestructura pública (subdominios, DNS).
  - Consultar filtraciones en la dark web.

👉 Ejemplo: encontrar en LinkedIn al equipo de finanzas para después enviar spear phishing dirigido.

#### 2. **Reconocimiento activo**

- El atacante interactúa directamente con los sistemas del objetivo, lo que aumenta el riesgo de ser detectado.
- Ejemplos:

  - Escaneo de puertos (Nmap, Masscan).
  - Identificación de servicios activos (FTP, SSH, RDP).
  - Fingerprinting de versiones de software (qué sistema operativo o aplicación usan).
  - Escaneo de vulnerabilidades (Nessus, OpenVAS).

👉 Ejemplo: descubrir que un servidor corre un **Apache desactualizado** vulnerable a un exploit.

### 💡 Casos de uso del reconocimiento cibernético

#### ✅ Usos legítimos (defensivos)

- **Pentesting**: los auditores hacen reconocimiento para encontrar debilidades antes que los atacantes.
- **Red team**: simular ataques reales contra una empresa.
- **OSINT corporativo**: entender qué información pública de la organización podría ser usada contra ella.

#### ❌ Usos maliciosos (ofensivos)

- **Cibercriminales**: preparar ataques de phishing, ransomware o robo de datos.
- **APT patrocinadas por estados**: espionaje digital y sabotaje.
- **Hacktivistas**: exponer información de empresas o gobiernos.

### 🛡️ Cómo pueden las empresas protegerse del ciberreconocimiento

#### 1. **Reducir la huella digital**

- Limitar información pública en la web y redes sociales.
- Eliminar metadatos de documentos antes de publicarlos.
- Minimizar la exposición de servicios en internet (VPNs, puertos abiertos innecesarios).

#### 2. **Detección temprana**

- Usar sistemas de detección de intrusiones (IDS/IPS) para identificar escaneos sospechosos.
- Monitorear logs y tráfico de red en busca de patrones anómalos.
- Implementar threat intelligence para saber si se habla de la empresa en foros oscuros.

#### 3. **Endurecimiento de sistemas**

- Actualizar software y sistemas operativos.
- Aplicar el principio de **mínimo privilegio**.
- Deshabilitar servicios que no se utilicen.

#### 4. **Concienciación del personal**

- Capacitar a empleados en **ingeniería social** (ej. no revelar información en llamadas sospechosas).
- Simulaciones de phishing y pretexting.

### 📌 Conclusión

El **reconocimiento cibernético** es una fase crítica, ya sea para **atacar** o para **defender**.

Las organizaciones que entienden cómo los atacantes recopilan información pueden **cerrar brechas antes de que sean explotadas**.
La clave es reducir la huella digital, vigilar continuamente y entrenar a las personas.

### ❓ Preguntas frecuentes sobre reconocimiento cibernético

**1. ¿Es ilegal el reconocimiento cibernético?**

👉 Depende. Si se hace sin autorización, puede considerarse intrusión. Si es parte de una auditoría autorizada, es legal.

**2. ¿Cuánto dura la fase de reconocimiento en un ataque?**

👉 Puede durar desde horas hasta meses, según la complejidad del objetivo.

**3. ¿Qué diferencia hay entre reconocimiento pasivo y activo?**

👉 Pasivo: no interactúa directamente con los sistemas. Activo: sí lo hace (ej. escaneo de puertos).

**4. ¿Cómo saber si alguien me está espiando digitalmente?**

👉 Monitoreando logs de red, buscando escaneos inusuales, detectando patrones de tráfico sospechoso.

**5. ¿Cuál es la mejor defensa contra el reconocimiento?**

👉 Reducir la información expuesta y monitorear continuamente los accesos a tu infraestructura.

---

[🔼](#índice)

---

## **288. ¿Qué es el reconocimiento de ciberseguridad?**

El **reconocimiento en ciberseguridad** es la fase inicial de un ataque o auditoría, en la que se recopila información sobre el objetivo (redes, sistemas, usuarios, servicios expuestos) para identificar posibles puntos de entrada.

👉 Es como el “espionaje previo” que un ladrón hace antes de entrar a una casa.

### 🧭 Tipos de reconocimiento cibernético: pasivo vs. activo

#### 🔹 Reconocimiento pasivo

- El atacante obtiene información sin interactuar directamente con los sistemas de la víctima.
- Menos riesgoso, más difícil de detectar.
- Ejemplos:

  - Buscar en redes sociales información de empleados.
  - Analizar metadatos de documentos públicos.
  - Consultar bases de datos de filtraciones.

#### 🔹 Reconocimiento activo

- El atacante interactúa directamente con los sistemas del objetivo.
- Más preciso, pero con mayor riesgo de ser detectado.
- Ejemplos:

  - Escaneo de puertos y servicios con **Nmap**.
  - Fingerprinting del sistema operativo.
  - Escaneo de vulnerabilidades con **Nessus** o **OpenVAS**.

### 📊 Tabla comparativa: reconocimiento pasivo vs. activo

| Aspecto                 | Reconocimiento Pasivo 🕵️            | Reconocimiento Activo ⚡                                 |
| ----------------------- | ----------------------------------- | -------------------------------------------------------- |
| **Interacción**         | Ninguna o mínima                    | Directa con los sistemas                                 |
| **Riesgo de detección** | Muy bajo                            | Alto (puede activar alarmas)                             |
| **Precisión**           | Limitada (información pública)      | Alta (detalles técnicos del sistema)                     |
| **Fuentes comunes**     | OSINT, redes sociales, WHOIS, leaks | Escaneo de puertos, fingerprinting, pruebas de servicios |
| **Ejemplo**             | Encontrar emails en LinkedIn        | Detectar un servidor FTP sin contraseña                  |

### 🌐 Reconocimiento de red: un subconjunto crítico

El **reconocimiento de red** se centra en descubrir cómo está estructurada la red del objetivo.

Incluye:

- **Identificación de hosts**: descubrir qué máquinas están activas.
- **Mapeo de puertos**: saber qué servicios están expuestos.
- **Fingerprinting de sistemas operativos**: identificar qué SO se está ejecutando.
- **Topología de red**: cómo se conectan los distintos nodos.

👉 Ejemplo: descubrir que un servidor expone **puerto 3389 (RDP)**, lo que puede llevar a ataques de fuerza bruta o explotación de vulnerabilidades.

### 🛠️ Herramientas y técnicas utilizadas en el reconocimiento

#### 🔹 Pasivo

- **Shodan** → buscador de dispositivos expuestos en internet.
- **Google Dorking** → búsquedas avanzadas para encontrar datos ocultos.
- **theHarvester** → recopila correos y dominios.
- **Recon-ng** → framework OSINT.

#### 🔹 Activo

- **Nmap / Masscan** → escaneo de puertos y servicios.
- **Nessus / OpenVAS** → escaneo de vulnerabilidades.
- **WhatWeb / Wappalyzer** → identificar tecnologías web.
- **Nikto** → detectar fallos en aplicaciones web.

### 🛡️ Defensa contra el reconocimiento en ciberseguridad

1. **Reducir la huella digital**

   - Publicar menos información en la web corporativa y redes sociales.
   - Quitar metadatos de documentos antes de compartirlos.
   - Minimizar servicios expuestos en internet.

2. **Monitoreo y detección temprana**

   - Usar **IDS/IPS** para detectar escaneos sospechosos.
   - Analizar logs de red en busca de patrones anómalos.
   - Integrar **threat intelligence** para detectar filtraciones de datos.

3. **Endurecimiento de sistemas**

   - Actualizar software y aplicar parches.
   - Deshabilitar puertos y servicios innecesarios.
   - Configurar firewalls para bloquear escaneos.

4. **Capacitación del personal**

   - Evitar filtraciones por ingeniería social.
   - Simulaciones de phishing para entrenar al personal.

### 🛡️ Cómo Cymulate ayuda a protegerse del reconocimiento

**Cymulate** es una plataforma de **BAS (Breach and Attack Simulation)** que permite:

- Simular ataques de reconocimiento contra una empresa.
- Identificar qué información está expuesta públicamente.
- Probar defensas contra escaneos y técnicas OSINT.
- Recibir reportes de riesgos y recomendaciones para corregirlos.

👉 En la práctica: una empresa puede usar Cymulate para descubrir **qué vería un atacante en su fase de reconocimiento** y reforzar sus defensas antes de un ataque real.

### 📌 Conclusiones clave

- El **reconocimiento cibernético** es la primera fase de un ataque: recolecta información crítica del objetivo.
- Existen dos enfoques: **pasivo** (menos riesgoso, más oculto) y **activo** (más preciso pero más detectable).
- El **reconocimiento de red** es fundamental para descubrir hosts, puertos y servicios vulnerables.
- Se utilizan tanto técnicas de **OSINT** como escaneos activos con herramientas específicas.
- Las defensas incluyen reducir la huella digital, monitoreo continuo, endurecimiento de sistemas y capacitación.
- Herramientas como **Cymulate** ayudan a simular y detectar posibles exposiciones antes de que los atacantes las exploten.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 10**                       | **Siguiente 12**              |
| ------------------ | ---------------------------------- | ----------------------------- |
| [🏠](../README.md) | [⏪](./9_10_Social_Engineering.md) | [⏩](./9_12_Impersonation.md) |
