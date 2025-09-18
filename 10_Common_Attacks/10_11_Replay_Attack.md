| **Inicio**         | **atrás 10**                   | **Siguiente 12**                    |
| ------------------ | ------------------------------ | ----------------------------------- |
| [🏠](../README.md) | [⏪](./10_10_Deauth_Attack.md) | [⏩](./10_12_Rogue_Access_Point.md) |

---

## **Índice**

| Temario                                                                      |
| ---------------------------------------------------------------------------- |
| [320. ¿Qué es un ataque de repetición?](#320-qué-es-un-ataque-de-repetición) |

# **Replay Attack**

## **320. ¿Qué es un ataque de repetición?**

![Replay Attack](/img/10_Common_Attacks/Replay_Attack.jpeg "Replay Attack")

### 🔹 ¿Qué es un ataque de repetición?

Un **ataque de repetición** ocurre cuando un atacante **captura comunicaciones válidas** (normalmente mensajes autenticados o cifrados) y luego las **reenvía de nuevo (repite)** para engañar al sistema y obtener acceso o causar efectos no autorizados.

👉 La clave está en que **no necesita descifrar ni modificar el mensaje**, solo repetirlo.

Esto lo convierte en un ataque relativamente sencillo si no hay mecanismos de protección adicionales.

### 🔹 ¿Cómo funciona un ataque de repetición?

Veamos el flujo general:

1. **Captura:**

   El atacante intercepta una comunicación legítima entre dos partes (ejemplo: cliente y servidor).

2. **Almacenamiento:**

   Guarda el paquete/mensaje válido.

3. **Reenvío (repetición):**

   Más tarde, el atacante envía el mismo mensaje al servidor.

4. **Efecto:**

   - Si el sistema **no valida la frescura** del mensaje, lo tratará como legítimo.
   - Esto puede llevar a accesos no autorizados, transacciones duplicadas o falsos inicios de sesión.

### 📌 Ejemplos prácticos

#### Ejemplo 1: Banca en línea

- Cliente envía al banco: _“Transferir \$100 a la cuenta 123”_.
- El mensaje viaja cifrado con TLS.
- El atacante no puede descifrarlo, pero sí **capturar y reenviar**.
- Resultado: el banco procesa de nuevo la transferencia, enviando **otro \$100** a la misma cuenta.

#### Ejemplo 2: Control de acceso físico

- Una tarjeta RFID manda un código de autenticación para abrir una puerta.
- Un atacante graba la señal y luego la **reproduce con un dispositivo**.
- Aunque no sabe cómo se generó, la puerta se abre otra vez.

#### Ejemplo 3: Red Wi-Fi

- Durante la autenticación WPA, un atacante graba parte del intercambio de claves.
- Si no hay protección contra replay, puede reenviar mensajes y provocar reconexiones o intentos de explotación.

### 🔹 ¿Cómo detener un ataque de repetición?

Para prevenirlos, los sistemas deben garantizar **frescura y unicidad** en los mensajes. Esto se logra con varias técnicas:

#### ✅ 1. Uso de **marcas de tiempo**

- Cada mensaje lleva una **hora/fecha exacta**.
- El servidor rechaza cualquier mensaje con marca de tiempo antigua.
- Ejemplo: las **tokens JWT** en autenticación tienen expiración (`exp`).

#### ✅ 2. Uso de **números de secuencia / contadores**

- Cada mensaje incluye un **número único creciente**.
- El servidor solo acepta mensajes con el número siguiente esperado.
- Esto evita que mensajes antiguos sean aceptados.

#### ✅ 3. **Nonces (números aleatorios de un solo uso)**

- El servidor envía un **nonce aleatorio** al cliente antes de aceptar una operación.
- El cliente debe incluir ese nonce en la respuesta.
- Como el nonce cambia cada vez, el atacante no puede reutilizar un mensaje capturado.

👉 Ejemplo: protocolos de autenticación como **Kerberos** y **OAuth** usan nonces.

#### ✅ 4. **Cifrado con integridad (MAC, HMAC)**

- Aunque el mensaje esté cifrado, el servidor también valida su **autenticidad** con un código de integridad que depende de valores únicos (ej. número de secuencia).
- Si el atacante repite un paquete, la validación falla.

#### ✅ 5. **Protocolos modernos seguros**

- TLS (cuando bien implementado) incluye protecciones contra replay.
- WPA2/WPA3 en Wi-Fi incluyen contadores para descartar paquetes repetidos.
- Sistemas de pagos electrónicos incluyen **tokens únicos por transacción**.

### 🔹 Resumen

- **Ataque de repetición:** el atacante captura y reenvía mensajes válidos para engañar al sistema.
- **Funcionamiento:** no requiere descifrar ni alterar datos, solo repetirlos.
- **Ejemplos:** transferencias bancarias duplicadas, apertura de puertas RFID, ataques Wi-Fi.
- **Defensa:** marcas de tiempo, números de secuencia, nonces, HMAC, protocolos modernos como TLS, Kerberos, WPA3.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 10**                   | **Siguiente 12**                    |
| ------------------ | ------------------------------ | ----------------------------------- |
| [🏠](../README.md) | [⏪](./10_10_Deauth_Attack.md) | [⏩](./10_12_Rogue_Access_Point.md) |
