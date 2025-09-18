| **Inicio**         | **atrás 6**                     | **Siguiente 8**                |
| ------------------ | ------------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./9_6_Shoulder_Surfing.md) | [⏩](./9_8_Dumpster_Diving.md) |

---

## **Índice**

| Temario                                                                                                                                                             |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [278. Ataques de seguimiento](#278-ataques-de-seguimiento)                                                                                                          |
| [279. Tailgating y Piggybacking: Explicación de las tácticas de ingeniería social](#279-tailgating-y-piggybacking-explicación-de-las-tácticas-de-ingeniería-social) |

# **Tailgating**

## **278. Ataques de seguimiento**

![Tailgating](/img/9_Attack_Types_and_Differences/Tailgating.jpg "Tailgating")

### 🚪 ¿Qué es un ataque de tailgating?

Un **ataque de tailgating** (o _intrusión por seguimiento_) ocurre cuando un atacante **aprovecha la entrada legítima de un empleado autorizado** para acceder a un área restringida **sin tener credenciales propias**.

👉 Ejemplo: Un intruso entra detrás de un empleado cuando este abre la puerta con su tarjeta de acceso, sin que nadie lo cuestione.

Se llama _tailgating_ (ir a la “cola”) porque el atacante literalmente se **pega al paso del empleado** para entrar.

### ⚖️ Ataques de tailgating vs. piggybacking

Aunque suelen confundirse, hay una diferencia:

- **Tailgating**:

  - El atacante entra **sin que el empleado lo note**.
  - Ejemplo: alguien aprovecha que la puerta queda abierta unos segundos tras un acceso autorizado.

- **Piggybacking**:

  - El atacante entra **con el consentimiento (aunque engañoso) del empleado**.
  - Ejemplo: un intruso dice “¿puedes sostener la puerta? olvidé mi tarjeta” y el empleado, por cortesía, lo deja pasar.

👉 En resumen:

- **Tailgating** = el intruso entra **sin permiso consciente**.
- **Piggybacking** = el intruso entra **con ayuda ingenua** de alguien autorizado.

### 👥 ¿Quiénes corren mayor riesgo de sufrir ataques por infiltración?

- **Empresas grandes** con alto flujo de empleados y visitantes.
- **Entornos corporativos con cultura de cortesía** (abrir puertas sin verificar).
- **Organizaciones con seguridad física laxa**, sin control de accesos.
- **Industrias críticas**: bancos, centros de datos, hospitales, fábricas de tecnología.
- **Nuevos empleados**: suelen no reconocer aún quién pertenece y quién no.

### 📌 Ejemplos de ataques de tailgating

1. **Centro de datos**: un atacante sigue a un ingeniero de TI al área de servidores y coloca un USB malicioso.
2. **Oficina corporativa**: un intruso con apariencia de mensajero entra detrás de un empleado y roba documentos de escritorios.
3. **Hospital**: alguien con bata falsa se infiltra tras el personal médico y accede a historiales clínicos.
4. **Empresa de logística**: un falso proveedor entra tras un trabajador al almacén y roba mercancía.

### 🛡️ 10 maneras de prevenir los ataques de tailgating

1. **Política de puertas seguras**: no permitir el acceso a desconocidos, aunque sea por cortesía.
2. **Autenticación multifactor física**: combinar tarjetas con biometría (huella, rostro).
3. **Puertas mantrap o esclusas**: solo permiten el paso de una persona a la vez.
4. **Guardias de seguridad en entradas críticas**.
5. **Torniquetes y sensores de presencia**.
6. **Cámaras de videovigilancia con monitoreo en tiempo real**.
7. **Credenciales visibles siempre**: exigir el uso de gafetes en todo momento.
8. **Concienciación de empleados**: entrenamientos para reconocer y reportar intentos de infiltración.
9. **Política de visitantes estricta**: registro obligatorio y acompañamiento.
10. **Alertas automáticas**: sistemas que detectan accesos múltiples con una sola tarjeta.

### 🛡️ Cómo puede ayudar Proofpoint

Aunque Proofpoint es más conocida por su **ciberseguridad digital** (protección contra phishing, malware, ingeniería social y amenazas internas), también ayuda a mitigar **ataques de tailgating** al:

- **Educar a empleados** con simulaciones de ingeniería social.
- **Concienciar sobre riesgos físicos** junto con riesgos digitales.
- **Proporcionar análisis de comportamiento** para detectar actividades sospechosas (ej. accesos inusuales tras un tailgating exitoso).
- **Integrar la seguridad física y digital** en programas de concienciación: entender que el “factor humano” es el punto más vulnerable.

✅ **Resumen rápido:**

- El **tailgating** es cuando alguien entra detrás de un empleado autorizado **sin permiso**.
- El **piggybacking** es cuando alguien consigue entrar porque un empleado **lo deja pasar por cortesía**.
- Las organizaciones con **alto flujo de personas y baja vigilancia física** son más vulnerables.
- Se previene con **cultura de seguridad, controles de acceso físicos, concienciación y tecnología**.

---

[🔼](#índice)

---

## **279. Tailgating y Piggybacking: Explicación de las tácticas de ingeniería social**

### 🚪 ¿Qué son el Tailgating y el Piggybacking?

- **Tailgating**: el atacante entra **sin permiso consciente** de la víctima, aprovechando la apertura de una puerta o el paso de un empleado autorizado.

  👉 Es como “colar” en la entrada.

- **Piggybacking**: el atacante entra **con el permiso ingenuo** de un empleado, normalmente apelando a la cortesía o engaño.

  👉 Es como pedir que “te sostengan la puerta”.

Ambos buscan el mismo objetivo: **acceder a áreas restringidas sin credenciales válidas**.

### 🎭 Explicación detallada de Tailgating

#### 🔹 ¿Cómo funciona?

1. El atacante se acerca a una puerta de acceso controlado.
2. Espera a que un empleado autorizado pase con su tarjeta/huella.
3. Antes de que la puerta se cierre, se cuela detrás sin que el empleado lo note.

#### 📌 Ejemplo realista:

En una empresa de telecomunicaciones, un intruso vestido como técnico de mantenimiento espera cerca de la entrada. Cuando un empleado pasa su tarjeta y entra, el intruso lo sigue a pocos pasos de distancia. El trabajador no se da cuenta porque estaba distraído con su celular.

👉 El intruso ahora tiene acceso a servidores y equipos sensibles.

### 🎭 Explicación detallada de Piggybacking

#### 🔹 ¿Cómo funciona?

1. El atacante se presenta como alguien legítimo (proveedor, visitante, nuevo empleado).
2. Se acerca a una puerta con control de acceso.
3. Convence o manipula a un empleado para que lo deje entrar (“Olvidé mi tarjeta”, “¿Me sostienes la puerta, por favor?”).
4. El empleado, por cortesía o presión, le abre el acceso.

#### 📌 Ejemplo realista:

Un atacante con un paquete en las manos finge ser un mensajero de Amazon. Se acerca a la entrada de una empresa y le dice a un empleado:

> “Disculpa, no puedo abrir con las manos ocupadas, ¿me ayudas con la puerta?”

El empleado sostiene la puerta y el atacante entra sin mostrar identificación ni credenciales.

👉 Resultado: infiltración lograda gracias a la **confianza y cortesía humanas**.

### ⚖️ Diferencia clave entre Tailgating y Piggybacking

| Característica           | Tailgating 🚶‍♂️                                                                 | Piggybacking 🤝                                                        |
| ------------------------ | ----------------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| **Permiso del empleado** | No hay permiso, el atacante entra sin ser visto o sin que el empleado lo note | Sí hay “permiso”, aunque el empleado fue engañado o actuó por cortesía |
| **Método**               | Siguiendo de cerca a alguien autorizado                                       | Pidiendo ayuda, excusas, manipulación                                  |
| **Ejemplo típico**       | Colarse detrás de alguien en un torniquete                                    | “Olvidé mi tarjeta, ¿me sostienes la puerta?”                          |
| **Nivel de engaño**      | Bajo (aprovecha distracción)                                                  | Alto (requiere persuasión)                                             |

### 🛡️ Cómo protegerse contra Tailgating y Piggybacking

✅ **Concienciación de empleados**

- Recordar que la **seguridad > cortesía**.
- Nunca permitir la entrada a alguien sin credenciales visibles.

✅ **Controles físicos**

- **Puertas mantrap** (esclusas que dejan pasar de a una persona).
- **Torniquetes y sensores** que detectan múltiples accesos con una sola tarjeta.
- **Guardias de seguridad** en entradas críticas.

✅ **Políticas empresariales**

- Uso obligatorio de **gafetes/credenciales visibles**.
- Registro y acompañamiento obligatorio de **visitantes y proveedores**.
- Reglas claras: “Si alguien olvidó su tarjeta → debe pasar por recepción”.

✅ **Tecnología de refuerzo**

- Cámaras de videovigilancia con IA para detectar accesos sospechosos.
- Alarmas cuando se detecta más de una persona pasando tras un único acceso autorizado.

### 📌 Resumen final

- **Tailgating** → el intruso entra sin ser notado, aprovechando puertas abiertas o descuidos.
- **Piggybacking** → el intruso entra con ayuda ingenua, manipulando la cortesía del empleado.
- Ambos son formas de **ingeniería social física**, muy usadas porque explotan la **confianza y la falta de vigilancia**.
- La mejor defensa es la combinación de **cultura de seguridad, controles físicos, políticas claras y tecnología de monitoreo**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**                     | **Siguiente 8**                |
| ------------------ | ------------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./9_6_Shoulder_Surfing.md) | [⏩](./9_8_Dumpster_Diving.md) |
