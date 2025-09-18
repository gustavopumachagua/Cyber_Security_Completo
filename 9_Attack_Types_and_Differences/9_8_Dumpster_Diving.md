| **Inicio**         | **atrás 7**               | **Siguiente 9**         |
| ------------------ | ------------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./9_7_Tailgating.md) | [⏩](./9_9_Zero_Day.md) |

---

## **Índice**

| Temario                                                                                                                |
| ---------------------------------------------------------------------------------------------------------------------- |
| [280. Dumpster Diving](#280-dumpster-diving)                                                                           |
| [281. Que es Dumpster Diving?](#281-que-es-dumpster-diving)                                                            |
| [282. Dumpster Diving en busca de informacion confidencial](#282-dumpster-diving-en-busca-de-informacion-confidencial) |

# **Dumpster Diving**

## **280. Dumpster Diving**

![Dumpster Diving](/img/9_Attack_Types_and_Differences/Dumpster_Diving.jpg "Dumpster Diving")

### 🗑️ ¿Qué es el Dumpster Diving?

El **Dumpster Diving** es la práctica de **revisar la basura** de una persona u organización para encontrar información valiosa que pueda usarse en un ataque.

👉 El término viene de _dumpster_ (contenedor de basura) y _diving_ (bucear).

En pocas palabras: “bucear en la basura para obtener secretos”.

No se limita a papeles; también incluye **dispositivos electrónicos, memorias USB, discos duros, tarjetas de acceso** o cualquier objeto que pueda contener datos.

### 🎭 ¿Cómo funciona el Dumpster Diving?

1. **Identificación del objetivo**

   - El atacante elige una persona o empresa que le interesa (banco, compañía tecnológica, directivo).

2. **Revisión de desechos**

   - Busca en la basura física: papeles, notas, recibos.
   - O en la “basura digital”: discos duros, CD, celulares desechados.

3. **Obtención de datos útiles**

   - Contraseñas anotadas.
   - Documentos internos.
   - Información de clientes/proveedores.
   - Planos de edificios o diagramas de red.

4. **Explotación de la información**

   - Preparar ataques de **phishing más creíbles**.
   - Clonar identidades.
   - Acceder a sistemas o lugares físicos.
   - Vender la información en la dark web.

### 📌 Ejemplos de Dumpster Diving

#### 🔹 Caso 1: Oficina corporativa

Un atacante revisa los contenedores de basura de una empresa y encuentra:

- Documentos con el logotipo de la compañía.
- Correos impresos con firmas de directivos.
- Notas adhesivas con contraseñas temporales.

👉 Con esta información puede crear un **correo de spear phishing** que parezca auténtico.

#### 🔹 Caso 2: Cajero automático retirado

Una entidad bancaria tira un cajero antiguo sin limpiar el disco duro.
El atacante lo consigue en un basural y extrae:

- Historial de transacciones.
- Software interno del banco.

👉 Resultado: robo de información financiera y preparación de futuros ataques.

#### 🔹 Caso 3: Persona individual

Un criminal revisa la basura doméstica de alguien y encuentra:

- Extractos bancarios.
- Recibos de tarjetas de crédito.
- Fotocopias de documentos de identidad.

👉 Con esos datos realiza **suplantación de identidad** para pedir créditos a nombre de la víctima.

### ⚠️ ¿Por qué es peligroso el Dumpster Diving?

- **Es legalmente ambiguo** en algunos países (la basura puede considerarse abandonada).
- **Difícil de detectar**: no deja huella digital como un hackeo.
- **Bajo costo**: no requiere software, solo paciencia.
- **Información muy valiosa**: a veces más de lo que se encuentra en internet.

### 🛡️ Cómo protegerse del Dumpster Diving

✅ **Para personas**

1. **Destruye documentos**: utiliza trituradoras de papel de corte cruzado.
2. **Rompe tarjetas y chequeras** antes de tirarlas.
3. **Borra discos y memorias** con software especializado antes de desecharlos.
4. **No compartas basura sensible** en lugares accesibles (ej. edificios de departamentos).

✅ **Para empresas**

1. **Política de destrucción de documentos** obligatoria.
2. **Contenedores de seguridad** con cerradura para papeles confidenciales.
3. **Reciclaje controlado de hardware**: limpiar discos duros con herramientas forenses o destruirlos físicamente.
4. **Capacitación a empleados** sobre riesgos de dejar documentos en la basura.
5. **Auditorías periódicas** de eliminación de información.

### 📌 Resumen

- **Dumpster Diving** es buscar información sensible en la basura física o digital.
- Puede revelar contraseñas, datos financieros, información personal o planes empresariales.
- Se usa como base para ataques más sofisticados como **phishing, fraude, robo de identidad o espionaje industrial**.
- La prevención consiste en **triturar, destruir, borrar y educar**: nunca tirar información sensible sin procesarla antes.

---

[🔼](#índice)

---

## **281. Que es Dumpster Diving?**

### 🗑️ ¿Qué es el buceo en el contenedor en ciberseguridad?

El **Dumpster Diving** es una técnica de **ingeniería social** en la que los atacantes buscan información confidencial revisando la basura física o digital de una persona o empresa.

👉 La idea es simple: **“lo que tiras todavía puede usarse en tu contra”**.

Incluye desde revisar papeles en un basurero, hasta recuperar discos duros o memorias USB desechadas.

### 🔹 ¿Qué es el buceo en el contenedor?

- En términos simples: **recolectar datos sensibles de la basura**.
- Ejemplo físico: encontrar una hoja con credenciales de acceso.
- Ejemplo digital: comprar un disco duro usado sin borrar y extraer datos de clientes.

### 🚀 ¡Simplifique la ciberseguridad con PowerDMARC!

Herramientas como **PowerDMARC** ayudan a evitar que la información obtenida mediante Dumpster Diving sea usada en ataques de **phishing o suplantación de identidad**.

- Con protocolos como **DMARC, DKIM y SPF**, aunque un atacante encuentre datos de correo electrónico en la basura, **no podrá falsificar fácilmente el dominio** de la empresa.
- Esto reduce el riesgo de que empleados o clientes caigan en fraudes.

### ⚙️ ¿Cómo funciona el Dumpster Diving?

1. **Recolección de basura**: el atacante revisa contenedores, oficinas o dispositivos desechados.
2. **Extracción de datos útiles**: contraseñas, nombres de usuario, documentos internos, datos de clientes.
3. **Explotación**: se usan esos datos para:

   - Crear correos de phishing creíbles.
   - Cometer fraude o robo de identidad.
   - Infiltrarse en redes corporativas.

📌 Ejemplo real: Un hacker compra laptops viejas en una subasta de gobierno y encuentra archivos sin borrar con información de ciudadanos.

### ✅ Beneficios de la inmersión en el contenedor de la informática (para el atacante)

Desde el punto de vista de un atacante, el Dumpster Diving es atractivo porque:

- **Es barato**: no requiere software avanzado.
- **Es discreto**: difícil de detectar.
- **Puede ser muy valioso**: datos internos son más útiles que la información pública.
- **Complementa otros ataques**: los datos recuperados sirven para personalizar campañas de ingeniería social.

### 🎭 Los buzos de la basura utilizan tácticas de ingeniería social

El Dumpster Diving **no siempre implica meterse físicamente en la basura**.
A veces se combina con:

- **Engaños**: fingir ser un reciclador para entrar en áreas restringidas.
- **Pretexting**: justificar el acceso con una historia falsa (“Soy de mantenimiento, debo revisar este equipo viejo”).
- **Reconocimiento previo**: identificar días de recogida de basura para actuar sin levantar sospechas.

### 🌑 El lado oscuro de la ciberseguridad

- La mayoría de ataques cibernéticos comienzan con **información recolectada de forma sencilla**.
- Algo tan trivial como una **nota adhesiva con una contraseña** puede ser la puerta de entrada a un ataque mayor.
- Incluso empresas grandes han sufrido **espionaje industrial** porque no destruyeron documentos internos antes de desecharlos.

### 🛑 ¿Cómo detener los ciberataques de Dumpster Diving?

#### 🔒 Para empresas

- **Política de destrucción de documentos**: uso de trituradoras de corte cruzado.
- **Eliminación segura de hardware**: formateo de bajo nivel o destrucción física de discos.
- **Contenedores de basura protegidos**: con cerraduras y acceso limitado.
- **Capacitación a empleados**: enseñar que la basura también es un riesgo de seguridad.

#### 🔒 Para personas

- Tritura estados de cuenta, contratos o recibos antes de botarlos.
- Borra y sobrescribe memorias USB, discos duros y teléfonos antes de venderlos.
- No dejes recibos bancarios completos en la basura.
- Evita tirar tarjetas bancarias sin cortarlas en pedazos.

### 🛡️ Proteger su información

La mejor defensa contra el Dumpster Diving combina:

1. **Prevención física** (triturar, destruir, bloquear).
2. **Prevención digital** (cifrado de discos, borrado seguro).
3. **Seguridad en comunicaciones** (usar DMARC, DKIM, SPF para impedir suplantaciones).
4. **Conciencia del usuario**: la información que tiras puede ser tan valiosa como la que guardas.

-

📌 **Resumen rápido**:

El Dumpster Diving es un ataque de **ingeniería social** que se aprovecha de la basura para obtener información sensible. Aunque parezca un método “rudimentario”, sigue siendo una de las formas más efectivas de obtener datos que luego se usan en ataques de **phishing, fraude o espionaje**.

---

[🔼](#índice)

---

## **282. Dumpster Diving en busca de informacion confidencial**

### 🗑️ ¿Qué es el Dumpster Diving en busca de información confidencial?

El **Dumpster Diving** (o “buceo en la basura”) consiste en **revisar la basura física o digital** de una persona o empresa con el fin de obtener **información sensible** que pueda usarse para un ataque.

👉 Lo importante no es la basura en sí, sino **los datos valiosos que contiene**: contraseñas, documentos internos, planos, registros financieros, discos duros, correos impresos, etc.

### ⚙️ ¿Cómo funciona este ataque?

1. **Selección del objetivo**
   El atacante identifica una empresa, institución o persona de interés.

2. **Recolección de basura**

   - Físicamente: contenedores, oficinas, bolsas de reciclaje, papeleras.
   - Digitalmente: equipos informáticos viejos, discos duros, memorias USB.

3. **Extracción de información útil**

   - Contraseñas anotadas en papeles o post-its.
   - Documentos de clientes/proveedores.
   - Información financiera (facturas, estados de cuenta).
   - Números de tarjeta de crédito.
   - Correos electrónicos internos.

4. **Explotación**

   El atacante usa lo encontrado para:

   - Preparar ataques de **phishing más creíbles**.
   - **Robar identidades** y cometer fraude.
   - Acceder a sistemas informáticos o físicos.
   - Vender la información en la dark web.

### 📌 Ejemplos de Dumpster Diving

#### 🔹 Ejemplo 1: Empresa tecnológica

Un atacante revisa los contenedores de basura de una compañía y encuentra:

- Una nota adhesiva con una contraseña Wi-Fi.
- Una lista de clientes con correos electrónicos.

  👉 Con eso, lanza un **ataque de phishing** haciéndose pasar por soporte técnico de la empresa.

#### 🔹 Ejemplo 2: Banco

Una sucursal bancaria desecha impresiones de transacciones sin triturar.
El atacante encuentra:

- Nombres completos.
- Números de cuenta.
- Firmas escaneadas.

  👉 Con esa información, comete **suplantación de identidad** para retirar dinero.

#### 🔹 Ejemplo 3: Persona particular

En la basura de un edificio de departamentos, un atacante halla:

- Extractos bancarios.
- Recibos de tarjeta de crédito.
- Copias viejas de documentos de identidad.

  👉 Los usa para pedir **un préstamo fraudulento** a nombre de la víctima.

### ⚠️ ¿Por qué es tan peligroso?

- **Fácil y barato**: no requiere habilidades técnicas avanzadas.
- **Legalmente ambiguo**: en muchos lugares, la basura “abandonada” puede ser recogida libremente.
- **Valioso para el atacante**: los datos reales son más efectivos que cualquier búsqueda en internet.
- **Base de otros ataques**: lo que se encuentra en la basura suele ser el primer paso para algo mayor (phishing, espionaje industrial, fraudes).

### 🛡️ Cómo protegerse del Dumpster Diving

✅ **Para individuos**

- Tritura o quema documentos antes de desecharlos.
- Rompe tarjetas de crédito o débito caducadas.
- Borra con software seguro la información en memorias y discos antes de tirarlos o venderlos.
- Evita dejar correspondencia con datos personales en la basura sin destruir.

✅ **Para empresas**

- Implementar **políticas de eliminación segura de documentos** (trituradoras de corte cruzado).
- Usar **contenedores de seguridad con cerradura** para papeles confidenciales.
- **Eliminar o destruir físicamente discos duros** antes de descartarlos.
- Capacitar al personal sobre la importancia de **tratar la basura como información sensible**.
- Establecer auditorías periódicas para verificar el cumplimiento.

### 📌 Resumen final

- El **Dumpster Diving en busca de información confidencial** es una técnica de ingeniería social en la que se explota la **basura física o digital** para obtener datos sensibles.
- Con esos datos, los atacantes pueden cometer fraudes, espionaje o lanzar ataques cibernéticos más avanzados.
- Aunque parezca un método “rudimentario”, es altamente efectivo y de bajo costo.
- La prevención requiere **conciencia, destrucción de documentos, gestión segura de dispositivos electrónicos y políticas claras en las organizaciones**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**               | **Siguiente 9**         |
| ------------------ | ------------------------- | ----------------------- |
| [🏠](../README.md) | [⏪](./9_7_Tailgating.md) | [⏩](./9_9_Zero_Day.md) |
