| **Inicio**         | **atrás 2**             | **Siguiente 4**                           |
| ------------------ | ----------------------- | ----------------------------------------- |
| [🏠](../README.md) | [⏪](./11_2_Hashing.md) | [⏩](./11_4_Private_Key_vs_Public_Key.md) |

---

## **Índice**

| Temario                                                                    |
| -------------------------------------------------------------------------- |
| [337. Intercambio de claves](#337-intercambio-de-claves)                   |
| [338. Intercambio de claves secretas](#338-intercambio-de-claves-secretas) |
|                                                                            |

# **Key Exchange**

## **337. Intercambio de claves**

![Key Exchange](/img/11_Basics_of_Cryptography/Key_Exchange.jpg "Key Exchange")

### 📌 Definición de intercambio de claves

El **intercambio de claves** es un proceso criptográfico mediante el cual **dos partes (Alice y Bob)** acuerdan una **clave secreta compartida** para cifrar su comunicación, **sin que un atacante pueda descubrirla**, incluso si la comunicación es interceptada.

👉 Esto es fundamental porque:

- La **criptografía simétrica** necesita que ambas partes usen la misma clave.
- El problema es: **¿cómo compartir esa clave de forma segura a través de un canal inseguro (como Internet)?**

### 📌 Esquemas de intercambio de claves

Existen principalmente **dos esquemas**:

#### 1. 🔑 Intercambio de claves con criptografía asimétrica (RSA)

- Usa un **par de claves pública/privada**.
- La clave pública se utiliza para cifrar, y la privada para descifrar.

**Ejemplo con RSA:**

1. Bob genera un par de claves:

   - Pública: 🔓`(n, e)`
   - Privada: 🔐`(n, d)`

2. Bob envía su clave pública a Alice.
3. Alice genera una clave simétrica (ej: `ABC123`) y la cifra con la clave pública de Bob.

   - Cifrado: `RSA(ABC123, e)` → `XYZ...`

4. Alice envía el mensaje cifrado a Bob.
5. Bob lo descifra con su clave privada → recupera `ABC123`.

👉 Ahora **ambos tienen la misma clave simétrica** (`ABC123`) para cifrar mensajes con AES, más rápido que RSA.

#### 2. 🔑 Intercambio de claves con criptografía basada en matemáticas (Diffie-Hellman)

Es uno de los métodos más famosos (1976).

- Permite que dos partes acuerden una clave secreta **sin enviarla directamente**.
- Se basa en la dificultad de calcular logaritmos discretos en grandes números primos.

**Ejemplo simplificado (Diffie-Hellman):**

1. Alice y Bob acuerdan públicamente dos números:

   - Primo grande `p = 23`
   - Base `g = 5`

2. Alice elige un número secreto `a = 6`.

   - Calcula: `A = g^a mod p = 5^6 mod 23 = 8`.
   - Envía `A = 8` a Bob.

3. Bob elige un número secreto `b = 15`.

   - Calcula: `B = g^b mod p = 5^15 mod 23 = 19`.
   - Envía `B = 19` a Alice.

4. Alice recibe `B` y calcula la clave compartida:

   - `K = B^a mod p = 19^6 mod 23 = 2`.

5. Bob recibe `A` y calcula la clave compartida:

   - `K = A^b mod p = 8^15 mod 23 = 2`.

👉 Ahora ambos comparten la clave secreta `K = 2`, sin haberla transmitido directamente.

### 🔒 Comparación de esquemas

| Esquema                                  | Seguridad                                 | Rapidez                               | Uso común                                             |
| ---------------------------------------- | ----------------------------------------- | ------------------------------------- | ----------------------------------------------------- |
| **RSA**                                  | Basado en factorización de primos grandes | Más lento                             | Intercambio de claves en SSL/TLS (versiones antiguas) |
| **Diffie-Hellman (DH)**                  | Basado en logaritmos discretos            | Más rápido                            | Fundamento de muchos protocolos modernos              |
| **ECDH (Elliptic Curve Diffie-Hellman)** | Basado en curvas elípticas                | Muy rápido y seguro con claves cortas | Usado en TLS moderno, Signal, WhatsApp                |

✅ **Conclusión**:

El intercambio de claves es **el primer paso para comunicación segura**.

- En la práctica, se suele usar **ECDH** o **RSA** para acordar una clave.
- Luego esa clave se usa en un algoritmo **simétrico (AES, ChaCha20)** para cifrar todo el tráfico.

---

[🔼](#índice)

---

## **338. Intercambio de claves secretas**

El **intercambio de claves secretas** (key exchange) es el proceso por el cual dos (o más) entidades que se comunican acuerdan una **clave secreta compartida** que usarán después para cifrar sus mensajes con cifrado simétrico. El reto es lograr esto **sobre un canal inseguro** (Internet) sin que un atacante que escucha o manipula la comunicación pueda conocer la clave.

### Objetivos y amenazas

**Objetivos**:

- Derivar una clave que solo conozcan las partes legítimas.
- Garantizar propiedades como **confidencialidad**, **integridad** y, idealmente, **perfect forward secrecy** (PFS).

**Amenazas**:

- **Eavesdropping** (escucha): atacante lee mensajes.
- **Man-in-the-middle (MITM)**: atacante intercala sus propias claves para comunicarse por separado con cada parte.
- **Replay, downgrade, malas RNG, ataques de fuerza bruta** (si se usan contraseñas débiles).

### Tipos (conceptuales) de intercambio de claves

1. **Key transport (transporte de clave)**

   - Una parte genera la clave simétrica y la _envía_ cifrada con la clave pública del receptor (ej. RSA-encrypt).
   - Simple pero depende de la seguridad de la clave pública y no proporciona PFS si la clave privada a largo plazo se compromete.

2. **Key agreement (acuerdo de clave)**

   - Ninguna parte envía la clave en claro; ambas colaboran para _calcular_ la misma clave (ej. Diffie-Hellman).
   - Permite variantes con claves efímeras para PFS.

3. **Pre-shared key (PSK)**

   - La clave ya existe previamente (compartida por un canal seguro físico o administrativo).
   - Útil en redes cerradas o dispositivos IoT, pero escalabilidad y distribución son problemas.

4. **Password-Authenticated Key Exchange (PAKE)**

   - Permite acordar una clave usando una contraseña compartida sin revelar la contraseña ni permitir ataques off-line eficaces (ej.: SRP, SPAKE2, OPAQUE).
   - Ideal cuando la autenticación se basa en contraseña.

### Esquemas concretos (y cuándo se usan)

#### 1) Diffie-Hellman (DH) — matemáticas sobre un grupo multiplicativo

- Idea: ambas partes usan exponenciación módulo un primo `p` con base `g`.
- Proporciona key _agreement_.
- **Vulnerabilidad**: si se usa sin autenticación, falla contra MITM.

**Ejemplo numérico simplificado (educativo)**
Parámetros públicos: `p = 23`, `g = 5`.

- Alice elige `a = 6`, calcula `A = g^a mod p = 5^6 mod 23 = 15625 mod 23 = 8`. Envía `A=8`.
- Bob elige `b = 15`, calcula `B = g^b mod p = 5^15 mod 23 = 30517578125 mod 23 = 19`. Envía `B=19`.
- Alice calcula `K = B^a mod p = 19^6 mod 23 = 2`.
- Bob calcula `K = A^b mod p = 8^15 mod 23 = 2`.

Clave compartida `K = 2`. Un eavesdropper ve `A=8` y `B=19` pero, sin resolver el logaritmo discreto, no puede obtener `a` o `b` fácilmente si `p` fuera muy grande.

#### 2) Ephemeral Diffie-Hellman (DHE) y ECDHE (curvas elípticas)

- **DHE**: DH con claves efímeras que cambian cada sesión → **PFS** (si la clave a largo plazo se compromete, sesiones pasadas siguen seguras).
- **ECDHE**: versión en curvas elípticas (más eficiencia y claves más cortas). Hoy en práctica se prefiere **X25519** (ECDH moderna, eficiente) para key exchange y **Ed25519** para firmas.

#### 3) RSA key transport

- Alice genera una clave simétrica `K`, cifra `K` con la clave pública de Bob (RSA), Bob descifra con su privada.
- Fácil pero no da PFS; si la privada de Bob se filtra, todas las claves transportadas quedan comprometidas.

#### 4) PAKEs (SRP, SPAKE2, OPAQUE)

- Diseñados para autenticar usando una contraseña sin exponerla y sin permitir offline brute-force eficaces.
- Ejemplos prácticos: SRP (usado históricamente), SPAKE2, OPAQUE (protocolos modernos de PAKE).

### Proceso seguro típico (pasos y buenas prácticas)

Un intercambio de claves moderno y seguro tiene estos componentes:

1. **Generación de claves efímeras** (ECDH / X25519): genera pares (privada, pública) por sesión.
2. **Autenticación** del interlocutor: con certificados (PKI), firmas digitales (Ed25519/RSA) o verificación por huella/fingerprint. Esto previene MITM.
3. **Intercambio de públicos**: se transmiten las claves públicas efímeras.
4. **Cálculo del secreto compartido**: ECDH → shared secret (bytes brutos).
5. **Derivación de claves (KDF)**: pasar el secreto por HKDF (o similar) con `salt` y `info` para obtener claves concretas (AES, MACs).
6. **Key confirmation**: verificar que ambas partes derivaron la misma clave (opcional pero recomendable).
7. **Uso de la clave simétrica**: cifrado/auth con AEAD (AES-GCM o ChaCha20-Poly1305).
8. **Rotación / expiración**: no usar una clave más tiempo del necesario.

### Ejemplo práctico y realista (ECDH + HKDF en Python — conceptual)

```py
# Ejemplo ilustrativo usando la librería cryptography (Python)
from cryptography.hazmat.primitives.asymmetric import ec
from cryptography.hazmat.primitives.kdf.hkdf import HKDF
from cryptography.hazmat.primitives import hashes

# 1) Generación de claves efímeras (Alice y Bob)
alice_priv = ec.generate_private_key(ec.SECP256R1())   # o X25519 en implementaciones modernas
bob_priv   = ec.generate_private_key(ec.SECP256R1())

alice_pub = alice_priv.public_key()
bob_pub   = bob_priv.public_key()

# 2) Intercambio (se intercambian alice_pub y bob_pub por la red)
# 3) Cálculo del secreto compartido
alice_shared = alice_priv.exchange(ec.ECDH(), bob_pub)
bob_shared   = bob_priv.exchange(ec.ECDH(), alice_pub)
assert alice_shared == bob_shared  # ahora tienen el mismo secreto bruto

# 4) Derivar claves simétricas con HKDF (hacer salt y info apropiados)
hkdf = HKDF(
    algorithm=hashes.SHA256(),
    length=32,     # por ejemplo, 256-bit AES key
    salt=b"salt_aleatorio_o_nonce",
    info=b"contexto:chat-session",
)
key = hkdf.derive(alice_shared)  # key usable por AES-GCM o ChaCha20-Poly1305
```

**Notas**:

- Usar `X25519` en producción es común hoy por rendimiento y seguridad.
- `salt` debe ser único por sesión (nonce).
- Nunca reutilices claves efímeras ni uses secretos sin pasar por un KDF.

### Autenticación: por qué es imprescindible

Un DH sin autenticación queda expuesto a MITM. Para autenticar puedes usar:

- **Certificados X.509**: la parte A firma su clave efímera con su clave privada larga (certificado verificado por CA). Ej.: TLS.
- **Firmas digitales**: firmar el mensaje `A || B || nonce`.
- **Pre-shared key (PSK)** o **PAKE**: si se comparte una contraseña o clave previa, se usa para autenticar el intercambio.
- **Verificación manual**: comparar _fingerprints_ (huellas) de claves fuera de banda (por ejemplo, Signal safety numbers).

### Propiedades de seguridad importantes

- **Perfect Forward Secrecy (PFS)**: las claves efímeras aseguran que comprometer la clave a largo plazo no compromete sesiones pasadas.
- **Key confirmation**: ambas partes confirman que comparten la misma clave (puede ser HMAC sobre la sesión).
- **Authenticity**: la identidad del interlocutor está verificada (certificados, firmas, PAKE).
- **Resistance a replay/downgrade**: incluir nonces y chequeos de versión.

### Ejemplos reales (protocolos que usan intercambio de claves)

- **TLS 1.3**: ECDHE (ephemeral) + certificado (firma) → clave de sesión + HKDF → AES-GCM/ChaCha20-Poly1305.
- **Signal / Double Ratchet**: X25519 para establecer claves iniciales, luego ratchet (rotación continua) para PFS y forward secrecy muy fuertes.
- **SSH**: ECDH o DH con autenticación por clave pública.
- **WPA3-SAE**: un PAKE moderno para redes Wi-Fi (protege contra ataques offline de contraseñas).

### Errores comunes y cómo evitarlos

- **Usar parámetros débiles** (p pequeños, curvas débiles) → evita y usa estándares modernos.
- **No autenticar el intercambio** → MITM.
- **Reutilizar claves efímeras** → pierde PFS.
- **Mala RNG** → claves predecibles; usa CSPRNG.
- **No derivar claves (usar secreto bruto)** → inseguro; siempre HKDF u otro KDF.
- **Implementar tu propio protocolo** → usa librerías/protocolos probados.

### Checklist práctico para implementar intercambio de claves seguro

1. Usa **X25519** o **secp256r1**/secp384r1 si X25519 no está disponible.
2. Genera claves **efímeras** por sesión (ECDHE).
3. **Autentica** el intercambio: certificados, firmas o PAKE.
4. Deriva claves con **HKDF** (incluye salt/nonce y `info`).
5. Usa AEAD (AES-GCM o ChaCha20-Poly1305) para cifrar mensajes.
6. Protege claves privadas (HSM o almacenamiento seguro).
7. Implementa **rotación** y corta vida a las claves.
8. Valida y verifica fingerprints si la autenticación es fuera de banda.
9. Usa librerías de reputación (OpenSSL, libsodium, libs/cryptography oficiales).
10. Prueba con herramientas y revisiones de seguridad (fuzzing, pentests).

### Resumen corto

- Intercambio de claves = acordar una clave secreta entre partes que se comunican por un medio inseguro.
- Las técnicas principales son **Diffie-Hellman** y sus variantes (ECDH, X25519), **RSA key transport**, **PAKEs** y **PSK**.
- La clave para que sea seguro: **autenticación + KDF + claves efímeras + buenas prácticas de implementación**.
- En la práctica usa protocolos probados (TLS1.3, Signal, WireGuard) o librerías consolidadas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**             | **Siguiente 4**                           |
| ------------------ | ----------------------- | ----------------------------------------- |
| [🏠](../README.md) | [⏪](./11_2_Hashing.md) | [⏩](./11_4_Private_Key_vs_Public_Key.md) |
