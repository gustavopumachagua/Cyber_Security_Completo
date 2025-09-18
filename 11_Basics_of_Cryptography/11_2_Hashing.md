| **Inicio**         | **atrás 1**             | **Siguiente 3**              |
| ------------------ | ----------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./11_1_Salting.md) | [⏩](./11_3_Key_Exchange.md) |

---

## **Índice**

| Temario                                                                                                                                       |
| --------------------------------------------------------------------------------------------------------------------------------------------- |
| [333. ¿Qué es el hashing y cómo funciona?](#333-qué-es-el-hashing-y-cómo-funciona)                                                            |
| [334. Descripción general del algoritmo hash tipos, metodologías y uso](#334-descripción-general-del-algoritmo-hash-tipos-metodologías-y-uso) |
| [335. Comprensión de los tipos de criptografía](#335-comprensión-de-los-tipos-de-criptografía)                                                |
| [336. Hash explicado](#336-hash-explicado)                                                                                                    |

# **Hashing**

## **333. ¿Qué es el hashing y cómo funciona?**

![Hashing](/img/11_Basics_of_Cryptography/Hashing.webp "Hashing")

### 1) Resumen rápido

Un **hash** (o función hash) toma un mensaje de **cualquier tamaño** y lo transforma en una **salida de tamaño fijo** (el _digest_). En criptografía usamos funciones hash con propiedades especiales: deben ser **deterministas**, rápidas de calcular, pero difíciles de invertir o de encontrar colisiones (dependiendo del tipo de hash).

### 2) Propiedades importantes de una función hash criptográfica

- **Determinista**: la misma entrada → siempre la misma salida.
- **Salida de tamaño fijo**: p. ej. SHA-256 siempre produce 256 bits (32 bytes → 64 hex).
- **Preimage resistance (resistencia a preimagen)**: dado el hash H, debe ser difícil encontrar un mensaje M tal que hash(M)=H.
- **Second-preimage resistance**: dado M1, difícil encontrar M2 ≠ M1 con hash(M2)=hash(M1).
- **Collision resistance**: difícil encontrar _cualquier_ par distinto (M1, M2) con el mismo hash.
- **Avalanche effect**: cambiar 1 bit en la entrada cambia drásticamente el hash.
- **Velocidad**: criptográficamente deben ser razonablemente rápidas; para contraseñas se prefieren funciones deliberadamente lentas/memoriosas (bcrypt, Argon2).

### 3) Ejemplo _toy_ (muy simple) para entender la idea

Definamos un hash sencillo: `H(m) = (suma de los códigos ASCII de los caracteres de m) mod 256`.

Ejemplo con `"hola"`:

- `'h'` = 104
- `'o'` = 111
- `'l'` = 108
- `'a'` = 97

  Cálculo paso a paso:

  104 + 111 = 215

  215 + 108 = 323

  323 + 97 = 420

  420 mod 256 = 164

Entonces `H("hola") = 164`.

Con este toy-hash:

- Es **determinista** (siempre 164 para "hola").
- **Colisiones** son muy frecuentes (pocas salidas: sólo 256 valores). Ejemplo de colisión simple con modulo 10: `'a'` (97) y `'k'` (107) → 97 mod 10 = 7 y 107 mod 10 = 7, ambos dan 7.

  Esto muestra por qué necesitamos funciones hash con salidas largas y propiedades criptográficas.

### 4) Ejemplo real: SHA-256 (hash criptográfico común)

SHA-256 produce siempre 256 bits = 32 bytes. En hexadecimal eso aparece como 64 caracteres hex (cada hex = 4 bits → 256/4 = 64).

Cálculos con SHA-256 (hex digest):

- `SHA256("hola")` =
  `b221d9dbb083a7f33428d7c2a3c3198ae925614d70210e28716ccaa7cd4ddb79`

- `SHA256("Hola")` =
  `e633f4fc79badea1dc5db970cf397c8248bac47cc3acf9915ba60b5d76b0e88f`

Observa el **avalanche effect**: solo se cambió la primera letra minúscula → mayúscula, y el hash resultante es totalmente diferente.

También, `SHA256("hola\n")` (con un salto de línea) produce otro hash distinto:
`133ee989293f92736301280c6f14c89d521200c17dcdcecca30cd20705332d44`

> Nota técnica: SHA-256 y otras funciones modernas (SHA-3) están diseñadas para que invertir el proceso o encontrar colisiones sea computacionalmente inviable con la tecnología actual.

### 5) Ejemplo de HMAC (mensaje autenticado con clave)

Un **HMAC** combina una clave secreta con una función hash para producir un MAC (message authentication code) seguro frente a ataques como length-extension.

Ejemplo (HMAC-SHA256) con clave `"secretkey"` y mensaje `"hola"`:

- `HMAC_SHA256(key="secretkey", message="hola")` =
  `c5fd2eb9854596372584b2a2a4775c3b8210527e27abc44dfe43250b7a56f652`

HMAC se usa para verificar **integridad + autenticidad** (mensaje no modificado y proviene del dueño de la clave).

### 6) Código de ejemplo (cómo calcular hashes en práctica)

Python (hash SHA-256):

```py
import hashlib
s = "hola"
h = hashlib.sha256(s.encode('utf-8')).hexdigest()
print(h)
# -> b221d9db... (igual al ejemplo)
```

Node.js (SHA-256):

```js
const crypto = require("crypto");
const h = crypto.createHash("sha256").update("hola", "utf8").digest("hex");
console.log(h);
```

Python (HMAC-SHA256):

```py
import hmac, hashlib
h = hmac.new(b"secretkey", b"hola", hashlib.sha256).hexdigest()
print(h)
# -> c5fd2e...
```

Password hashing (NO usar SHA-256 solo para contraseñas — usar bcrypt/Argon2/pbkdf2):

Node.js (bcrypt):

```js
const bcrypt = require("bcrypt");
const hash = await bcrypt.hash("MiClave123!", 12); // bcrypt genera la sal internamente
```

Python (argon2):

```py
from argon2 import PasswordHasher
ph = PasswordHasher()
hash = ph.hash("MiClave123!")
```

### 7) ¿Por qué no usar un hash "simple" (como SHA-256) para contraseñas?

- SHA-256 es **rápido**, por eso un atacante puede probar millones de contraseñas por segundo con GPU/ASIC.
- Para contraseñas se prefieren **KDFs** (Key Derivation Functions) que sean **lentas y/o consuman memoria**:

  - **bcrypt**, **scrypt**, **Argon2** (Argon2id recomendado)
  - **PBKDF2** (configurable con iteraciones)

- Además combinar **salt** único por usuario y, opcionalmente, un **pepper** (secreto global) fuera de la DB mejora seguridad.

### 8) Casos de uso del hashing

- **Almacenamiento de contraseñas** (con sal + KDF).
- **Integridad de ficheros** (checksums, p. ej. SHA-256 al descargar un archivo).
- **Firmas digitales**: se "hashea" el mensaje y luego se firma el hash con clave privada.
- **Merkle trees** y **blockchains**: combinar hashes para obtener una raíz que verifica el conjunto.
- **Content-addressing**: identificar objetos por su hash (p. ej. IPFS, Git usa hashes para objetos).

### 9) Problemas comunes y recomendaciones

- **No crear tu propio algoritmo**: usa implementaciones probadas.
- **Evitar MD5 y SHA-1** para seguridad (existen colisiones prácticas).
- **Usar HMAC** para autenticación de mensajes (no `hash(secret || message)` a secas — vulnerable a length-extension en construcciones tipo Merkle-Damgård).
- **Para contraseñas**: usar Argon2/bcrypt/scrypt + salt por usuario.
- **Para integridad**: SHA-256 o SHA-3 son buenas opciones modernas.
- **Si necesitas velocidad (no seguridad)** para deduplicación o hashing no crítico, usar hashes no criptográficos optimizados (xxHash, MurmurHash), pero con cuidado: no son seguros criptográficamente.

### 10) Concepto clave final (por qué el hashing es útil)

- Hashing **condensa** datos arbitrarios a una **huella corta y fácil de comparar**.
- Las funciones hash criptográficas añaden la propiedad de que esa huella **no revela** fácilmente el dato original y es **resistente a manipulación**, lo que las hace imprescindibles para integridad, autenticación y sistemas distribuidos.

---

[🔼](#índice)

---

## **334. Descripción general del algoritmo hash tipos, metodologías y uso**

### 🔹 1. ¿Qué es un algoritmo hash?

Un **algoritmo hash** es una función matemática que transforma una entrada de datos de cualquier tamaño (**texto, archivo, contraseña, mensaje**) en una salida de **longitud fija** llamada **hash** o **digest**.

👉 Esa salida es como una **huella digital**:

- Fácil de calcular.
- Muy difícil de revertir.
- Si cambia un solo bit de la entrada, el hash cambia drásticamente (_avalanche effect_).

Ejemplo (SHA-256):

- Entrada: `"hola"`
- Hash: `b221d9dbb083a7f33428d7c2a3c3198ae925614d70210e28716ccaa7cd4ddb79`

### 🔹 2. ¿Cómo funciona un algoritmo hash?

Aunque cada algoritmo tiene su propia mecánica interna, en general:

1. **Se toma la entrada** (ej: archivo, contraseña, mensaje).
2. **Se divide en bloques** de datos.
3. Cada bloque pasa por una **función de compresión** (operaciones matemáticas, bit a bit, sustituciones y permutaciones).
4. Se combinan todos los bloques hasta obtener un **digest de longitud fija**.

⚡ Propiedades importantes:

- **Determinismo** → siempre el mismo hash para la misma entrada.
- **Resistencia a colisiones** → difícil encontrar dos entradas diferentes con el mismo hash.
- **Resistencia a preimagen** → dado un hash, es casi imposible recuperar la entrada original.
- **Avalanche effect** → un pequeño cambio en la entrada altera todo el hash.

Ejemplo práctico:

- `"Hola"` y `"hola"` tienen hashes totalmente diferentes en SHA-256.

### 🔹 3. ¿Para qué se utilizan los algoritmos hash?

Los algoritmos hash tienen aplicaciones en **seguridad informática** y en **tecnología en general**:

1. ✅ **Almacenamiento seguro de contraseñas** (usando hashing con sal + algoritmos lentos como bcrypt o Argon2).
2. ✅ **Verificación de integridad de archivos** (ej: al descargar software, se publica un SHA-256 para confirmar que no fue alterado).
3. ✅ **Firmas digitales** (se firma el hash del mensaje, no el mensaje entero → más rápido y eficiente).
4. ✅ **Blockchain y criptomonedas** (Bitcoin usa SHA-256 para encadenar bloques y validar transacciones).
5. ✅ **Tablas hash** en programación (para búsquedas rápidas).
6. ✅ **Control de duplicados** (almacenar hash de archivos para detectar repetidos sin comparar el contenido completo).

### 🔹 4. Ejemplos de algoritmos hash

Existen **algoritmos hash criptográficos** (seguros) y **no criptográficos** (rápidos, pero no seguros).

- **Hash criptográficos clásicos**:

  - **MD5** → obsoleto (colisiones fáciles).
  - **SHA-1** → obsoleto (colisiones demostradas).
  - **SHA-2** (SHA-224, SHA-256, SHA-512) → todavía seguros.
  - **SHA-3** (Keccak) → estándar más moderno.

- **Hash para contraseñas (Key Derivation Functions, KDFs)**:

  - **bcrypt**
  - **scrypt**
  - **Argon2** (recomendado hoy).

- **Hash no criptográficos (para búsquedas rápidas, no seguridad)**:

  - MurmurHash
  - xxHash
  - CityHash

### 🔹 5. Explicación de los algoritmos hash más populares

#### 🔸 MD5

- Longitud: 128 bits (32 caracteres hex).
- Muy usado en los 90s, pero **inseguro** por colisiones.
- Ejemplo: `MD5("hola") = 4d186321c1a7f0f354b297e8914ab240`.

#### 🔸 SHA-1

- Longitud: 160 bits (40 hex).
- Usado en certificados SSL/TLS antiguos.
- Hoy **inseguro**: Google demostró colisiones en 2017.

#### 🔸 SHA-2 (SHA-256, SHA-512)

- Longitud: 256 o 512 bits.
- Estándar en seguridad moderna (TLS, Bitcoin, firmas digitales).
- Ejemplo:
  `SHA-256("hola") = b221d9dbb083a7f33428d7c2a3c3198ae925614d70210e28716ccaa7cd4ddb79`

#### 🔸 SHA-3 (Keccak)

- Último estándar (2015).
- Usa estructura **sponge** (distinta a SHA-2).
- Más resistente a ataques futuros.

#### 🔸 bcrypt, scrypt, Argon2

- Diseñados específicamente para **contraseñas**.
- Son **lentos** y consumen memoria, lo que dificulta ataques de fuerza bruta.
- bcrypt ya incorpora **sal** automáticamente.
- Argon2 es el ganador del **Password Hashing Competition (PHC)** en 2015 y el más recomendado hoy.

### 🔹 6. ¿Cuánto deberías saber sobre el hashing?

Depende de tu perfil e intereses, pero como **data scientist y futuro especialista en ciberseguridad** te conviene:

#### Nivel básico (todos deberían saber):

- Qué es un hash y para qué sirve.
- Diferencia entre MD5/SHA-1 (inseguros) y SHA-2/SHA-3 (seguros).
- Que nunca se almacenan contraseñas en texto plano → se hashean.

#### Nivel intermedio (lo que un desarrollador/científico de datos debe manejar):

- Saber calcular hashes en Python, Node, etc.
- Uso de hashing para integridad de datos (ej: verificar datasets).
- Entender la diferencia entre hash criptográfico y no criptográfico.
- Conocer sal y pepper para proteger contraseñas.

#### Nivel avanzado (ciberseguridad):

- Diferencia entre hashing, cifrado y HMAC.
- Por qué usar bcrypt/scrypt/Argon2 para contraseñas.
- Vulnerabilidades de MD5 y SHA-1 (colisiones prácticas).
- Entender ataques como _rainbow tables_ y fuerza bruta.
- Saber aplicar hashing en blockchain, firmas digitales y protocolos seguros.

---

[🔼](#índice)

---

## **335. Comprensión de los tipos de criptografía**

### 🔐 1. Principios básicos de la criptografía

La **criptografía** es el estudio de técnicas que permiten **proteger información** mediante la transformación de datos, de modo que solo las personas autorizadas puedan leerlos o verificarlos.

👉 Sus objetivos principales son:

- **Confidencialidad** → solo el destinatario puede leer la información.
- **Integridad** → los datos no deben ser alterados sin detección.
- **Autenticación** → verificar la identidad de las partes.
- **No repudio** → el emisor no puede negar que envió un mensaje.

### 🔑 2. Criptografía de clave simétrica

- Usa **una sola clave secreta** para cifrar y descifrar.
- Ejemplo: **AES, DES, ChaCha20**.

Ejemplo:

- Mensaje: `"Hola Gustavo"`
- Clave: `"ABC123"`
- Cifrado (con AES-256): `7df4e2c9a1f...`
- Para leerlo, el receptor necesita la misma clave `"ABC123"`.

#### ✅ Ventajas

- Muy rápida (apta para grandes volúmenes de datos).
- Menos consumo de recursos que la asimétrica.
- Algoritmos modernos como AES son muy seguros.

#### ❌ Desventajas

- **Problema de distribución de claves**: ¿cómo compartes la clave de forma segura si ambos la necesitan?
- Si la clave se filtra, cualquiera puede descifrar la información.

### 🔑 3. Criptografía de clave asimétrica

- Usa un **par de claves**:

  - **Clave pública** → se comparte libremente (para cifrar o verificar).
  - **Clave privada** → se mantiene en secreto (para descifrar o firmar).

- Ejemplo: **RSA, ECC, Diffie-Hellman**.

Ejemplo con RSA:

- Gustavo genera un par de claves:

  - Pública: 🔓`(n, e)`
  - Privada: 🔐`(n, d)`

- Alguien cifra un mensaje con la clave pública de Gustavo.
- Solo Gustavo (con la privada) puede descifrarlo.

#### ✅ Ventajas

- No es necesario compartir claves secretas → más seguro.
- Permite **firmas digitales** y **autenticación**.
- Fundamental en **Internet (SSL/TLS, HTTPS)**.

#### ❌ Desventajas

- Mucho más lenta que la simétrica.
- No apta para cifrar grandes volúmenes de datos directamente.
- Puede requerir mayor consumo de recursos.

### 🔑 4. Función hash

- No es cifrado → es un **proceso unidireccional**.
- Convierte datos de cualquier tamaño en un valor de longitud fija (**digest**).
- Ejemplo:

  - `SHA-256("hola")` = `b221d9dbb083a7f33428d7c2a3c3198a...`

👉 Usos:

- Almacenamiento seguro de contraseñas (con sal y algoritmos como bcrypt o Argon2).
- Verificación de integridad (ej: comparar hash de un archivo descargado).
- Blockchain (enlaces entre bloques).

### 🌍 5. Aplicaciones de la criptografía en la vida real

- **Comunicación segura** → WhatsApp, Signal (cifrado extremo a extremo).
- **Banca online** → cifrado de transacciones, firmas digitales.
- **E-commerce** → protocolos SSL/TLS (HTTPS).
- **Firmas digitales** → contratos electrónicos, certificados.
- **Criptomonedas y blockchain** → Bitcoin, Ethereum (SHA-256, ECC).
- **Autenticación** → tokens, certificados, autenticación multifactor.
- **Protección de datos personales** → GDPR, HIPAA requieren cifrado.

### ⚠️ 6. Desafíos en la criptografía

1. **Gestión de claves**:

   - ¿Cómo generar, distribuir y almacenar claves de forma segura?

2. **Avances tecnológicos**:

   - Criptografía actual puede romperse con **computación cuántica** (RSA, ECC en riesgo).
   - Investigación en **criptografía poscuántica**.

3. **Errores humanos**:

   - Uso de contraseñas débiles, mala configuración de algoritmos.

4. **Obsolescencia de algoritmos**:

   - MD5 y SHA-1 ya no son seguros, pero aún se encuentran en sistemas antiguos.

5. **Equilibrio entre seguridad y rendimiento**:

   - Algoritmos muy fuertes pueden ser lentos y costosos de implementar.

### 🧩 Conclusión

- La **criptografía simétrica** es rápida y eficiente, ideal para grandes volúmenes, pero con problemas de distribución de claves.
- La **criptografía asimétrica** soluciona el problema de compartir claves, pero es más lenta.
- Las **funciones hash** son fundamentales para integridad y contraseñas.
- En la práctica, se suelen **combinar**:

  - Asimétrica para intercambiar la clave.
  - Simétrica para cifrar datos masivos.
  - Hash para verificar integridad y autenticación.

---

[🔼](#índice)

---

## **336. Hash explicado**

### 1) ¿Qué es un _hash_?

Un **hash** (o _digest_) es el resultado de aplicar una **función hash** a unos datos.

Una **función hash** toma una entrada de **cualquier tamaño** (texto, archivo, mensaje) y devuelve una **salida de longitud fija** —esa salida es el _hash_. Es una especie de “huella digital” del dato.

Ejemplo simple: si aplicas un hash a la cadena `"hola"`, obtendrás un valor fijo (en SHA-256, por ejemplo, un bloque hexadecimal de 64 caracteres).

### 2) Propiedades importantes de una función hash criptográfica

- **Determinista**: la misma entrada → mismo hash.
- **Salida de tamaño fijo**: p. ej. SHA-256 siempre devuelve 256 bits (64 hex).
- **Resistencia a preimagen**: dado H, difícil encontrar M tal que `hash(M)=H`.
- **Resistencia a segunda preimagen**: dado M1, difícil hallar M2 ≠ M1 con `hash(M2)=hash(M1)`.
- **Resistencia a colisiones**: difícil hallar _cualquier_ par distinto con el mismo hash.
- **Avalanche effect**: cambiar 1 bit en la entrada cambia drásticamente el hash.
- **Velocidad controlada**: las funciones normales son rápidas; para contraseñas se prefieren funciones deliberadamente lentas/memoriosas (bcrypt, Argon2).

### 3) Cómo funciona (visión práctica y simplificada)

1. **Entrada** (mensaje, archivo, contraseña).
2. **Padding y división en bloques** (la entrada se adapta/añade para encajar en el algoritmo).
3. Cada **bloque** pasa por una **compresión**: operaciones bit a bit, rotaciones, XOR, sumas, tablas S, etc.
4. Se combina el estado intermedio con el siguiente bloque hasta obtener un **estado final fijo** → el hash.

> Nota: Cada algoritmo (SHA-2, SHA-3, MD5) implementa esas etapas de forma distinta; el resultado es siempre un digest de tamaño fijo.

### 4) Ejemplo _toy_ (para entender la idea)

Función hipotética: `H(m) = (suma ASCII de caracteres de m) mod 256`.

- `"hola"` → códigos ASCII: 104,111,108,97 → suma = 420 → `420 mod 256 = 164`
  `H("hola") = 164`.

Esto muestra: determinismo y que pequeñas funciones simples producen _muchas_ colisiones — por eso necesitamos funciones complejas y salidas largas.

### 5) Ejemplo real: SHA-256 (salida visible)

- `SHA256("hola") = b221d9dbb083a7f33428d7c2a3c3198ae925614d70210e28716ccaa7cd4ddb79`
- `SHA256("Hola")` (H mayúscula) → hash completamente distinto (avalanche effect).

### 6) Tipos de hashes y casos de uso

1. **Hashes criptográficos** (integridad, firmas, blockchain): SHA-2, SHA-3.
2. **Hashes para contraseñas** (KDFs, lentos y con memoria): bcrypt, scrypt, Argon2 (recomendado).
3. **Hashes no criptográficos** (alto rendimiento, no seguros): MurmurHash, xxHash — útiles en tablas hash, deduplicación, índices.
4. **HMAC**: MAC basado en hash que usa una clave secreta para autenticar mensajes (HMAC-SHA256, etc.).

Usos comunes: almacenamiento seguro de contraseñas, verificación de integridad de archivos, firmas digitales, Merkle trees (blockchain), deduplicación, caching.

### 7) HMAC y por qué es diferente a solo hashear con clave

- HMAC combina una **clave secreta** y una función hash de manera que evita ataques como _length-extension_.
- **No** es seguro hacer `hash(secret || message)` y usarlo para autenticar; usar HMAC en su lugar.

Ejemplo conceptual en Python:

```py
import hmac, hashlib
mac = hmac.new(b"mi_clave_secreta", b"mensaje", hashlib.sha256).hexdigest()
```

### 8) Length-extension attack (breve explicación)

Algunos hashes construidos con la estructura Merkle–Damgård (p. ej. SHA-1, MD5) permiten extender `hash(secret || message)` a `hash(secret || message || extra)` sin conocer `secret` — por eso **HMAC** (que evita esto) es la forma correcta de autenticar mensajes.

### 9) Colisiones y la paradoja del cumpleaños

- Para un hash de `n` bits, la dificultad práctica de **encontrar una colisión** suele ser del orden de `2^(n/2)` (paradoja del cumpleaños).
- Ejemplo: para SHA-256 (`n = 256`), la dificultad es ≈ `2^128` operaciones, lo cual es _astronómicamente_ alto (≈ 3.4 × 10^38).
  → encontrar colisiones en SHA-256 **no es práctico** hoy.

(Valores aproximados: `2^128 ≈ 3.4028e38`, `2^256 ≈ 1.1579e77`.)

### 10) Hashing de contraseñas — puntos clave (sal, pepper, KDF)

- **Sal (salt)**: valor aleatorio único por usuario, concat/mezclado antes del hash. Previene rainbow tables y hace que hashes iguales sean distintos. Sal no es secreto. Recomendación: **≥ 16 bytes** aleatorios.
- **Pepper**: secreto global (opcional) fuera de la base de datos (p. ej. variable de entorno o HSM). Añade resistencia si la BD se filtra.
- **Usar KDFs**: no uses SHA-256 puro. Usa **Argon2id**, **bcrypt** o **scrypt**.
- **Parámetros**: balancea tiempo/memoria según hardware. Ejemplo Argon2 (orientativo): `time=2, memory=65536 KB, parallelism=1` — pero siempre **tunea** según tu infra.

Ejemplo en Node.js con `argon2`:

```js
const argon2 = require("argon2");
const hash = await argon2.hash("MiClaveSegura123!", { type: argon2.argon2id });
```

Ejemplo en Python con `argon2-cffi`:

```py
from argon2 import PasswordHasher
ph = PasswordHasher()
hash = ph.hash("MiClaveSegura123!")
```

> Nota: bcrypt automáticamente genera una sal y la integra en el formato de hash resultante.

### 11) Ejemplo práctico: cómo almacenar hashes de contraseñas (esquema)

Tabla SQL sugerida:

```sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  username TEXT UNIQUE,
  password_hash TEXT,   -- formato que incluye algoritmo/params
  salt BYTEA,           -- opcional si no está dentro del hash
  algorithm TEXT,       -- "argon2id", "bcrypt", versión, etc.
  created_at TIMESTAMP DEFAULT now()
);
```

**Consejo**: guarda también algoritmo y parámetros para facilitar migraciones.

### 12) Migración de hashes antiguos (ej.: MD5 → Argon2)

Estrategia práctica:

1. Mantén soporte para leer hashes antiguos.
2. En el login: si la contraseña del usuario coincide con hash antiguo, **rehash** con el nuevo algoritmo y guarda el nuevo hash.
3. Forzar rehash a todos los usuarios gradualmente (por login o por política).

### 13) Ejemplos de comandos útiles

- Ver hash SHA-256 de un archivo (Linux/macOS):

  ```bash
  sha256sum archivo.zip
  ```

- Python, SHA-256:

  ```py
  import hashlib
  hashlib.sha256(b"hola").hexdigest()
  ```

### 14) Errores comunes y malas prácticas

- Usar MD5 o SHA-1 para seguridad → **inseguro**.
- Hashear contraseñas con SHA-256 sin sal ni KDF → vulnerable a GPU bruteforce.
- Reutilizar salts o usar salt estático/global.
- Manejar pepper en el mismo lugar que la base de datos (pierde su sentido).
- No versionar el esquema/algoritmo del hash.

### 15) Buenas prácticas resumidas (checklist)

- Usa **Argon2id** (o bcrypt/scrypt si no puedes Argon2).
- Sal único por usuario (≥ 16 bytes).
- Considera **pepper** para defensa en profundidad.
- Almacena algoritmo y parámetros con el hash.
- Rehash automático en login si parámetros/algoritmo cambian.
- Usa **HMAC** para autenticación de mensajes (no `hash(secret||msg)`).
- No inventes tu propio esquema criptográfico. Usa librerías probadas.

### 16) ¿Cuánto deberías saber sobre hashing?

- **Básico**: qué es un hash, por qué no almacenar contraseñas en texto plano.
- **Intermedio** (desarrollador): cómo calcular hashes en tu stack, cuándo usar HMAC, cómo almacenar hashes/ salts, y migrar hashes.
- **Avanzado** (seguridad): internals de SHA-2/SHA-3, ataques (length-extension, colisiones), parámetros de Argon2, y criptografía poscuántica para la planificación a largo plazo.

### 17) Mini-glosario rápido

- **Digest**: salida del hash.
- **Salt**: valor aleatorio por usuario para mezclar antes del hash.
- **Pepper**: secreto global fuera de BD.
- **KDF**: Key Derivation Function, p. ej. Argon2, PBKDF2.
- **HMAC**: Hash-based Message Authentication Code.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**             | **Siguiente 3**              |
| ------------------ | ----------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./11_1_Salting.md) | [⏩](./11_3_Key_Exchange.md) |
