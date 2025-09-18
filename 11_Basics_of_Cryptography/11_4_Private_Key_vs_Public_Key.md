| **Inicio**         | **atrás 3**                  | **Siguiente 5**     |
| ------------------ | ---------------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./11_3_Key_Exchange.md) | [⏩](./11_5_PKI.md) |

---

## **Índice**

| Temario                                                                    |
| -------------------------------------------------------------------------- |
| [339. Intercambio de claves](#339-intercambio-de-claves)                   |
| [340. Intercambio de claves secretas](#340-intercambio-de-claves-secretas) |
|                                                                            |

# **Private Key vs Public Key**

## **339. Intercambio de claves**

![Private Key vs Public Key](/img/11_Basics_of_Cryptography/Private_Key_vs_Public_Key.png "Private Key vs Public Key")

### 🔐 1. ¿Qué es una clave SSH?

Una **clave SSH (Secure Shell Key)** es un par criptográfico de claves (pública y privada) que se usa para autenticarte de manera segura en servidores y sistemas remotos a través del protocolo **SSH**.

👉 Se utilizan para:

- Iniciar sesión en servidores remotos sin contraseña.
- Automatizar despliegues (ejemplo: GitHub, GitLab, Vercel, Render).
- Asegurar la comunicación entre máquinas.

### ⚙️ 2. ¿Cómo funcionan las claves SSH?

Las claves SSH se basan en **criptografía asimétrica**:

- **Clave privada** 🔐 → se guarda en tu máquina, nunca se comparte.
- **Clave pública** 🔓 → se copia en el servidor remoto (en `~/.ssh/authorized_keys`).

**Proceso de autenticación**:

1. El cliente (tu PC) quiere conectarse al servidor.
2. El servidor envía un desafío cifrado con la clave pública.
3. El cliente responde usando la clave privada.
4. Si coincide, se concede el acceso.

👉 Esto permite autenticación sin contraseña y con mayor seguridad.

### 🔑 3. Autenticación de clave pública SSH vs. contraseñas

| Método         | Ventajas                                                  | Desventajas                                         |
| -------------- | --------------------------------------------------------- | --------------------------------------------------- |
| **Contraseña** | Fácil de usar, no requiere configuración inicial.         | Vulnerable a fuerza bruta, phishing, keyloggers.    |
| **Clave SSH**  | Muy segura, resistente a ataques, permite automatización. | Requiere configuración inicial y gestión de claves. |

**Ejemplo**:

- Con contraseña: `ssh usuario@servidor` → pide clave cada vez.
- Con claves SSH: `ssh usuario@servidor` → acceso inmediato y seguro (si tu clave privada está cargada).

### 📂 4. Dónde encontrar tus claves SSH

En sistemas Unix/Linux/Mac, las claves se guardan en:

```
~/.ssh/
```

Archivos típicos:

- `id_rsa` → clave privada (NUNCA compartir).
- `id_rsa.pub` → clave pública (se comparte con el servidor).
- `config` → configuración personalizada de hosts.
- `known_hosts` → servidores conocidos en los que confías.

👉 Hoy en día se recomienda usar **Ed25519** en lugar de RSA por seguridad y rendimiento.

### ⚡ 5. Cómo generar un par de claves SSH

En tu máquina (Linux, Mac, WSL o Git Bash en Windows):

```bash
ssh-keygen -t ed25519 -C "tu_email@ejemplo.com"
```

Explicación:

- `-t ed25519` → tipo de clave (segura y moderna).
- `-C` → comentario (identifica la clave, normalmente tu correo).

El sistema te pedirá:

- Dónde guardar la clave (`~/.ssh/id_ed25519`).
- Una _passphrase_ opcional para proteger la clave privada.

📌 Archivos generados:

- `~/.ssh/id_ed25519` → privada.
- `~/.ssh/id_ed25519.pub` → pública.

### ⚙️ 6. Configuración de SSH

1. Copiar tu clave pública al servidor:

   ```bash
   ssh-copy-id usuario@servidor
   ```

   Esto la añade a `~/.ssh/authorized_keys` del servidor.

2. Conectarte al servidor:

   ```bash
   ssh usuario@servidor
   ```

3. (Opcional) Configurar un archivo `~/.ssh/config` para simplificar conexiones:

   ```ini
   Host miServidor
       HostName 123.45.67.89
       User usuario
       IdentityFile ~/.ssh/id_ed25519
   ```

   Ahora puedes conectarte con:

   ```bash
   ssh miServidor
   ```

### 📋 7. Gestión de claves SSH

Algunas buenas prácticas para manejar tus claves:

✅ **Usa claves modernas**

- Preferir `ed25519` o `ecdsa` frente a `rsa`.

✅ **Protege la clave privada**

- Establece permisos correctos:

  ```bash
  chmod 600 ~/.ssh/id_ed25519
  ```

- Usa passphrase para mayor seguridad.

✅ **Usa un agente SSH**

- Cargar claves en memoria para no ingresar la passphrase siempre:

  ```bash
  eval "$(ssh-agent -s)"
  ssh-add ~/.ssh/id_ed25519
  ```

✅ **Rotación periódica**

- Cambia claves cada cierto tiempo o si sospechas que fueron comprometidas.

✅ **Gestión multi-servicio**

- Puedes tener diferentes pares de claves para GitHub, servidores de trabajo, proyectos personales.

### 🧩 Ejemplo práctico (GitHub + Servidor personal)

1. Generas clave `ed25519`.
2. Copias `id_ed25519.pub` en tu perfil de GitHub.
3. Copias la misma clave en tu VPS (`ssh-copy-id`).
4. Configuras `~/.ssh/config` con dos hosts (`github.com` y `miServidor`).
5. Ahora puedes hacer:

   ```bash
   git clone git@github.com:usuario/repositorio.git
   ssh miServidor
   ```

   ¡Sin contraseñas! 🎉

---

[🔼](#índice)

---

## **340. Intercambio de claves secretas**

### 1) ¿Qué es el intercambio de claves secretas?

El **intercambio de claves** es el proceso por el cual dos (o más) partes que se comunican por un canal inseguro acuerdan una **clave secreta compartida** que luego usarán para cifrar y autenticar su comunicación simétrica.

Objetivo: que sólo las partes legítimas conozcan la clave, aun cuando un atacante pueda ver o manipular los mensajes en la red.

### 2) Conceptos clave que conviene entender

- **Key transport**: una parte genera la clave y la _transporta_ cifrada (ej. cifrar la clave con RSA).
- **Key agreement**: ambas partes colaboran para _calcular_ la misma clave sin enviarla directamente (ej. Diffie-Hellman).
- **Clave efímera**: generada sólo para la sesión; proporciona _Perfect Forward Secrecy (PFS)_.
- **KDF (Key Derivation Function)**: transforma el secreto bruto (resultado del intercambio) en claves concretas (AES, MAC) con salt/nonce/context (ej. HKDF).
- **AEAD**: modos de cifrado autenticados (AES-GCM, ChaCha20-Poly1305) para proteger confidencialidad + integridad.

### 3) Amenazas principales

- **Eavesdropping**: atacante escucha pero no altera (intercambio debe resistirlo).
- **Man-in-the-middle (MITM)**: atacante intercepta y sustituye claves públicas → sin autenticación, MITM logra dos claves diferentes y puede leer/alterar tráfico.
- **Replay, downgrade, malas RNG**: problemas operativos que reducen seguridad.

Para evitar MITM necesitas **autenticación** (certificados, firmas, PSK, PAKE, verificación de huella).

### 4) Esquemas comunes con ejemplos y cuándo usarlos

#### A) Diffie-Hellman (DH) — ejemplo numérico educativo

Parámetros públicos: primo `p = 23`, base `g = 5`. _(Ejemplo pequeño sólo educativo — en producción p debe ser enorme.)_

- Alice: escoge secreto `a = 6`, calcula `A = g^a mod p = 5^6 mod 23 = 8`. Envía `A=8`.
- Bob: escoge secreto `b = 15`, calcula `B = g^b mod p = 5^15 mod 23 = 19`. Envía `B=19`.
- Alice calcula `K = B^a mod p = 19^6 mod 23 = 2`.
- Bob calcula `K = A^b mod p = 8^15 mod 23 = 2`.

Ambos obtienen `K = 2` sin que la clave haya sido transmitida. Con parámetros grandes esto es seguro frente a cálculo eficiente del logaritmo discreto.

##### Variante práctica: **DHE** (ephemeral DH) y **ECDHE** (curvas elípticas)

- Si usas _efímeras_ por sesión → **PFS**: comprometer una clave a largo plazo no revela sesiones pasadas.
- **ECDHE / X25519** son los métodos modernos: menor tamaño de clave, más rápidos y seguros.

#### B) RSA key transport

- Bob publica su clave pública RSA.
- Alice genera una clave simétrica `K`, la cifra con la pública de Bob (RSA-OAEP) y la envía.
- Bob descifra con su privada → obtiene `K`.
  Fácil, pero **no proporciona PFS** (si la privada de Bob se compromete, las claves pasadas se pueden recuperar si fueron grabadas).

#### C) PAKE (Password-Authenticated Key Exchange)

- Protocolos que permiten acordar claves usando una contraseña sin exponerla ni permitir ataques offline eficaces.
- Ejemplos: SRP (histórico), SPAKE2, OPAQUE (moderno).
  Útiles cuando la autenticación se basa en contraseñas (Wi-Fi con WPA3-SAE es un PAKE).

#### D) PSK (Pre-Shared Key)

- La clave ya ha sido compartida antes por un canal seguro (p. ej. IoT en red cerrada).
- Simple pero poco escalable y arriesga si la clave se filtra.

### 5) De secreto bruto a claves útiles: KDF y AEAD

El intercambio (DH/ECDH/X25519) produce un **secreto compartido bruto** (bytes). **Nunca** usar ese secreto directamente: pásalo por un **KDF** (ej. HKDF-SHA256) junto con `salt`/`nonce`/`info` para derivar:

- clave de cifrado (ej. 256 bits para AES-256 o 256-bit key para ChaCha20),
- clave de autenticación / MAC,
- y/o nonces.

Luego cifrar con **AEAD** (AES-GCM o ChaCha20-Poly1305) y usar MACs para confirmación si aplica.

### 6) Ejemplo práctico en Python (X25519 + HKDF + ChaCha20-Poly1305)

Código realista usando `cryptography` (concepto listo para probar en tu entorno):

```py
# Requiere: pip install cryptography
import os
from cryptography.hazmat.primitives.asymmetric import x25519
from cryptography.hazmat.primitives.kdf.hkdf import HKDF
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.ciphers.aead import ChaCha20Poly1305

# 1) Generar pares efímeros
alice_priv = x25519.X25519PrivateKey.generate()
alice_pub_bytes = alice_priv.public_key().public_bytes(
    encoding = serialization.Encoding.Raw,
    format   = serialization.PublicFormat.Raw
)

bob_priv = x25519.X25519PrivateKey.generate()
bob_pub_bytes = bob_priv.public_key().public_bytes(
    encoding = serialization.Encoding.Raw,
    format   = serialization.PublicFormat.Raw
)

# 2) Intercambio de públicos (transmitir alice_pub_bytes/bob_pub_bytes)
# (en la práctica se envían por la red y se autentican por firma/certificado)

# 3) Calcular secreto compartido
alice_shared = alice_priv.exchange(x25519.X25519PublicKey.from_public_bytes(bob_pub_bytes))
bob_shared   = bob_priv.exchange(x25519.X25519PublicKey.from_public_bytes(alice_pub_bytes))
assert alice_shared == bob_shared

# 4) Derivar clave con HKDF
hkdf = HKDF(
    algorithm=hashes.SHA256(),
    length=32,
    salt=os.urandom(16),         # salt único por sesión
    info=b'handshake v1'
)
key = hkdf.derive(alice_shared)  # 32 bytes

# 5) Cifrar con AEAD (ChaCha20-Poly1305)
aead = ChaCha20Poly1305(key)
nonce = os.urandom(12)
ciphertext = aead.encrypt(nonce, b"mensaje secreto", associated_data=b"contexto")
# Para descifrar, la otra parte usa la misma key y nonce
plaintext = aead.decrypt(nonce, ciphertext, associated_data=b"contexto")
```

**Notas prácticas**:

- En producción: autentica `alice_pub_bytes` y `bob_pub_bytes` (firma o certificado).
- Guarda `salt`/`nonce` y `info` necesarios para derivación/descifrado.

### 7) Ejemplo de RSA key transport en Python (clave simétrica cifrada con RSA-OAEP)

```py
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.primitives import hashes

# Generar par RSA (demostración)
priv = rsa.generate_private_key(public_exponent=65537, key_size=2048)
pub = priv.public_key()

# Alice genera clave simétrica
sym_key = os.urandom(32)  # AES-256 key

# Alice cifra la clave con la pública de Bob
ciphertext = pub.encrypt(
    sym_key,
    padding.OAEP(mgf=padding.MGF1(algorithm=hashes.SHA256()),
                 algorithm=hashes.SHA256(), label=None)
)

# Bob descifra con su privada
recovered = priv.decrypt(
    ciphertext,
    padding.OAEP(mgf=padding.MGF1(algorithm=hashes.SHA256()),
                 algorithm=hashes.SHA256(), label=None)
)
assert recovered == sym_key
```

### 8) Autenticación y prevención de MITM

Un intercambio DH sin autenticación es vulnerable a MITM. Opciones para autenticar:

- **Certificados X.509**: servidor firma su clave efímera con su privada; cliente valida cadena de confianza (TLS).
- **Firmas digitales**: firma la clave efímera y envía la firma junto con el público.
- **PAKE**: autentica con contraseña sin exponerla.
- **PSK**: autenticación mediante clave precompartida.
- **Verificación de huellas**: comparar fingerprints fuera de banda (Signal safety numbers).

En protocolos modernos (TLS 1.3): se usa **ECDHE** efímero + certificado del servidor (firma) → previene MITM y aporta PFS.

### 9) Propiedad deseable: Perfect Forward Secrecy (PFS)

- Si una parte usa claves efímeras para cada sesión (DHE/ECDHE), el compromiso de claves a largo plazo **no** permite descifrar sesiones antiguas grabadas.
- PFS es una **requisito** para comunicaciones sensibles (mensajería segura, VPNs).

### 10) Buenas prácticas y checklist

- Prefiere **X25519** o **secp256r1/secp384r1** para ECDH (X25519 recomendado por simplicidad y seguridad).
- Genera **claves efímeras** para sesiones y rota claves según política.
- **Autentica** el intercambio (certificados, firma, PAKE).
- Pasa el secreto por **HKDF** (usar salt/nonce y `info` contextual).
- Usa **AEAD** (AES-GCM o ChaCha20-Poly1305) para cifrar la sesión.
- Protege claves privadas (HSM, almacenamiento seguro).
- Usa CSPRNG (no RNGs débiles).
- No reutilices nonces.
- Valida parámetros y usa librerías maduras (OpenSSL, libsodium, cryptography, libsodium wrappers).

### 11) Errores comunes

- No autenticar → MITM trivial.
- Reusar claves efímeras → pierde PFS.
- Usar parámetros débiles o curvas obsoletas.
- Usar secreto bruto sin KDF.
- Mala gestión de nonces o salt.
- Implementar tu propio protocolo en vez de usar estándares probados.

### 12) ¿Cómo se ve en la práctica (resumen del flujo TLS1.3 simplificado)?

1. Cliente → Server: lista de cifrados y parámetros (ClientHello) + clave efímera pública.
2. Server → Client: clave efímera pública del servidor + certificado (firma sobre la clave efímera).
3. Ambos calculan secreto compartido (ECDHE), lo pasan por HKDF → claves de sesión.
4. Comunicaciones cifradas con AEAD — autenticadas y con PFS.

### 13) Cuándo usar cada esquema

- **Comunicación servidor-cliente general (web)**: ECDHE + certificados (TLS1.3).
- **Mensajería instantánea con máxima privacidad**: X25519 + ratchet (Signal).
- **Dispositivos IoT/entornos cerrados**: PSK o ECDHE con certificados si posible.
- **Autenticación por contraseña**: PAKEs (OPAQUE, SPAKE2) para evitar ataques offline.
- **Sistemas legados**: RSA key transport puede funcionar, pero planifica migración a ECDHE para obtener PFS.

### 14) Recursos/prácticos (bibliotecas recomendadas)

- **Python**: `cryptography` (ECDH/X25519, HKDF, AEAD).
- **C/++**: OpenSSL, libsodium (mucho más sencillo y seguro para primitives modernos).
- **Node.js**: módulo `crypto` (ECDH) y libsodium wrappers (`libsodium-wrappers`).
- **Go / Rust**: libs estándar y crates/paquetes de confianza para X25519 y AEAD.

### Resumen final rápido

- El intercambio de claves permite a dos partes acordar una clave simétrica sin enviarla en claro.
- **Diffie-Hellman/ECDH (X25519)** es la opción moderna para key agreement con PFS; **RSA** sirve para key transport pero sin PFS.
- **Autenticación + KDF + AEAD + claves efímeras** constituyen la receta para un handshake seguro.
- Usa librerías probadas y parámetros actuales, y siempre autentica para evitar MITM.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**                  | **Siguiente 5**     |
| ------------------ | ---------------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./11_3_Key_Exchange.md) | [⏩](./11_5_PKI.md) |
