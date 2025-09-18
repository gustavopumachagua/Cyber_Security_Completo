| **Inicio**         | **Siguiente 2**         |
| ------------------ | ----------------------- |
| [🏠](../README.md) | [⏩](./11_2_Hashing.md) |

---

## **Índice**

| Temario                                     |
| ------------------------------------------- |
| [332. Que es salting?](#332-que-es-salting) |

# **Salting**

## **332. Que es salting?**

![Salting](/img/11_Basics_of_Cryptography/Salting.webp "Salting")

### 🔑 ¿Qué es el salado de contraseñas?

El **salado de contraseñas (salting)** es una técnica de seguridad que consiste en **añadir datos aleatorios únicos (llamados _sal_) a una contraseña antes de aplicarle una función hash**.

- El _sal_ evita que contraseñas iguales tengan el mismo hash.
- Cada usuario debería tener un _sal_ único y almacenarse junto con el hash.

Ejemplo sencillo:

- Contraseña: `123456`
- Hash sin sal (SHA-256):

  ```
  8d969eef6ecad3c29a3a629280e686cf...
  ```

- Si 10 usuarios tienen `123456`, todos tendrán el mismo hash.
- Con **sal**:

  ```
  Sal = "X9f$2a"
  Hash( "X9f$2a123456" ) → distinto al de otro usuario
  ```

### 🔒 Hashing de contraseñas y por qué es necesario el uso de sal

El **hashing** transforma la contraseña en un valor irreversible mediante algoritmos como **bcrypt, scrypt, Argon2** o incluso SHA-256 (aunque este último ya no es seguro solo).

👉 Problema: si dos usuarios tienen la misma contraseña, el hash será idéntico → vulnerable a ataques con **tablas arcoíris** o comparaciones directas.

👉 Solución: el _sal_ rompe esta uniformidad, haciendo que cada hash sea único incluso con contraseñas iguales.

### ⚙️ ¿Cómo funciona el salado de contraseñas?

1. El sistema genera un valor aleatorio (sal) para cada usuario.
2. Antes de hacer el hash de la contraseña, concatena:

   ```
   Hash = Algoritmo( Sal + Contraseña )
   ```

3. Se almacena en la base de datos:

   - **Hash**
   - **Sal** (no es secreto, se guarda junto al hash).

4. Al autenticar, se toma la contraseña ingresada, se concatena con el _sal_ almacenado y se vuelve a hashear → si coincide, acceso permitido.

### 🛡️ ¿Qué ataques puede prevenir o minimizar el salting de contraseñas?

El salting no evita todos los ataques, pero **sí complica enormemente los siguientes**:

1. **Tablas arcoíris (rainbow tables)** → bases precomputadas de contraseñas+hash.

   - Con sal, estas tablas se vuelven inútiles porque cada hash es único.

2. **Ataques de diccionario** → listas de contraseñas comunes.

   - Se vuelven más costosos porque hay que aplicar el diccionario para cada usuario (por su sal única).

3. **Ataques de hash idéntico** → detectar usuarios con la misma contraseña.

   - Con sal, cada hash luce diferente.

⚠️ Importante: el sal no protege contra **fuerza bruta pura** (probar todas las contraseñas posibles). Para eso se usan algoritmos lentos como **bcrypt, Argon2 o scrypt**.

### 💡 Consejos para aprovechar al máximo el uso de la sal de contraseñas

1. **Usar una sal única y aleatoria para cada contraseña** (mínimo 16 bytes).
2. **No usar sal global o fija** para toda la base de datos.
3. **Guardar la sal junto al hash** en la base de datos, no es un secreto.
4. **Combinar sal con algoritmos resistentes** (bcrypt, Argon2, scrypt).
5. **Agregar un "pepper" opcional**: un secreto global guardado fuera de la base de datos (por ejemplo, en un HSM o variable de entorno). Esto añade otra capa.
6. **Actualizar hashes inseguros**: si se usó MD5 o SHA-1, migrar a bcrypt/Argon2 con sal.

📌 Ejemplo práctico con bcrypt (Node.js):

```js
const bcrypt = require("bcrypt");

// Contraseña original
const password = "MiClaveSegura123!";

// Generar sal y hash
const saltRounds = 12; // factor de costo
const hash = await bcrypt.hash(password, saltRounds);

console.log(hash);
// $2b$12$9uIvSx6QWAvM6JGjT7ePiOKHkqA2iXfxO9tOnF4uvJFo9Uty6YHbC
```

👉 Aquí `bcrypt` internamente genera una sal aleatoria única y la incluye dentro del hash.

---

[🔼](#índice)

---

| **Inicio**         | **Siguiente 2**         |
| ------------------ | ----------------------- |
| [🏠](../README.md) | [⏩](./11_2_Hashing.md) |
