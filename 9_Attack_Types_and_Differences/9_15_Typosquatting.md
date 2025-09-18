| **Inicio**         | **atrás 14**                    | **Siguiente 16**                              |
| ------------------ | ------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./9_14_Drive_by_Attack.md) | [⏩](./9_16_Brute_Force_vs_Password_Spray.md) |

---

## **Índice**

| Temario                                                 |
| ------------------------------------------------------- |
| [294. Que es Typosquatting?](#294-que-es-typosquatting) |

# **Typosquatting**

## **294. Que es Typosquatting?**

![Typosquatting](/img/9_Attack_Types_and_Differences/Typosquatting.avif "Typosquatting")

El **Typosquatting** (también llamado _URL hijacking_ o “secuestro de errores tipográficos”) es una técnica de ciberataque en la que los hackers **registran dominios web muy similares a los legítimos**, aprovechándose de errores comunes de escritura que cometen los usuarios al teclear direcciones en el navegador.

👉 Ejemplo:

- Sitio legítimo: `www.microsoft.com`
- Sitio malicioso (typosquatting): `www.micosoft.com` (falta la “r”)

El usuario, al equivocarse escribiendo, entra al sitio falso, creyendo que es el verdadero.

### 🎭 ¿Cómo utilizan los hackers el Typosquatting?

1. **Phishing con páginas falsas**

   - Clonan el sitio original (ejemplo: banca online).
   - Piden al usuario ingresar credenciales, que luego son robadas.

2. **Malware y descargas falsas**

   - El sitio alternativo contiene un enlace para descargar “software legítimo” que en realidad es malware.

3. **Publicidad engañosa (Ad Fraud)**

   - Muestran anuncios o redirigen a páginas de pago por clic para generar ingresos.

4. **Recolección de tráfico**

   - Aprovechan que muchos usuarios escriben mal para recibir visitas gratis y luego vender esos dominios.

### ⚠️ Los peligros del Typosquatting

- **Robo de credenciales**: contraseñas de bancos, correos, redes sociales.
- **Instalación de malware**: troyanos, spyware, ransomware.
- **Fraude financiero**: pagos en tiendas falsas.
- **Daño a la reputación empresarial**: clientes que creen que el sitio legítimo es inseguro.
- **Pérdida de datos personales**: direcciones, teléfonos, tarjetas de crédito.

### 📧 Phishing y Typosquatting

El **typosquatting** es una herramienta muy usada en ataques de **phishing**:

- Un atacante envía un correo con un enlace que parece legítimo:

  👉 Ejemplo: `www.faceb00k.com` (con ceros en lugar de “o”).

- El usuario hace clic y entra en la **página clonada**, donde ingresa sus datos.
- El atacante obtiene acceso a su cuenta real.

### 🛡️ Cómo protegerse del Typosquatting

#### ✅ Como usuario:

1. **Revisar la URL** antes de ingresar datos sensibles.

   - Ejemplo: comprobar que sea `www.paypal.com` y no `www.paypa1.com`.

2. **Escribir manualmente las direcciones** en lugar de seguir enlaces de correos o mensajes.
3. **Usar marcadores (favoritos)** para acceder siempre al mismo sitio legítimo.
4. **Activar HTTPS** y buscar el candado 🔒 en la barra de direcciones.
5. **Mantener un antivirus actualizado** con protección de navegación.
6. **No confiar en correos urgentes** que pidan actualizar cuentas desde un enlace.

#### ✅ Como empresa:

1. **Registrar dominios similares** (ejemplo: `micros0ft.com`, `microsoft.co`, etc.) para prevenir que sean usados por atacantes.
2. **Monitorear dominios sospechosos** que puedan confundir a clientes.
3. **Implementar autenticación multifactor (MFA)** para mitigar el robo de contraseñas.
4. **Educar a los empleados y clientes** sobre el typosquatting y el phishing.
5. **Usar DMARC, DKIM y SPF** para proteger correos y reducir phishing.

### 📌 Ejemplo real de Typosquatting

- En 2018, se detectó que los dominios **“goggle.com”** y **“googkle.com”** redirigían a páginas llenas de anuncios y malware.
- Millones de usuarios que intentaban entrar a Google terminaron expuestos a ataques.

✅ **Conclusión**:

El **Typosquatting** es una forma silenciosa de engañar a los usuarios aprovechando **errores humanos**. Al combinarse con **phishing**, se convierte en un riesgo crítico tanto para personas como para empresas.
La clave para prevenirlo está en:

- **Vigilancia digital**
- **Buenas prácticas de navegación**
- **Seguridad proactiva empresarial**

---

[🔼](#índice)

---

| **Inicio**         | **atrás 14**                    | **Siguiente 16**                              |
| ------------------ | ------------------------------- | --------------------------------------------- |
| [🏠](../README.md) | [⏪](./9_14_Drive_by_Attack.md) | [⏩](./9_16_Brute_Force_vs_Password_Spray.md) |
