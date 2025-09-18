| **Inicio**         | **atrás 15**           | **Siguiente 17**    |
| ------------------ | ---------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./7_15_RADIUS.md) | [⏩](./7_17_SSO.md) |

---

## **Índice**

| Temario                                                                |
| ---------------------------------------------------------------------- |
| [190. ¿Qué es LDAP y cómo funciona?](#190-qué-es-ldap-y-cómo-funciona) |
| [191. ¿Qué es LDAP?](#191-qué-es-ldap)                                 |

# **LDAP**

## **190. ¿Qué es LDAP y cómo funciona?**

![LDAP](/img/7_Troubleshooting_Tools/LDAP.png "LDAP")

### 🔹 1. ¿Qué es LDAP?

**LDAP (Lightweight Directory Access Protocol)** es un **protocolo de aplicación** que se utiliza para acceder, consultar y modificar servicios de directorio en red.

👉 Un **directorio** es una base de datos jerárquica optimizada para **lecturas rápidas** (más que para escrituras) que almacena información sobre:

- Usuarios (nombre, contraseña, correo, grupos).
- Dispositivos (computadoras, impresoras, routers).
- Recursos de red (aplicaciones, permisos).

📌 **Ejemplo práctico**:

En una empresa, en lugar de que cada aplicación guarde sus propios usuarios y contraseñas, todos consultan un **directorio LDAP** centralizado para validar credenciales y recuperar atributos del usuario.

### 🔹 2. El proceso LDAP explicado

Un flujo típico de **autenticación con LDAP** funciona así:

1. **Conexión inicial (Bind anónimo o autenticado)**

   - El cliente se conecta al servidor LDAP (ej. `ldap://servidor:389` o `ldaps://servidor:636`).
   - Puede hacerlo anónimamente o con credenciales de servicio.

2. **Búsqueda del usuario**

   - El cliente busca el **DN (Distinguished Name)** del usuario.
   - Ejemplo: `uid=juan,ou=empleados,dc=empresa,dc=com`.

3. **Autenticación (Bind con DN + contraseña)**

   - El cliente intenta "bindearse" con el DN y la contraseña del usuario.
   - Si es correcto → autenticación exitosa.

4. **Consulta de atributos**

   - Una vez autenticado, el cliente puede solicitar atributos: email, grupos, permisos.

📌 **Ejemplo con comandos**:

```bash
# Buscar un usuario
ldapsearch -x -H ldap://ldap.empresa.com -b "dc=empresa,dc=com" "(uid=juan)"

# Autenticar usuario
ldapwhoami -x -D "uid=juan,ou=empleados,dc=empresa,dc=com" -W
```

### 🔹 3. Términos LDAP que debes comprender

| Término                               | Explicación                                                                           |
| ------------------------------------- | ------------------------------------------------------------------------------------- |
| **DN (Distinguished Name)**           | Identificador único de una entrada. Ej: `cn=Maria Perez,ou=ventas,dc=empresa,dc=com`. |
| **RDN (Relative Distinguished Name)** | La parte relativa del DN. Ej: `cn=Maria Perez`.                                       |
| **DC (Domain Component)**             | Parte de un dominio. Ej: `dc=empresa,dc=com`.                                         |
| **OU (Organizational Unit)**          | Contenedor de objetos. Ej: `ou=empleados`.                                            |
| **CN (Common Name)**                  | Nombre común de un objeto. Ej: `cn=ServidorWeb01`.                                    |
| **Schema**                            | Define qué atributos y tipos de objetos se pueden almacenar.                          |
| **Entry**                             | Cada registro en LDAP (usuario, grupo, dispositivo).                                  |
| **Attribute**                         | Información asociada a una entrada (ej: `mail`, `uid`, `memberOf`).                   |

### 🔹 4. LDAP frente a Active Directory

| Característica | **LDAP**                                                                      | **Active Directory (AD)**                                     |
| -------------- | ----------------------------------------------------------------------------- | ------------------------------------------------------------- |
| Definición     | Protocolo de acceso a directorios.                                            | Servicio de directorio desarrollado por Microsoft.            |
| Rol            | Medio de comunicación.                                                        | Implementación que usa LDAP (y otros protocolos).             |
| Plataforma     | Estándar abierto, multiplataforma (OpenLDAP, 389 Directory Server, ApacheDS). | Exclusivo de Windows Server.                                  |
| Autenticación  | Solo protocolo LDAP, aunque puede usar SASL/TLS.                              | Usa LDAP + Kerberos + NTLM.                                   |
| Estructura     | Jerárquica, definida en el schema.                                            | Jerárquica + funciones específicas (dominios, bosques, GPOs). |
| Ejemplo        | OpenLDAP en Linux.                                                            | Active Directory en Windows Server.                           |

📌 **Resumen**:

LDAP es el **lenguaje/protocolo**, y Active Directory es una **implementación** que lo usa junto con Kerberos para autenticación.

### 🔹 5. LDAP + Okta

**Okta** es un proveedor de **Identity as a Service (IDaaS)** que ofrece autenticación y gestión de identidades en la nube.

👉 Muchas empresas tienen **aplicaciones antiguas** que dependen de LDAP (bases de datos, servidores, intranets).

👉 Okta ofrece un **LDAP Interface** que actúa como **proxy LDAP**:

- Permite que esas aplicaciones sigan hablando "LDAP" como si existiera un servidor LDAP tradicional.
- Internamente, Okta traduce esas solicitudes a su servicio de identidades en la nube.
- Así, se logra **Single Sign-On (SSO)** y **autenticación centralizada** con MFA y políticas modernas.

📌 **Ejemplo**:

- Una aplicación legacy pide autenticación a `ldap.okta.com`.
- El usuario ingresa sus credenciales.
- Okta valida contra su base en la nube (con políticas de seguridad, MFA, etc.).
- La app piensa que habló con un LDAP clásico, pero en realidad fue gestionado por Okta.

### ✅ Resumen final

- **LDAP** es un **protocolo estándar** para acceder a directorios que almacenan usuarios, grupos y recursos de red.
- El proceso LDAP sigue un flujo: **bind → búsqueda → autenticación → consulta de atributos**.
- Conceptos clave: **DN, OU, CN, DC, entries, atributos**.
- **Active Directory** usa LDAP, pero no es lo mismo (AD = servicio, LDAP = protocolo).
- **Okta + LDAP** permite integrar aplicaciones legacy que requieren LDAP con una plataforma moderna en la nube.

---

[🔼](#índice)

---

## **191. ¿Qué es LDAP?**

**Respuesta corta:**

LDAP (Lightweight Directory Access Protocol) es un protocolo estándar para **consultar y modificar** servicios de directorio. Un _directorio_ es una base de datos optimizada para lecturas rápidas y búsquedas jerárquicas (usuarios, grupos, equipos, impresoras, políticas). LDAP define cómo pedir/recuperar esa información en red.

### 1. ¿Por qué importa LDAP?

- Centraliza identidades y atributos (nombre, email, uid, groups).
- Muchas aplicaciones/servicios usan LDAP para autenticar usuarios o consultar atributos (correo, grupo, número de teléfono).
- Es muy usado en infraestructuras corporativas: OpenLDAP, 389 Directory Server, ApacheDS, y como protocolo de consulta en **Active Directory** (AD implementa LDAP además de Kerberos y otros servicios).

### 2. Modelo de datos (cómo está organizada la información)

- **Entry**: unidad básica; representada por un **DN (Distinguished Name)**, p. ej.
  `uid=juan,ou=people,dc=example,dc=com`
- **RDN (Relative Distinguished Name)**: la parte del DN que identifica la entrada dentro de su contenedor, p. ej. `uid=juan`.
- **DN**: cadena completa que sitúa la entrada en la jerarquía (similar a una ruta de archivo).
- **Atributos**: pares `nombre: valor` (ej. `mail: juan@example.com`).
- **objectClass**: define qué atributos puede/ debe tener una entrada (`inetOrgPerson`, `posixAccount`, `organizationalUnit`, ...).
- **Schema**: conjunto de reglas que definen atributos y objectClasses permitidos.

Ejemplo (LDIF — formato de intercambio):

```ldif
dn: uid=juan,ou=people,dc=example,dc=com
objectClass: inetOrgPerson
objectClass: posixAccount
cn: Juan Pérez
sn: Pérez
uid: juan
uidNumber: 1001
gidNumber: 1001
homeDirectory: /home/juan
mail: juan@example.com
userPassword: {SSHA}...hashed...
```

### 3. Operaciones LDAP principales (RFCs relevantes)

- **BIND** — autenticación (simple o SASL).
- **SEARCH** — buscar entradas (base DN, scope, filtro).
- **COMPARE** — comparar un valor de atributo.
- **ADD / MODIFY / DELETE** — gestionar entradas.
- **MODIFY DN (RENAME)** — mover o renombrar entradas.
- **UNBIND** — cerrar sesión.
- **Extended ops** (p. ej. StartTLS).

### 4. Filtros y búsquedas (ejemplos prácticos)

#### Filtros LDAP (sintaxis)

- `(cn=Juan Pérez)` — igualdad.
- `(mail=*@example.com)` — comodín.
- `(&(objectClass=person)(|(sn=García)(sn=Pérez)))` — AND/OR combinados.
- `(!(status=inactive))` — NOT.

#### Ejemplos con `ldapsearch` (OpenLDAP client)

1. **Listado público (bind anónimo)**:

```bash
ldapsearch -x -H ldap://ldap.example.com -b "dc=example,dc=com" "(objectClass=inetOrgPerson)"
```

2. **Buscar por uid**:

```bash
ldapsearch -x -H ldap://ldap.example.com -b "dc=example,dc=com" "(uid=juan)"
```

3. **Bind autenticado (probar credenciales)**:

```bash
ldapwhoami -x -D "uid=juan,ou=people,dc=example,dc=com" -W
# pedirá contraseña; salida 'dn: uid=juan,...' si éxito
```

4. **Autenticación de una aplicación (service account bind)**:

```bash
ldapsearch -x -D "cn=svc-app,ou=services,dc=example,dc=com" -W -b "dc=example,dc=com" "(uid=*)"
```

5. **Uso de StartTLS (elevar a TLS en puerto 389)**:

```bash
ldapsearch -H ldap://ldap.example.com -ZZ -x -b "dc=example,dc=com" "(objectClass=*)"
```

### 5. LDAP URLs

Formato: `ldap://host:port/baseDN?attributes?scope?filter`
Ejemplo:

```
ldap://ldap.example.com:389/dc=example,dc=com?uid,mail?sub?(uid=juan)
```

### 6. Seguridad: cómo proteger LDAP

- **LDAPS** (LDAP sobre TLS) puerto **636** o **StartTLS** en 389 -> cifrado del canal.
- **SASL** para métodos de autenticación segura (GSSAPI/Kerberos, DIGEST-MD5, EXTERNAL).
- **Desactivar bind anónimo** o limitarlo.
- **ACLs** en el servidor para controlar qué DN puede ver/editar qué atributos.
- **Hashear contraseñas** (SSHA, bcrypt) en `userPassword`.
- **Auditoría** y logs centralizados.
- **Firewall**: limitar acceso a puertos LDAP a hosts autorizados.

### 7. Integración con aplicaciones (ejemplos de uso)

- **Autenticación**: aplicaciones (JIRA, GitLab, Postfix/Dovecot, Samba, PAM) hacen **bind** y luego `search` para validar credenciales o recuperar atributos.
- **Directorio de contactos**: directorio global de la empresa accesible por Outlook o clientes de correo.
- **SSO complementario**: LDAP + Kerberos (AD) para SSO: LDAP para atributos, Kerberos para autenticación fuerte.
- **Provisioning**: herramientas de gestión crean/actualizan cuentas en LDAP (SCIM, scripts con `ldapadd`).

### 8. Ejemplos de modificación (LDIF + comandos)

#### Añadir una entrada (archivo `add-user.ldif`)

```ldif
dn: uid=maria,ou=people,dc=example,dc=com
objectClass: inetOrgPerson
cn: María López
sn: López
uid: maria
mail: maria@example.com
userPassword: {SSHA}...hashed...
```

Comando:

```bash
ldapadd -x -D "cn=admin,dc=example,dc=com" -W -f add-user.ldif
```

#### Modificar (ldapmodify)

Archivo `modify-mail.ldif`:

```ldif
dn: uid=maria,ou=people,dc=example,dc=com
changetype: modify
replace: mail
mail: maria.lopez@example.com
```

Comando:

```bash
ldapmodify -x -D "cn=admin,dc=example,dc=com" -W -f modify-mail.ldif
```

#### Borrar entrada:

```bash
ldapdelete -x -D "cn=admin,dc=example,dc=com" -W "uid=maria,ou=people,dc=example,dc=com"
```

### 9. Buenas prácticas operativas

- Mantén **índices** en atributos usados en búsquedas frecuentes (`uid`, `mail`) para rendimiento.
- Planifica **replicación** y alta disponibilidad (multi-master o master-slave según servidor).
- **Backups LDIF** regulares y verificables.
- Usa **cuentas de servicio** limitadas para binds de aplicaciones.
- Evita modificar schema a menos que sea necesario; documenta extensiones.
- Control de retención y protección de atributos sensibles (PII).

### 10. Diferencias breves entre **LDAP** y **Active Directory (AD)**

- LDAP = **protocolo**.
- AD = **servicio de directorio** de Microsoft que **implementa LDAP** (además de Kerberos, DNS integrado, GPOs).
- AD tiene conceptos adicionales (dominios/forest, controladores de dominio, SPNs) y extensiones propietarias (atributos específicos, MS-AD schema).

### 11. Ejemplo rápido en Python (ldap3)

```python
from ldap3 import Server, Connection, ALL, SUBTREE

srv = Server('ldap.example.com', get_info=ALL)
conn = Connection(srv, user='cn=svc-app,dc=example,dc=com', password='secret', auto_bind=True)

conn.search('dc=example,dc=com', '(uid=juan)', SUBTREE, attributes=['cn','mail'])
for entry in conn.entries:
    print(entry.cn, entry.mail)

conn.unbind()
```

### 12. Errores comunes y su significado

- `Invalid Credentials (49)` — DN o contraseña incorrectos.
- `No Such Object (32)` — base DN o filtro apunta a un DN inexistente.
- `Insufficient Access (50)` — ACLs niegan la operación.
- `Time Limit Exceeded (3)` / `Size Limit Exceeded (4)` — límites de la búsqueda.

### 13. Resumen / cuándo usar LDAP

Usa LDAP cuando necesites:

- Un **almacén centralizado** de identidades y atributos.
- Que varias aplicaciones **lean** datos de usuario desde un origen único.
- Integrar con sistemas de autenticación/SSO en infraestructuras internas.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 15**           | **Siguiente 17**    |
| ------------------ | ---------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./7_15_RADIUS.md) | [⏩](./7_17_SSO.md) |
