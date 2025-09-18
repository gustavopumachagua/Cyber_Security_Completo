| **Inicio**         | **atrás 3**            | **Siguiente 5**             |
| ------------------ | ---------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./9_3_Whaling.md) | [⏩](./9_5_Spam_vs_Spim.md) |

---

## **Índice**

| Temario                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------ |
| [271. ¿Qué es el smishing (phishing por SMS)?](#271-qué-es-el-smishing-phishing-por-sms)                                 |
| [272. ¿Qué es el smishing? Cómo funciona el phishing por SMS](#272-qué-es-el-smishing-cómo-funciona-el-phishing-por-sms) |

# **Smishing**

## **271. ¿Qué es el smishing (phishing por SMS)?**

![Smishing](/img/9_Attack_Types_and_Differences/Smishing.png "Smishing")

El **smishing** (SMS + phishing) es un ataque de **ingeniería social** en el que un ciberdelincuente envía mensajes de texto falsos haciéndose pasar por bancos, empresas de mensajería, tiendas online o incluso entidades gubernamentales.

👉 El objetivo es:

- Robar contraseñas o información personal.
- Conseguir que la víctima descargue malware.
- Engañarla para hacer pagos o transferencias.

### ⚙️ Cómo funcionan los ataques de smishing

1. **El atacante envía un SMS masivo** con un texto alarmante o tentador:

   - Problemas con la cuenta bancaria.
   - Un paquete pendiente de entrega.
   - Una promoción especial.

2. **El SMS contiene un enlace o número de teléfono falso**.

   - El enlace lleva a una web clonada del banco o empresa.
   - El número conecta con un call center falso manejado por los atacantes.

3. **La víctima hace clic o responde al mensaje**, entregando sus datos.

   - Usuario y contraseña.
   - Número de tarjeta.
   - Código de verificación OTP.

4. **El atacante explota la información robada** para:

   - Vaciar cuentas bancarias.
   - Robar identidades.
   - Vender los datos en la Dark Web.

### 📧 Ejemplos de estafas de smishing

#### 🔴 Ejemplo 1: Banco falso

```
[Banco Nacional] Se ha detectado un acceso inusual en su cuenta.
Verifique inmediatamente aquí: http://bn-seguridad.com
```

👉 El enlace lleva a una página falsa del banco que pide usuario, clave y token.

#### 🔴 Ejemplo 2: Paquete pendiente

```
Su paquete no pudo ser entregado.
Confirme su dirección y pague la tarifa de envío en: http://delivery-seguro.net
```

👉 Al ingresar, la víctima entrega sus datos personales y tarjeta de crédito.

#### 🔴 Ejemplo 3: Gobierno o impuestos

```
[SUNAT] Tiene una devolución de impuestos pendiente.
Reclámela en: http://sunat-gov-validacion.com
```

👉 El sitio falso roba números de DNI, contraseñas y datos bancarios.

### 🔄 Smishing vs Phishing vs Vishing

| Tipo         | Canal utilizado           | Ejemplo                                                   |
| ------------ | ------------------------- | --------------------------------------------------------- |
| **Phishing** | Correo electrónico        | Un email de “PayPal” solicitando confirmar la cuenta.     |
| **Smishing** | SMS / WhatsApp / Telegram | Un mensaje de “tu banco” con enlace a “verificación”.     |
| **Vishing**  | Llamadas telefónicas      | Un supuesto agente del banco pidiendo datos por teléfono. |

👉 Los tres buscan lo mismo: **robar datos**, pero usan distintos canales para engañar.

### 🛡️ Combatiendo los ataques de smishing

#### ✅ Para usuarios

- **No hacer clic en enlaces recibidos por SMS**, especialmente si prometen dinero o amenazan con bloqueos.
- **Verificar directamente en la app oficial** del banco o en la web real, nunca desde el enlace del SMS.
- **No responder al mensaje**, ya que confirma que el número está activo.
- **Instalar apps oficiales** solo desde tiendas confiables (Google Play, App Store).
- **Activar alertas de seguridad en el banco** para detectar movimientos sospechosos.

#### ✅ Para empresas

- **Campañas de concienciación** para empleados y clientes.
- **Implementar canales oficiales de comunicación** y aclarar que nunca pedirán datos por SMS.
- **Monitorear dominios falsos** que intenten suplantar a la organización.
- **Colaborar con operadores móviles** para bloquear números y enlaces maliciosos.

### 📌 Resumen

- El **smishing** es phishing a través de SMS o apps de mensajería.
- Funciona gracias al **miedo o la urgencia** que transmiten los mensajes.
- Se diferencia de:

  - **Phishing** → correo electrónico.
  - **Vishing** → llamadas telefónicas.

- Para protegerse: **nunca confiar en enlaces de SMS, validar en canales oficiales y denunciar los intentos sospechosos**.

---

[🔼](#índice)

---

## **272. ¿Qué es el smishing? Cómo funciona el phishing por SMS**

El **smishing** es una forma de **phishing** que se realiza a través de **mensajes de texto (SMS)** o aplicaciones de mensajería (WhatsApp, Telegram, etc.).

👉 El nombre viene de la combinación de:

- **SMS** (Short Message Service).
- **Phishing** (engaño para robar datos).

En este tipo de ataque, los ciberdelincuentes envían un **mensaje que parece provenir de una fuente legítima** (banco, empresa de mensajería, gobierno, tienda online) con el objetivo de que la víctima:

- Haga clic en un enlace malicioso.
- Descargue una app falsa.
- O revele información personal o financiera.

### ⚙️ ¿Cómo funciona el smishing?

1. **El atacante envía un SMS masivo**

   - Puede enviarlo a miles de números al azar o a una lista filtrada (por ejemplo, clientes de cierto banco).
   - El mensaje suele generar **miedo o urgencia** (“su cuenta será bloqueada”, “su paquete no pudo ser entregado”).

2. **El mensaje incluye un enlace o un número falso**

   - El enlace lleva a una **página web clonada** que parece oficial.
   - El número conecta con un **call center falso** donde un supuesto “agente” pide datos sensibles.

3. **La víctima cae en la trampa**

   - Introduce su usuario, contraseña, tarjeta de crédito o códigos de verificación OTP.
   - O descarga un archivo malicioso que roba datos del teléfono.

4. **El atacante aprovecha la información**

   - Realiza compras, transacciones o roba identidad.
   - Puede incluso vender esos datos en foros clandestinos (Dark Web).

### 📧 Ejemplos de smishing

#### 🔴 Ejemplo 1: Banco falso

```
[Banco Nacional] Se ha detectado un inicio de sesión sospechoso en su cuenta.
Verifique de inmediato: http://bn-seguridad.com
```

👉 La víctima abre el enlace, ingresa su usuario y clave, y el atacante captura sus credenciales.

#### 🔴 Ejemplo 2: Paquete pendiente

```
Su paquete no pudo ser entregado.
Confirme su dirección y pague la tarifa de envío en: http://delivery-seguro.net
```

👉 Parece un aviso de DHL o FedEx, pero en realidad roba datos personales y tarjeta de crédito.

#### 🔴 Ejemplo 3: Gobierno o impuestos

```
[SUNAT] Tiene una devolución de impuestos pendiente.
Reclámela aquí: http://sunat-devoluciones.com
```

👉 Página falsa que pide DNI, clave SOL y datos bancarios.

### 🚨 Características típicas de un SMS de smishing

- Mensajes **urgentes o alarmantes** (“bloqueo inmediato”, “última oportunidad”).
- **Errores de ortografía o redacción** que delatan al atacante.
- **Enlaces sospechosos** con dominios raros (`.net`, `.info`, o parecidos al original).
- Peticiones **inusuales** (confirmar datos personales o bancarios).

### 🛡️ Cómo protegerse del smishing

✅ Para usuarios:

- **Nunca hacer clic en enlaces de SMS desconocidos.**
- **Verificar directamente en la app oficial o en la web legítima** (escribir la URL manualmente).
- **No responder** al mensaje (confirma que el número está activo).
- **Instalar apps solo desde tiendas oficiales** (Google Play, App Store).
- **Contactar al banco o empresa** por canales oficiales si hay dudas.

✅ Para empresas:

- **Educar a clientes y empleados** sobre smishing.
- **Aclarar en políticas de comunicación** que nunca pedirán datos sensibles por SMS.
- **Monitorear y denunciar dominios falsos** que intenten suplantar la marca.
- **Trabajar con operadoras móviles** para bloquear números de origen fraudulentos.

### 📌 Resumen

- El **smishing** es **phishing por SMS o apps de mensajería**.
- Se basa en **crear urgencia o miedo** para que la víctima actúe sin pensar.
- El ataque funciona a través de **enlaces o números falsos** que llevan a webs clonadas o call centers falsos.
- Se puede prevenir con **desconfianza activa, validación en canales oficiales y educación en ciberseguridad**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**            | **Siguiente 5**             |
| ------------------ | ---------------------- | --------------------------- |
| [🏠](../README.md) | [⏪](./9_3_Whaling.md) | [⏩](./9_5_Spam_vs_Spim.md) |
