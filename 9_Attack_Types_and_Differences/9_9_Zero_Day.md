| **Inicio**         | **atrás 8**                    | **Siguiente 10**                   |
| ------------------ | ------------------------------ | ---------------------------------- |
| [🏠](../README.md) | [⏪](./9_8_Dumpster_Diving.md) | [⏩](./9_10_Social_Engineering.md) |

---

## **Índice**

| Temario                                                                      |
| ---------------------------------------------------------------------------- |
| [283. ¿Qué es un ataque de día cero?](#283-qué-es-un-ataque-de-día-cero)     |
| [284. ¿Qué es una amenaza de día cero?](#284-qué-es-una-amenaza-de-día-cero) |

# **Día Cero**

## **283. ¿Qué es un ataque de día cero?**

![Día Cero](/img/9_Attack_Types_and_Differences/Dia_Cero.jpg "Día Cero")

Un **ataque de día cero (Zero-Day Attack)** es un ciberataque que aprovecha una **vulnerabilidad desconocida en software, hardware o firmware**, antes de que el fabricante la detecte y publique una corrección.

👉 “Día cero” significa que el proveedor tiene **cero días** para reaccionar porque el fallo se está explotando **antes de que exista un parche de seguridad**.

### 📖 Significado y definición de día cero

- **Zero-Day Vulnerability**: el fallo o debilidad aún no descubierto públicamente.
- **Zero-Day Exploit**: el método o código que aprovecha la vulnerabilidad.
- **Zero-Day Attack**: el ataque real contra víctimas utilizando ese exploit.

Ejemplo sencillo:

Si Windows tiene un error oculto que permite a un hacker entrar sin contraseña, y Microsoft aún no lo sabe, ese error es una vulnerabilidad de día cero.

### ⚙️ ¿Qué son los ataques de día cero y cómo funcionan?

1. **Descubrimiento de la vulnerabilidad**

   - Un atacante, investigador o grupo de hackers detecta un fallo.

2. **Creación del exploit**

   - Desarrollan un malware, script o técnica para aprovechar el error.

3. **Ataque**

   - Llevan a cabo campañas contra empresas, gobiernos o usuarios antes de que exista un parche.

4. **Reacción**

   - Una vez detectado, los proveedores deben crear actualizaciones urgentes para corregirlo.

### 🎭 ¿Quién lleva a cabo ataques de día cero?

- **Ciberdelincuentes**: buscan dinero (ransomware, robo de datos).
- **Hacktivistas**: motivados políticamente o ideológicamente.
- **Grupos APT (Advanced Persistent Threats)**: patrocinados por estados, hacen espionaje cibernético.
- **Investigadores de seguridad** (con fines éticos): reportan fallos a fabricantes, a veces cobran recompensas (_bug bounty_).

### 🎯 ¿Quiénes son los objetivos de los exploits de día cero?

- **Gobiernos y agencias militares** → espionaje o sabotaje.
- **Corporaciones grandes** → robo de datos, propiedad intelectual.
- **Bancos y financieras** → fraude económico.
- **Usuarios comunes** → a menudo como “daño colateral” cuando el malware se distribuye masivamente.

### 🔍 Cómo identificar ataques de día cero

Son muy difíciles de detectar porque:

- No hay firma conocida para los antivirus.
- No existe parche de seguridad aún.

Sin embargo, pueden identificarse con:

- **Sistemas de detección basados en comportamiento** (EDR/XDR).
- **Análisis de anomalías en la red** (actividad sospechosa).
- **Threat intelligence** (informes de ataques emergentes).

👉 Ejemplo: si un archivo desconocido comienza a cifrar carpetas, aunque el antivirus no lo reconozca, un EDR puede bloquearlo por comportamiento anómalo.

### 📌 Ejemplos de ataques de día cero

1. **Stuxnet (2010)**

   Malware usado para dañar plantas nucleares en Irán, explotaba múltiples vulnerabilidades de día cero en Windows.

2. **Ataque a Sony Pictures (2014)**

   Hackers usaron exploits de día cero para filtrar correos y películas inéditas.

3. **Zoom (2020)**

   Durante la pandemia se descubrieron vulnerabilidades de día cero que permitían a atacantes acceder a cámaras y micrófonos.

4. **Windows PrintNightmare (2021)**

   Fallo en el servicio de impresión de Windows que permitía escalamiento de privilegios.

### 🛡️ Cómo protegerse contra ataques de día cero

Aunque no se pueden evitar al 100%, se pueden mitigar riesgos:

✅ **Para usuarios**

- Mantener **sistemas y aplicaciones siempre actualizados**.
- Usar **antivirus con detección de comportamiento**.
- Evitar descargas de fuentes no confiables.
- Desconfiar de correos y enlaces sospechosos.

✅ **Para empresas**

- Implementar **EDR/XDR** para detección de amenazas avanzadas.
- Aplicar el principio de **mínimo privilegio** en accesos.
- Monitorear logs y tráfico de red con **SIEM**.
- Segmentar la red para contener intrusiones.
- Mantener planes de respuesta a incidentes y copias de seguridad.

### 📌 Resumen

- Un **ataque de día cero** aprovecha una vulnerabilidad **aún desconocida y sin parche**.
- Es muy peligroso porque ni fabricantes ni usuarios tienen forma inmediata de defenderse.
- Sus principales atacantes son ciberdelincuentes, gobiernos y grupos APT.
- Los objetivos suelen ser gobiernos, corporaciones, bancos y usuarios.
- La mejor defensa es la **ciberhigiene constante, sistemas de seguridad avanzados y actualizaciones regulares**.

---

[🔼](#índice)

---

## **284. ¿Qué es una amenaza de día cero?**

**Definición simple:** una **amenaza de día cero (zero-day)** es cualquier **ataque que explota una vulnerabilidad desconocida** para el fabricante o para la comunidad de seguridad, de modo que **no existe aún un parche o mitigación oficial** cuando el atacante la usa.
En otras palabras: la vulnerabilidad tiene “día cero” porque el proveedor tiene 0 días para arreglarla antes de que se esté explotando.

### Conceptos clave (para no confundir)

- **Vulnerabilidad zero-day:** el fallo de seguridad que aún no es público ni parchado.
- **Exploit zero-day:** el código o técnica que aprovecha esa vulnerabilidad.
- **Ataque zero-day:** la campaña/operación real que usa ese exploit contra víctimas.
- **Ventana de exposición (window of exposure):** tiempo entre que se descubre (o se empieza a explotar) la vulnerabilidad y que se despliega un parche efectivo.

### Ciclo de vida típico de una amenaza de día cero

1. **Descubrimiento** — un investigador, un atacante o una agencia detecta una falla.
2. **Weaponización** — se desarrolla el exploit (programa, payload o técnica) que la aprovecha.
3. **Distribución / explotación** — el exploit se usa: spear-phishing, watering hole, paquete malicioso, etc.
4. **Detección** — víctimas, proveedores o la comunidad detectan comportamiento extraño.
5. **Mitigación / parcheo** — el proveedor desarrolla y distribuye un parche; las defensas se actualizan.
6. **Divulgación / seguimiento** — se publica CVE, se comparte IOC (indicadores) y se estudia el impacto.

### ¿Quién usa ataques de día cero?

- **Grupos APT estatales** (espionaje de alto valor).
- **Ciberdelincuentes** que buscan dinero (ransomware, fraude).
- **Corredores de 0-day** o mercados privados (compra/venta de exploits).
- **Investigadores y bug-bounty hunters** (cuando reportan responsablemente, no explotando).

### ¿Quiénes son objetivo?

- **Gobiernos y organizaciones sensibles** (infraestructura, defensa, diplomacia).
- **Empresas grandes (propiedad intelectual)**.
- **Bancos y entidades financieras**.
- **Usuarios finales** cuando el exploit se emplea masivamente (p. ej. ransowmare que se propaga).

### Ejemplos famosos (breve)

- **Stuxnet (2010)** — exploitó varias vulnerabilidades de Windows desconocidas y fue usado contra instalaciones industriales; un clásico de “arma zero-day” dirigido.
- **Heartbleed (2014)** — vulnerabilidad en OpenSSL que permitió leer memoria privada; fue una falla crítica explotada antes de su parche generalizado.
- **EternalBlue → WannaCry / NotPetya (2017)** — exploit (presuntamente reservado por una agencia) que, tras filtrarse, se usó masivamente en ransomware.
- **PrintNightmare (2021)** — vulnerabilidades en el servicio de impresión de Windows que permitieron escalado de privilegios; se explotó antes de mitigaciones completas.

(Estos ejemplos muestran por qué los zero-days son tan peligrosos: acceso, furtividad y alto impacto.)

### Ejemplo hipotético paso a paso (para visualizar)

1. Un investigador descubre un bug en el servicio web de un proveedor de VPN corporativa.
2. Crea un exploit que permite ejecución remota de código.
3. Un grupo criminal compra el exploit en un mercado privado.
4. Lo usa contra compañías que usan esa VPN: obtiene credenciales y despliega ransomware.
5. El proveedor recibe evidencia, desarrolla parche y publica actualización; muchas empresas ya fueron afectadas en la ventana de exposición.

### ¿Por qué son difíciles de detectar?

- No hay firmas conocidas para antivirus/IPS.
- El exploit puede ser muy “silencioso” (larga persistencia, movimientos laterales).
- Las defensas tradicionales basadas en firmas fallan; hacen falta detección por **comportamiento** y contexto.

### Cómo identificar una potencial explotación de día cero

- **Comportamiento anómalo**: procesos que se comunican con dominios raros, privilegios inesperados, cifrado masivo repentino.
- **Alertas de EDR/XDR** por técnicas (lateral movement, privilege escalation) sin firma asociada.
- **Aumento inusual de fallos o crashes** en un servicio específico.
- **Threat intelligence**: informes que indican campañas nuevas contra software concreto.

### Cómo protegerse (medidas prácticas — organizaciones)

1. **Defensa en profundidad**: no confiar en un solo control.
2. **EDR/XDR y detección basada en comportamiento**: bloquear anomalías, no solo firmas.
3. **Segmentación de red** y principio de mínimo privilegio: limitar alcance si algo es comprometido.
4. **Respuesta a incidentes preparada**: playbooks, backups offline, pruebas de restauración.
5. **Gestión de parches ágil**: testeo rápido y despliegue cuando hay un parche.
6. **WAF / mitigaciones temporales**: filtros, reglas bloqueantes, virtual patching.
7. **Hardening y reducción de la superficie de ataque**: cerrar servicios innecesarios.
8. **Monitoreo y threat intelligence**: compartir IOC, suscribirse a feeds y participar en ISACs.
9. **Bug bounty / divulgación responsable**: incentivar que se reporten fallos antes de explotarlos.
10. **Copia de seguridad y recuperación**: especialmente para mitigar ataques que buscan cifrar datos.

### Cómo protegerse (medidas para usuarios finales)

- Mantén **sistemas y aplicaciones actualizadas**.
- Activa **multifactor (MFA)** en cuentas críticas.
- Evita descargar software de fuentes no confiables.
- Usa soluciones con detección por comportamiento y mantén backups regulares.
- Desconfía de documentos o enlaces inesperados (spear-phishing es un vector común de entrega).

### Buenas prácticas de respuesta tras un posible zero-day

1. **Contención**: aislar sistemas afectados.
2. **Recolectar evidencia** (logs, muestras) para análisis forense.
3. **Aplicar mitigaciones temporales** (bloquear puertos, reglas WAF).
4. **Coordinar con el proveedor** y seguir su plan de parcheo.
5. **Comunicar a stakeholders** y, si procede, a autoridades y clientes.
6. **Remediación y lecciones aprendidas**: reforzar controles para reducir ventana de exposición futura.

### Resumen práctico (checklist rápido)

- ¿Tienes EDR/XDR? ✔
- ¿Segmentas y limitas privilegios? ✔
- ¿Tienes playbook de respuesta y backups offline? ✔
- ¿Participas en feeds de threat intel / bug bounty? ✔
- ¿Aplicas mitigaciones temporales hasta parche? ✔

### Conclusión

Una **amenaza de día cero** es peligrosa porque ataca donde no hay defensa previa: ausencia de parche + desconocimiento = ventana de daño. La mejor estrategia es **reducir la superficie de ataque**, detectar comportamientos anómalos rápido y estar preparados para responder (contención, mitigación y parcheo rápido).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 8**                    | **Siguiente 10**                   |
| ------------------ | ------------------------------ | ---------------------------------- |
| [🏠](../README.md) | [⏪](./9_8_Dumpster_Diving.md) | [⏩](./9_10_Social_Engineering.md) |
