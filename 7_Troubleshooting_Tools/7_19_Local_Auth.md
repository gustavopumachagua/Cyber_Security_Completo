| **Inicio**         | **atrás 18**                 |
| ------------------ | ---------------------------- |
| [🏠](../README.md) | [⏪](./7_18_Certificates.md) |

---

## **Índice**

| Temario                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------------------- |
| [196. Autenticación local, registro y otras configuraciones](#196-autenticación-local-registro-y-otras-configuraciones) |

# **Local Auth**

## **196. Autenticación local, registro y otras configuraciones**

![Local Auth](/img/7_Troubleshooting_Tools/Local_Auth.png "Local Auth")

### 🔹 1. Autenticación local, registro y configuraciones

La **autenticación local** significa que el sistema **guarda y valida las credenciales en su propia base de datos**, en lugar de delegar en un proveedor externo (Google, Facebook, Azure AD, etc.).

- **Autenticación local:** el usuario ingresa usuario/contraseña → el servidor valida contra su base de datos (ej. `users` en MySQL, PostgreSQL o MongoDB).
- **Registro:** el usuario crea una nueva cuenta proporcionando correo, nombre y contraseña. La contraseña se almacena cifrada (_hashing_ con algoritmos como bcrypt, scrypt o Argon2).
- **Otras configuraciones:** el usuario puede cambiar datos personales, contraseña, foto de perfil, preferencias, etc.

👉 Ejemplo (tabla `users` en base de datos):

| id  | email                                       | password_hash                       | name       | created_at |
| --- | ------------------------------------------- | ----------------------------------- | ---------- | ---------- |
| 1   | [juan@ejemplo.com](mailto:juan@ejemplo.com) | `$2b$12$7dDF1xOjQ...` (bcrypt hash) | Juan Pérez | 2025-09-05 |

### 🔹 2. Requisitos

Antes de implementar la autenticación local, el sistema debe cumplir con:

- ✅ **Base de datos segura** para almacenar usuarios.
- ✅ **Hash de contraseñas** (nunca almacenar en texto plano).
- ✅ **Validaciones de entrada** (correos válidos, contraseñas seguras).
- ✅ **Tokens de sesión o JWT** para mantener la sesión activa.
- ✅ **Opcional:** verificación de email y/o captcha para evitar cuentas falsas.

👉 Ejemplo en Node.js (bcrypt):

```js
const bcrypt = require("bcrypt");

const hashPassword = async (password) => {
  const salt = await bcrypt.genSalt(12);
  return await bcrypt.hash(password, salt);
};

const validatePassword = async (password, hash) => {
  return await bcrypt.compare(password, hash);
};
```

### 🔹 3. Autenticar y registrar

- **Registro:** el usuario llena un formulario con correo y contraseña → el servidor guarda el correo y el hash de la contraseña en la base de datos.
- **Autenticación:** el usuario ingresa credenciales → el servidor busca el correo y compara la contraseña ingresada con el hash almacenado.
- **Resultado:** si coincide, se crea una sesión (cookie segura, token JWT o session ID).

👉 Ejemplo de flujo:

1. Usuario: `POST /register` → { email: "[juan@ejemplo.com](mailto:juan@ejemplo.com)", password: "123456" }
2. Servidor: guarda `email` + `bcrypt(password)`.
3. Usuario: `POST /login` → { email, password }.
4. Servidor: valida con bcrypt, si es correcto → genera un token JWT.

Ejemplo de JWT emitido:

```json
{
  "sub": "1",
  "email": "juan@ejemplo.com",
  "iat": 1736167200,
  "exp": 1736170800
}
```

### 🔹 4. Restablecer una contraseña

Cuando un usuario olvida su contraseña, el sistema debe permitir restablecerla de forma segura:

1. Usuario hace clic en **"Olvidé mi contraseña"**.
2. El servidor genera un **token único y temporal** (ej. válido 15 min).
3. Envía un correo con un enlace:
   `https://app.com/reset-password?token=XYZ`
4. El usuario ingresa nueva contraseña → el servidor valida el token → guarda la nueva contraseña (hash).

👉 Ejemplo de token en DB:

| user_id | reset_token | expires_at       |
| ------- | ----------- | ---------------- |
| 1       | `abc123xyz` | 2025-09-05 14:30 |

### 🔹 5. Canjear una invitación

En sistemas corporativos o colaborativos, en lugar de registro libre, los usuarios reciben **invitaciones**:

1. Admin envía invitación a `nuevo@ejemplo.com`.
2. El sistema genera un **link único** (ej. válido 7 días).
3. El usuario hace clic en el enlace → redirige a página de creación de cuenta.
4. El invitado elige contraseña, completa perfil y queda registrado.

👉 Ejemplo de enlace de invitación:
`https://app.com/invite/abcd-1234-efgh-5678`

Esto asegura que **solo usuarios autorizados** puedan registrarse.

### 🔹 6. Administrar cuentas de usuario a través de páginas de perfil

Cada usuario debe poder acceder a una **página de perfil** donde administre su cuenta:

- Cambiar nombre, correo, contraseña.
- Subir foto de perfil.
- Configurar preferencias (idioma, notificaciones, temas).
- Eliminar cuenta (en cumplimiento con GDPR/leyes de datos).
- Ver actividad reciente (últimos inicios de sesión).

👉 Ejemplo en frontend (React):

```jsx
function ProfilePage({ user }) {
  return (
    <div>
      <h2>Perfil de {user.name}</h2>
      <p>Email: {user.email}</p>
      <button onClick={logout}>Cerrar sesión</button>
    </div>
  );
}
```

### 🔹 Reflexión final

- La **autenticación local** es la base de muchos sistemas → sencilla de implementar pero requiere **buenas prácticas de seguridad** (hash, tokens, HTTPS, MFA).
- El **registro** debe balancear facilidad (para usuarios) con protección contra abusos (spam, bots).
- **Restablecer contraseñas e invitaciones** son parte fundamental de la experiencia de usuario.
- Las **páginas de perfil** permiten autogestión y reducen carga al soporte técnico.
- En sistemas modernos, suele combinarse **autenticación local + federada (SSO, OAuth, LDAP)** para más flexibilidad.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 18**                 |
| ------------------ | ---------------------------- |
| [🏠](../README.md) | [⏪](./7_18_Certificates.md) |
