| **Inicio**         | **atrás 2**             | **Siguiente 4**         |
| ------------------ | ----------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./9_2_Whishing.md) | [⏩](./9_4_Smishing.md) |

---

## **Índice**

| Temario                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------ |
| [269. ¿Qué es un ataque de Whaling?](#269-qué-es-un-ataque-de-whaling)                                                         |
| [270. ¿Qué es un ataque de Whaling y cómo mantenerse protegida?](#270-qué-es-un-ataque-de-whaling-y-cómo-mantenerse-protegida) |

# **Whaling**

## **269. ¿Qué es un ataque de Whaling?**

![Whaling](/img/9_Attack_Types_and_Differences/Whaling.jpg "Whishin")

Un **ataque de ballenas** (también llamado **whaling attack**) es una **variante de spear phishing** enfocada en los "grandes peces" de una organización, es decir: **directores, gerentes, CEOs, CFOs u otros altos cargos con acceso a información sensible y poder de decisión financiera**.

👉 Se le llama así porque las víctimas son "las ballenas" en la jerarquía corporativa: pocas, grandes y muy valiosas.

### ⚙️ Cómo funcionan los ataques de ballenas

1. **Investigación previa (reconocimiento)**

   - El atacante estudia al ejecutivo: redes sociales, comunicados de prensa, LinkedIn, información de la web corporativa.
   - El objetivo es conocer su rol, estilo de comunicación y relaciones internas.

2. **Suplantación convincente**

   - Se envía un correo o mensaje que parece legítimo, muchas veces suplantando a un proveedor, socio de negocios o incluso a otro directivo.
   - El lenguaje se adapta al perfil del ejecutivo (formal, sin errores, con detalles reales).

3. **Ingeniería social con urgencia y autoridad**

   - El mensaje suele pedir:

     - Autorización de una transferencia bancaria.
     - Descarga de un documento importante (malicioso).
     - Acceso a información confidencial.

4. **Acción de la víctima**

   - El directivo, confiando en la aparente legitimidad, hace la transferencia, comparte información o descarga el archivo.

5. **Explotación**

   - El atacante roba dinero, datos sensibles (ej. planes estratégicos, listas de clientes) o abre la puerta a un ataque mayor (ransomware, espionaje).

### 📧 Ejemplo realista de Whaling

**Correo falso dirigido a un CFO (Director Financiero):**

```
De: ceo@empresa-corporativa.com
Para: cfo@empresa-corporativa.com
Asunto: Transferencia urgente

Hola Juan,

Necesitamos completar la transferencia de $250,000 al nuevo proveedor antes de las 17:00,
es parte del acuerdo con el socio estratégico europeo.

Por favor, realiza la operación cuanto antes y confirma cuando esté completada.
El contrato lo discutiremos en la reunión de mañana.

Gracias por tu apoyo,
Carlos Gómez
CEO
```

👉 Este correo puede parecer legítimo, pero en realidad el remitente puede ser `ceo@empresaa-corporativa.com` (con doble “a”), o incluso una cuenta comprometida.

### 🛡️ Cómo protegerse de los ataques de ballenas

#### Para individuos (ejecutivos):

- **Verificar siempre la solicitud**: si recibes un pedido de transferencia o acceso urgente, confirma por otro canal (llamada, reunión).
- **Cuidado con dominios falsos**: revisar bien la dirección de correo, no solo el nombre visible.
- **Desconfiar de la urgencia extrema**: los atacantes presionan para que actúes rápido.

#### Para empresas:

- **Políticas de doble verificación**:

  - Ninguna transferencia grande debería hacerse solo con un correo.
  - Se requiere confirmación adicional (llamada, sistema interno, doble firma).

- **Autenticación multifactor (MFA)** en correos y sistemas financieros.
- **Filtros de correo (SPF, DKIM, DMARC)** para evitar suplantaciones.
- **Concienciación y simulacros**: entrenar a directivos en ingeniería social.
- **Monitoreo de fraudes financieros**: alertas automáticas de transferencias sospechosas.

### 📌 Resumen rápido

- **Phishing normal** → correos masivos a cualquiera.
- **Spear phishing** → ataque dirigido a una persona específica.
- **Whaling (ballenas)** → spear phishing contra ejecutivos o altos directivos, con el fin de obtener **dinero, información crítica o acceso privilegiado**.

---

[🔼](#índice)

---

## **270. ¿Qué es un ataque de Whaling y cómo mantenerse protegida?**

Un **ataque de Whaling** es un tipo de **phishing altamente dirigido** que busca engañar a **altos ejecutivos o directivos** (CEO, CFO, gerentes de finanzas, directores legales, etc.).

Se le llama así porque, dentro del mundo corporativo, los altos cargos son considerados **“ballenas”**: pocos, grandes y con un enorme valor para un atacante.

💡 A diferencia del phishing común (que apunta a cualquiera), el Whaling se centra en **personas clave con poder de decisión, acceso a información crítica y autorización de operaciones financieras**.

### ⚙️ Cómo funciona un ataque de Whaling

1. **Investigación previa (reconocimiento)**

   - El atacante recopila información del directivo en redes sociales (LinkedIn, Twitter, prensa, página corporativa).
   - Aprende su estilo de comunicación, proyectos en los que participa y contactos habituales.

2. **Diseño del engaño**

   - El atacante fabrica un correo, mensaje o documento que parece 100% legítimo.
   - Puede suplantar a un proveedor, socio estratégico o incluso a otro directivo de la empresa.

3. **Ingeniería social**

   - El mensaje aprovecha la **autoridad y urgencia** para presionar a la víctima.
   - Ejemplos de peticiones comunes:

     - Transferencias bancarias urgentes.
     - Entrega de credenciales de acceso.
     - Descarga de un documento infectado con malware.
     - Compartir información confidencial (estrategia, contratos, propiedad intelectual).

4. **Acción de la víctima**

   - El ejecutivo, creyendo que la orden es legítima, **aprueba la transacción** o **abre el archivo malicioso**.

5. **Explotación del atacante**

   - Robo de dinero.
   - Acceso a cuentas críticas de la empresa.
   - Filtración de secretos corporativos.
   - Punto de entrada para un ataque mayor (ejemplo: ransomware).

### 📧 Ejemplo realista de un ataque de Whaling

**Correo falso dirigido a un CFO (Director Financiero):**

```
De: carlos.gomez@empresa-corporativa.com
Para: juan.lopez@empresa-corporativa.com
Asunto: Transferencia urgente – Proyecto Internacional

Hola Juan,

Necesitamos completar la transferencia de $350,000 al nuevo proveedor europeo
antes de que cierre el banco hoy. Es parte del contrato con el socio estratégico.

Te adjunto los datos bancarios para que procedas de inmediato.
Confírmame cuando la operación esté completada.

Gracias por tu rapidez,
Carlos Gómez
CEO
```

👉 Detalles a notar:

- El remitente puede ser `carlos.gomez@empresaa-corporativa.com` (un dominio falso casi idéntico).
- Se usa **urgencia y autoridad** para que el CFO no dude.
- Se solicita **una transferencia directa** sin tiempo de validación.

### 🛡️ Cómo mantenerse protegida del Whaling

#### ✅ Para directivos y ejecutivos

- **Verificar siempre por otro canal**: si recibes un pedido inusual de transferencia o acceso, confirma por teléfono o reunión directa.
- **Revisar el dominio del remitente**: no solo el nombre visible, sino el correo completo.
- **Desconfiar de la urgencia extrema**: los atacantes presionan para que no pienses demasiado.
- **Nunca compartir credenciales** por correo ni descargar archivos sospechosos.

#### ✅ Para empresas

1. **Políticas de aprobación múltiple**

   - Ninguna transferencia grande debería hacerse con solo un correo electrónico.
   - Se requiere **doble validación** (firma de dos directivos o confirmación en el sistema interno).

2. **Concienciación y entrenamiento**

   - Realizar simulacros de phishing para directivos.
   - Capacitación en ingeniería social enfocada en altos cargos.

3. **Seguridad técnica**

   - Autenticación multifactor (MFA) en correos y cuentas críticas.
   - Protocolos de seguridad en correos: **SPF, DKIM, DMARC**.
   - Monitoreo de transferencias bancarias con alertas automáticas.

4. **Cultura de seguridad**

   - Fomentar que incluso los altos ejecutivos validen siempre los pedidos sensibles.
   - Reforzar que “nadie está por encima” de las políticas de seguridad.

### 📌 Resumen

- El **Whaling** es un **phishing dirigido a altos ejecutivos**.
- Funciona gracias a la **autoridad y urgencia**, con mensajes muy personalizados.
- Puede provocar **fraudes millonarios** o **filtraciones de información crítica**.
- Para protegerse, se requiere una combinación de **políticas empresariales, medidas técnicas y concienciación ejecutiva**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**             | **Siguiente 4**         |
| ------------------ | ----------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./9_2_Whishing.md) | [⏩](./9_4_Smishing.md) |
