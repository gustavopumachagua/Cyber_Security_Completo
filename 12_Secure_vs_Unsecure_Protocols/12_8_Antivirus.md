| **Inicio**         | **atrás 7**            | **Siguiente 9**              |
| ------------------ | ---------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./12_7_S_MIME.md) | [⏩](./12_9_Anti_malware.md) |

---

## **Índice**

| Temario                                                                                                        |
| -------------------------------------------------------------------------------------------------------------- |
| [371. ¿Qué es el software antivirus?](#371-qué-es-el-software-antivirus)                                       |
| [372. ¿Qué es un antivirus y cómo nos mantiene seguros?](#372-qué-es-un-antivirus-y-cómo-nos-mantiene-seguros) |

# **Antivirus**

## **371. ¿Qué es el software antivirus?**

![Antivirus](/img/12_Secure_vs_Unsecure_Protocols/Antivirus.webp "Antivirus")

El **software antivirus** es un programa de seguridad que protege tu computadora, servidor o dispositivo móvil contra **malware** (virus, troyanos, ransomware, spyware, gusanos, rootkits, etc.).
Su función principal es **detectar, bloquear y eliminar software malicioso** antes de que cause daño.

👉 Ejemplo: Si descargas un archivo infectado con un troyano desde un correo, el antivirus lo detecta y lo elimina/quarantena.

### ¿Cómo puede ayudarle a protegerse?

El antivirus protege en varios frentes:

- **Escaneo en tiempo real**: monitorea archivos, descargas y programas mientras se ejecutan.
- **Bloqueo de sitios web maliciosos**: evita que entres a páginas con phishing o malware.
- **Protección de correo electrónico**: analiza adjuntos y enlaces sospechosos.
- **Detección de comportamiento extraño**: alerta si un programa se comporta como malware.

👉 Ejemplo: Abres una memoria USB infectada → el antivirus detecta automáticamente el malware e impide que se ejecute.

### ¿Por qué necesito un software antivirus?

- Los ataques cibernéticos aumentan cada año.
- Incluso usuarios cuidadosos pueden caer en un **phishing** o descargar un archivo infectado.
- Los sistemas sin antivirus quedan expuestos a:

  - **Ransomware** (bloqueo de archivos hasta pagar rescate).
  - **Robo de contraseñas**.
  - **Uso del equipo como botnet** para ataques DDoS.

👉 Sin antivirus, basta abrir un adjunto malicioso para que un atacante tome el control de tu equipo.

### ¿Qué hace el software antivirus?

Funciones principales:

1. **Escaneo de archivos y programas** (manual y automático).
2. **Protección en tiempo real** (detecta amenazas al instante).
3. **Cuarentena** (aísla archivos sospechosos sin borrarlos).
4. **Eliminación de malware** (limpia el sistema).
5. **Actualizaciones constantes** de definiciones de virus y motores de detección.

### ¿Cuáles son los beneficios del software antivirus?

- Seguridad frente a virus y malware.
- Navegación más segura (bloqueo de webs peligrosas).
- Protección de datos personales y bancarios.
- Tranquilidad en el uso de correos, descargas y memorias USB.
- Evita pérdidas económicas y de productividad por infecciones.

👉 En empresas, reduce el riesgo de fuga de datos y sanciones legales por incumplimiento de normativas.

### ¿Cómo funciona el software antivirus?

El antivirus usa varios métodos de detección:

- **Basado en firmas**: compara archivos con una base de datos de virus conocidos.
- **Heurística**: analiza el código en busca de patrones sospechosos, incluso de malware desconocido.
- **Monitorización de comportamiento**: detecta programas que actúan de forma anómala (ej. intentan cifrar todos tus archivos como un ransomware).
- **Sandboxing**: ejecuta programas en un entorno aislado para probar si son maliciosos.

👉 Ejemplo: Un nuevo ransomware sin firma conocida aparece. Aunque no esté en la base de datos, el antivirus detecta que intenta cifrar archivos masivamente y lo bloquea.

### Antivirus gratuito vs. de pago

- **Gratuito**

  - Pros: básico, suficiente para usuarios poco expuestos.
  - Contras: menos funciones (sin protección avanzada de correo, navegación o firewall).

- **De pago**

  - Pros: incluye protecciones avanzadas (ransomware, phishing, firewall, control parental, VPN).
  - Contras: requiere suscripción.

👉 Ejemplo:

- Usuario doméstico → puede usar **Windows Defender** (gratis, ya integrado).
- Empresa con datos sensibles → conviene un **antivirus de pago con administración centralizada**.

### ¿Dónde puedo conseguir software antivirus para mi computadora?

- **Opciones gratuitas confiables**:

  - Windows Defender (ya integrado en Windows 10/11).
  - Avast Free, AVG Free, Avira Free.

- **Opciones de pago populares**:

  - Bitdefender, Kaspersky, ESET, Norton, McAfee, Trend Micro.
  - En empresas: Sophos, CrowdStrike, SentinelOne.

👉 Importante: **descargar siempre desde la página oficial del fabricante**, nunca desde enlaces sospechosos.

✅ **En resumen**:

Un antivirus es esencial para mantener tu computadora protegida frente a malware, robo de datos y fraudes. Los gratuitos ofrecen protección básica, mientras que los de pago añaden seguridad avanzada para usuarios y empresas.

---

[🔼](#índice)

---

## **372. ¿Qué es un antivirus y cómo nos mantiene seguros?**

### ¿Qué es un antivirus?

Un **antivirus** (o más ampliamente: software anti-malware) es un programa diseñado para **detectar, bloquear, aislar y eliminar software malicioso** (malware) — virus, troyanos, gusanos, ransomware, spyware, rootkits, adware y otras amenazas. Hoy en día las soluciones incluyen también capacidades avanzadas como detección por comportamiento, análisis en la nube y respuesta a incidentes (EDR).

### ¿Qué funciones realiza para protegernos?

Un antivirus moderno ofrece varias funciones clave:

- **Protección en tiempo real**: inspecciona archivos y procesos cuando se abren, ejecutan o descargan.
- **Escaneo bajo demanda**: análisis manual o programado (rápido/completo) sobre archivos y discos.
- **Escaneo de correo y web**: bloquea adjuntos maliciosos y URLs de phishing.
- **Cuarentena**: aísla archivos sospechosos para evitar ejecución y permitir análisis posterior.
- **Eliminación / remediación**: limpia o repara archivos infectados cuando es posible.
- **Actualizaciones automáticas**: descarga firmas y reglas nuevas para detectar amenazas emergentes.
- **Monitorización de comportamiento**: detecta acciones sospechosas (ej. procesos que cifran masivamente archivos).
- **Sandboxing / análisis dinámico**: ejecuta muestras en entorno seguro para observar su comportamiento.
- **Integración en la nube / ML**: envía metadatos para análisis centralizado y usa modelos de ML para detecciones sin firmas.
- **Registro y alertas**: guarda eventos y notifica al usuario o al equipo de seguridad.

### ¿Cómo detecta el malware? (métodos principales)

1. **Basado en firmas**

   - Compara archivos con una base de datos de “hashes” o patrones de malware conocido. Rápido y fiable para amenazas conocidas.

2. **Heurística**

   - Analiza el código buscando patrones sospechosos (ej.: rutinas de modificación del sistema) para identificar variantes nuevas.

3. **Detección por comportamiento**

   - Vigila acciones en tiempo real (creación masiva de archivos, modificación de boot sector, conexiones anómalas) y bloquea si se detecta conducta maliciosa.

4. **Sandbox / Análisis dinámico**

   - Ejecuta la muestra en un entorno aislado para ver qué hace realmente antes de permitirla en el sistema.

5. **Inteligencia en la nube / Machine Learning**

   - Compara metadatos y comportamientos con grandes repositorios y modelos que detectan amenazas emergentes sin necesidad de una firma exacta.

6. **Reputación**

   - Basado en la reputación del archivo/URL (si millones de usuarios lo han etiquetado como malicioso).

### Ejemplos concretos (escenarios y respuesta del antivirus)

- **Adjunto malicioso en e-mail**: el motor de correo analiza el adjunto, lo marca y lo mueve a cuarentena antes de que el usuario lo ejecute.
- **Ransomware que intenta cifrar archivos**: la detección por comportamiento detecta la escritura masiva y bloquea el proceso; el antivirus restaura archivos desde copias locales (si dispone de la función).
- **Descarga desde web con exploit**: el filtro web bloquea la URL; si el exploit llega, el sandbox lo analiza y lo detiene.
- **USB infectado**: el escáner en tiempo real analiza automáticamente archivos al conectar la unidad y evita la propagación.

### Ejemplos de uso (comandos reales)

(útiles para administradores o pruebas)

**Windows Defender (PowerShell)** — comandos útiles:

```powershell
# Actualizar firmas
Update-MpSignature

# Iniciar un escaneo rápido
Start-MpScan -ScanType QuickScan

# Iniciar un escaneo completo
Start-MpScan -ScanType FullScan

# Consultar estado
Get-MpComputerStatus
```

**ClamAV (Linux) — ejemplo básico**:

```bash
# actualizar base de firmas
sudo freshclam

# escanear un directorio recursivamente
clamscan -r /home/usuario

# escanear y mover archivos infectados a cuarentena
clamscan -r --move=/ruta/quarantine /home/usuario
```

### Limitaciones y qué NO puede hacer un antivirus por sí solo

- **No reemplaza buenas prácticas**: si ejecutas manualmente un malware nuevo (social engineering) puede ser difícil de evitar.
- **No detiene todas las amenazas 0-day** (sin firma) inmediatamente — por eso las capas heurística y comportamental son esenciales.
- **Falsos positivos**: pueden marcar software legítimo como malicioso (hay que revisar y, si procede, reportar/excluir).
- **Privacidad y permisos**: el antivirus necesita acceso profundo al sistema; elige proveedores reputados.
- **Ataques dirigidos complejos**: pueden requerir EDR, respuesta manual y análisis forense, no sólo antivirus tradicional.

### Antivirus gratuito vs de pago (breve)

- **Gratuitos**: protección básica en tiempo real y escaneos. Buen para usuarios domésticos con conducta segura.
- **De pago / enterprise**: funciones avanzadas (protección contra ransomware, EDR, administración centralizada, DLP, análisis en la nube, soporte). Más apropiado para empresas.

### Buenas prácticas para maximizar la protección

- Mantén **actualizaciones automáticas** del antivirus y del sistema operativo.
- Activa **protección en tiempo real** y **escaneos programados** (ej. semanal completo).
- Haz **copias de seguridad regulares** y verifica restauraciones (defensa contra ransomware).
- No des permisos de administrador innecesarios; aplica **principio de menor privilegio**.
- Evita abrir adjuntos sospechosos y verifica enlaces (formación contra phishing).
- Complementa antivirus con **firewall**, **actualizaciones de software**, **filtrado web** y (si eres empresa) **EDR/siem**.
- Revisa logs y responde a alertas con un playbook (aislar la máquina, forense, restauración, cambio de credenciales).

### ¿Qué hacer si el antivirus detecta una amenaza?

1. **No entrar en pánico**. Leer la alerta.
2. **Aislar el equipo** (desconectar red) si se sospecha compromiso grave (ransomware).
3. **Cuarentenar** y/o eliminar el archivo según recomiende el AV.
4. Ejecutar un **escaneo completo** y un análisis con herramientas de rescate si persiste.
5. Restaurar archivos desde copia de seguridad si es necesario.
6. Cambiar contraseñas (especialmente si hubo posible exfiltración).
7. Si es empresa, activar respuesta a incidentes y realizar análisis forense.

### Recomendaciones finales (rápidas)

- Usa una solución de un **proveedor confiable**.
- En entornos empresariales, prioriza soluciones con EDR y administración centralizada.
- Combina antivirus con **copias de seguridad**, **concienciación de usuarios**, y **parches automáticos**.
- Prueba regularmente (simulacros, phishing tests) y mantén documentación de recuperación.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**            | **Siguiente 9**              |
| ------------------ | ---------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./12_7_S_MIME.md) | [⏩](./12_9_Anti_malware.md) |
