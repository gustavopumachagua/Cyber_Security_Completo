| **Inicio**         | **atrás 9**                                         | **Siguiente 11**                |
| ------------------ | --------------------------------------------------- | ------------------------------- |
| [🏠](../README.md) | [⏪](./2_9_Installing_Software_and_Applications.md) | [⏩](./2_11_Troubleshooting.md) |

---

## **Índice**

| Temario                                      |
| -------------------------------------------- |
| [56. ¿Qué es CRUD?](#56-qué-es-crud)         |
| [57. Operaciones CRUD](#57-operaciones-crud) |

---

# **Realizar CRUD en archivos**

## **56. ¿Qué es CRUD?**

### 1. ¿Qué es CRUD?

**CRUD** son las siglas de las **cuatro operaciones básicas** que se realizan en bases de datos y sistemas que manejan información:

- **C → Create (Crear)**: insertar nuevos registros.
- **R → Read (Leer)**: consultar datos existentes.
- **U → Update (Actualizar)**: modificar datos existentes.
- **D → Delete (Eliminar)**: borrar datos.

👉 CRUD es la base de cualquier sistema de información, desde aplicaciones web hasta sistemas de escritorio.

Ejemplo en la vida real:

- Una tienda en línea permite **registrar un producto (Create)**, **ver productos (Read)**, **editar el precio (Update)** y **eliminar productos agotados (Delete)**.

### 2. ¿Cómo se realiza CRUD en una base de datos?

En SQL, cada operación tiene su propia instrucción:

- **Create → `INSERT`**
- **Read → `SELECT`**
- **Update → `UPDATE`**
- **Delete → `DELETE`**

Ejemplo con tabla `Clientes`:

```sql
CREATE TABLE Clientes (
    id INT PRIMARY KEY,
    nombre VARCHAR(50),
    correo VARCHAR(100),
    telefono VARCHAR(20)
);
```

### 3. Ejemplos de operaciones CRUD

#### ✅ **Create (INSERT)**

```sql
INSERT INTO Clientes (id, nombre, correo, telefono)
VALUES (1, 'Gustavo', 'gustavo@email.com', '999999999');
```

#### ✅ **Read (SELECT)**

```sql
SELECT * FROM Clientes;
SELECT nombre, correo FROM Clientes WHERE id = 1;
```

#### ✅ **Update (UPDATE)**

```sql
UPDATE Clientes
SET telefono = '988888888'
WHERE id = 1;
```

#### ✅ **Delete (DELETE)**

```sql
DELETE FROM Clientes WHERE id = 1;
```

### 🔹 4. Prueba de operaciones CRUD

Supongamos que tenemos esta tabla `Productos`:

```sql
CREATE TABLE Productos (
    id INT PRIMARY KEY,
    nombre VARCHAR(100),
    precio DECIMAL(10,2),
    stock INT
);
```

#### 1. Insertar productos

```sql
INSERT INTO Productos VALUES (1, 'Laptop HP', 2500.00, 10);
INSERT INTO Productos VALUES (2, 'Mouse Logitech', 80.00, 50);
```

#### 🔸 2. Consultar productos

```sql
SELECT * FROM Productos;
```

Resultado esperado:

```
id | nombre         | precio  | stock
------------------------------------
1  | Laptop HP      | 2500.00 | 10
2  | Mouse Logitech |   80.00 | 50
```

#### 3. Actualizar stock

```sql
UPDATE Productos
SET stock = stock - 1
WHERE id = 1;
```

👉 El stock de la Laptop HP baja a **9**.

#### 4. Eliminar producto

```sql
DELETE FROM Productos WHERE id = 2;
```

👉 El mouse ya no aparecerá en la tabla.

### 5. CRUD y rendimiento de la base de datos

El **rendimiento** depende de cómo implementes CRUD:

- **Create**: si insertas muchos datos, conviene usar **transacciones** y **batch inserts**.
- **Read**: usar **índices**, **cláusulas WHERE** y **LIMIT** para mejorar consultas.
- **Update**: evita actualizaciones masivas sin condiciones (`WHERE`).
- **Delete**: en tablas grandes, usa **borrado lógico** (ejemplo: un campo `activo = 0`) en lugar de borrar registros físicos para no fragmentar la base de datos.

Ejemplo de borrado lógico:

```sql
ALTER TABLE Productos ADD activo BIT DEFAULT 1;

UPDATE Productos SET activo = 0 WHERE id = 2;
```

👉 El producto sigue en la tabla, pero marcado como inactivo.

### ✅ Resumen

- CRUD = **Create, Read, Update, Delete**.
- Se implementa en SQL con **INSERT, SELECT, UPDATE, DELETE**.
- Ejemplo: una tienda en línea usa CRUD para manejar clientes y productos.
- El rendimiento mejora con índices, transacciones y borrado lógico.

---

[🔼](#índice)

---

## **57. Operaciones CRUD**

### 1. ¿Qué es CRUD?

**CRUD** es un acrónimo que describe las **cuatro operaciones básicas** que se pueden hacer con los datos en una base de datos o sistema de información:

- **C → Create (Crear)**: agregar datos nuevos.
- **R → Read (Leer)**: consultar datos existentes.
- **U → Update (Actualizar)**: modificar datos existentes.
- **D → Delete (Eliminar)**: borrar datos.

👉 Es la base de cualquier aplicación moderna: redes sociales, e-commerce, banca digital, etc.

Ejemplo real:
En **Facebook**:

- Cuando registras una cuenta → **Create**.
- Cuando ves tu muro o perfil → **Read**.
- Cuando editas tu foto de perfil → **Update**.
- Cuando borras una publicación → **Delete**.

### 2. ¿Qué es el **CREATE**, funcionamiento y cómo funciona?

El **CREATE** significa **crear un nuevo registro** en una base de datos.

- Funciona usando el comando **`INSERT`** en SQL.
- Se agrega una nueva fila en una tabla con los valores que indiquemos.

📌 Ejemplo en SQL:

```sql
INSERT INTO Usuarios (id, nombre, correo)
VALUES (1, 'Gustavo', 'gustavo@email.com');
```

👉 Aquí estamos **creando** un usuario nuevo en la tabla `Usuarios`.

Ejemplo real:
Cuando te registras en una aplicación y pones tu nombre, correo y contraseña → esos datos se guardan en la base de datos con **CREATE**.

### 3. ¿Qué es el **READ**, funcionamiento y cómo funciona?

El **READ** significa **leer datos ya existentes**.

- Funciona con el comando **`SELECT`** en SQL.
- Permite obtener información almacenada, filtrando si es necesario.

📌 Ejemplo en SQL:

```sql
SELECT nombre, correo FROM Usuarios WHERE id = 1;
```

👉 Aquí estamos **leyendo** los datos del usuario con `id = 1`.

Ejemplo real:
Cuando inicias sesión en Gmail y este te muestra tu bandeja de entrada → el sistema usa **READ** para leer tus correos desde la base de datos.

### 4. ¿Qué es el **UPDATE**, funcionamiento y cómo funciona?

El **UPDATE** significa **actualizar/modificar datos existentes**.

- Funciona con el comando **`UPDATE`** en SQL.
- Permite cambiar valores de registros ya creados.

📌 Ejemplo en SQL:

```sql
UPDATE Usuarios
SET correo = 'nuevoemail@email.com'
WHERE id = 1;
```

👉 Aquí estamos **actualizando** el correo del usuario con `id = 1`.

Ejemplo real:
Cuando cambias tu foto de perfil en Instagram o tu número de teléfono en WhatsApp → la aplicación ejecuta un **UPDATE** en la base de datos.

### 5. ¿Qué es el **DELETE**, funcionamiento y cómo funciona?

El **DELETE** significa **eliminar datos existentes**.

- Funciona con el comando **`DELETE`** en SQL.
- Borra un registro de la tabla según la condición indicada.

📌 Ejemplo en SQL:

```sql
DELETE FROM Usuarios WHERE id = 1;
```

👉 Aquí estamos **eliminando** al usuario con `id = 1`.

Ejemplo real:
Cuando borras un contacto en tu teléfono o una publicación en Twitter/X → la aplicación ejecuta un **DELETE** en la base de datos.

### 6. Conclusión

- **CRUD (Create, Read, Update, Delete)** es el **pilar fundamental de la gestión de datos**.
- Cada acción que hacemos en una app (registrarse, ver info, modificar, borrar) corresponde a una de estas operaciones.
- En SQL se implementa con:

  - `INSERT` → Create
  - `SELECT` → Read
  - `UPDATE` → Update
  - `DELETE` → Delete

- Comprender CRUD es esencial para programadores, administradores de bases de datos y desarrolladores web, ya que es la base de cualquier aplicación de software.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 9**                                         | **Siguiente 11**                |
| ------------------ | --------------------------------------------------- | ------------------------------- |
| [🏠](../README.md) | [⏪](./2_9_Installing_Software_and_Applications.md) | [⏩](./2_11_Troubleshooting.md) |
