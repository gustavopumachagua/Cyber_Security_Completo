| **Inicio**         | **atrás 9**             | **Siguiente 11**               |
| ------------------ | ----------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./9_9_Zero_Day.md) | [⏩](./9_11_Reconnaissance.md) |

---

## **Índice**

| Temario                                                                |
| ---------------------------------------------------------------------- |
| [285. ¿Qué es la ingeniería social?](#285-qué-es-la-ingeniería-social) |
| [286. Ingeniería social](#286-ingeniería-social)                       |

# **Ingeniería social**

## **285. ¿Qué es la ingeniería social?**

![Ingeniería social](/img/9_Attack_Types_and_Differences/Ingenieria_social.png "Ingeniería social")

La **ingeniería social** es la manipulación psicológica de las personas para que revelen información confidencial, otorguen acceso o realicen acciones que comprometen la seguridad.

👉 En vez de “hackear máquinas”, los atacantes **hackean la confianza humana**.

Ejemplo simple:

- Un atacante finge ser de soporte técnico y llama a un empleado:

  > “Hola, soy de IT, necesito tu usuario y contraseña para resolver un problema urgente”.

- El empleado, confiando, se las da.

  👉 La red fue comprometida sin usar malware ni exploits técnicos.

### ⚙️ ¿Cómo funciona la ingeniería social?

La mayoría de ataques siguen un ciclo:

1. **Investigación (reconocimiento)**: el atacante recopila información sobre la víctima (redes sociales, correos, cargos en la empresa).
2. **Generación de confianza o presión**: crea un pretexto creíble (ej. “Soy del banco”, “Soy tu jefe”).
3. **Explotación**: manipula para que la víctima entregue información, haga clic en un enlace, descargue un archivo o permita acceso físico.
4. **Ejecución**: usa la información obtenida para un ataque más grande (robo de datos, fraude, espionaje).

### ⚠️ ¿Por qué es tan peligrosa la ingeniería social?

- **Exploita emociones humanas**: miedo, urgencia, confianza, curiosidad o cortesía.
- **Difícil de detectar**: los firewalls y antivirus no siempre bloquean a alguien que “pide amablemente la clave”.
- **Alta tasa de éxito**: basta con que **una sola persona caiga** para comprometer a toda la organización.
- **Complementa ataques técnicos**: se usa junto a malware, phishing o ransomware.

Ejemplo: un atacante manda un correo de phishing con un enlace falso de inicio de sesión de Microsoft 365 → el empleado cae → el atacante roba credenciales → accede al correo corporativo.

### 🛡️ ¿Cómo protegerse contra la ingeniería social?

#### ✅ A nivel personal

- **Desconfía de urgencias sospechosas** (“¡actúe ahora o su cuenta será bloqueada!”).
- **Verifica identidades**: llama tú mismo a la empresa o área antes de dar datos.
- **Nunca compartas contraseñas** ni códigos MFA.
- **Configura MFA** en cuentas críticas (aunque roben la contraseña, no podrán entrar fácilmente).
- **Sé discreto en redes sociales**: menos información pública, menos material para los atacantes.

#### ✅ A nivel empresarial

- **Capacitación continua**: simular ataques de phishing para entrenar a los empleados.
- **Políticas claras**: IT nunca pedirá contraseñas por correo o teléfono.
- **Controles técnicos**:

  - Filtros anti-phishing en correos.
  - EDR/XDR para detectar actividad sospechosa.
  - Segmentación de red para limitar daños.

- **Cultura de seguridad**: que los empleados se sientan cómodos reportando intentos sospechosos.

### 🔎 Tipos de ataques de ingeniería social

1. **Phishing**

   - Correos o mensajes falsos que imitan a empresas legítimas.
   - Ejemplo: “Tu cuenta PayPal fue bloqueada, haz clic aquí para verificar”.

2. **Spear phishing**

   - Versión dirigida: correos muy personalizados a una persona o empresa.
   - Ejemplo: correo al CFO de una empresa haciéndose pasar por el CEO.

3. **Whaling (caza de ballenas)**

   - Variante de spear phishing contra ejecutivos de alto rango.

4. **Smishing**

   - Phishing vía SMS.
   - Ejemplo: “Su banco detectó un ingreso sospechoso, haga clic para bloquearlo”.

5. **Vishing**

   - Phishing por llamadas telefónicas.
   - Ejemplo: un supuesto “soporte técnico” que pide acceso remoto.

6. **Pretexting**

   - El atacante inventa una historia (pretexto) para obtener datos.
   - Ejemplo: “Soy de recursos humanos, necesito verificar tu información bancaria”.

7. **Baiting**

   - Ofrecer algo atractivo a cambio de que la víctima caiga.
   - Ejemplo: dejar un USB infectado en la oficina con etiqueta “Confidencial – salarios 2025”.

8. **Tailgating / Piggybacking**

   - Acceso físico a instalaciones siguiendo o convenciendo a alguien que lo deje entrar.

9. **Dumpster Diving**

   - Buscar información sensible en la basura (documentos, discos duros, recibos).

10. **Shoulder Surfing**

- Observar directamente la pantalla o el teclado de alguien para robar credenciales.

### 📌 Resumen

- La **ingeniería social** manipula a las personas para que, sin darse cuenta, comprometan la seguridad.
- Es peligrosa porque ataca el **eslabón más débil: el factor humano**.
- Puede hacerse por correo, teléfono, redes sociales, basura física o incluso cara a cara.
- La mejor defensa es la **conciencia, educación y buenas prácticas**, junto con controles técnicos de apoyo.

---

[🔼](#índice)

---

## **286. Ingeniería social**

La **ingeniería social** es el conjunto de técnicas que usan los atacantes para **manipular a las personas** y lograr que hagan algo que comprometa la seguridad: revelar datos, ejecutar una acción (transferencia, instalar un archivo) o permitir acceso físico.

No es un ataque técnico: es un ataque contra la **psicología y la confianza humana**.

### Por qué funciona la ingeniería social

Los humanos responden a atajos mentales y emociones:

- **Autoridad** (si “lo pide el jefe”, obedecemos).
- **Urgencia** (miedo a perder algo o sanciones).
- **Reciprocidad** (si alguien nos hace un favor, queremos corresponder).
- **Afinidad / simpatía** (si nos cae bien la persona, confiamos).
- **Prueba social** (si “todos lo hacen”, lo hago).
- **Consistencia** (queremos mantener coherencia con acciones pasadas).

Los atacantes construyen pretextos que activan estos sesgos: por eso la ingeniería social sigue siendo muy efectiva.

### Tipos comunes de ataques (con ejemplos)

1. **Phishing (correo masivo)**

   Ejemplo: un email "[banco@seguro.com](mailto:banco@seguro.com)" que dice “su cuenta será bloqueada — haga clic aquí”.

   Señal: enlaces sospechosos, urgencia, saludo genérico.

2. **Spear phishing (dirigido)**

   Ejemplo: correo a un empleado de RRHH con datos sobre un proceso de selección real y un adjunto que “contiene CVs”.

   Señal: personalización (nombre, proyecto), lenguaje conocido.

3. **Whaling (ataque a ejecutivos)**

   Ejemplo: mensaje al CFO aparentando ser el CEO pidiendo una transferencia urgente.

   Señal: petición financiera o legal urgente, fuera de proceso.

4. **Vishing (llamada telefónica fraudulenta)**

   Ejemplo: llamada de “soporte técnico” pidiendo un código de autenticación.

   Señal: solicitud de contraseñas, presión para actuar “ahora”.

5. **Smishing (SMS fraudulento)**

   Ejemplo: SMS de una “empresa de paquetería” con enlace para pagar una tarifa.

   Señal: enlaces cortos, números desconocidos.

6. **Pretexting**

   Ejemplo: alguien se hace pasar por auditor/a y pide acceso a una sala o a archivos “para una revisión”.

   Señal: historias muy elaboradas sin confirmación oficial.

7. **Baiting**

   Ejemplo: dejar una USB etiquetada “Bonos 2025” en la sala; quien la conecta ejecuta código malicioso.

   Señal: un “regalo” sin procedencia clara.

8. **Tailgating / Piggybacking**

   Ejemplo: entrar detrás de un empleado que abre la puerta con su tarjeta, fingiendo tener las manos ocupadas.

   Señal: personas que entran sin identificación visible o sin registro de visitante.

9. **Dumpster diving**

   Ejemplo: recuperar impresos con datos de clientes de la basura.

   Señal: información sensible descartada sin destruir.

### Ciclo típico de un ataque de ingeniería social (alto nivel)

1. **Reconocimiento**: recopilar información pública (LinkedIn, web, redes).
2. **Preparación del pretexto**: crear la historia convincente (falso proveedor, jefe, soporte).
3. **Contacto**: ejecutar el engaño (email, llamada, mensaje, presencial).
4. **Explotación**: obtener credenciales, que ejecuten una transferencia, descarguen un archivo, o abran una puerta.
5. **Uso de la información**: acceder a sistemas, robar datos, extorsionar, etc.

> Nota: describo el flujo para que puedas **defender** contra cada fase; no doy instrucciones de ataque.

### Señales de alerta (qué mirar para detectar ingeniería social)

- Peticiones que rompen procesos internos (p. ej. transferencias sin doble validación).
- Lenguaje urgente que busca acortar la verificación.
- Remitentes con dominios parecidos pero no exactos (micr0soft vs microsoft).
- Enlaces que muestran un dominio distinto al texto (hover para ver la URL).
- Archivos adjuntos inesperados o macros solicitando habilitación.
- Solicitud de credenciales, códigos MFA o acceso remoto.
- Persona física sin credenciales visibles en zonas restringidas.

### Ejemplos (analizados) — buen material para formación

#### Ejemplo de email (spear phishing) — análisis

```
De: soporte@empresa-partner.com
Para: maria@miempresa.com
Asunto: Actualización crítica del contrato – leer antes del viernes

Hola María,
Adjunto encontrará la versión final del contrato con el proveedor X. Necesitamos tu aprobación hoy para cumplir los plazos. Revisa el adjunto y confirma.

Saludos,
Ricardo – Procurement
```

- ¿Por qué podría engañar? menciona proyecto real y plazo.
- ¿Qué revisar? confirmar remitente (¿es el dominio real?), verificar por otro canal con Ricardo (llamada o chat corporativo), analizar adjunto en sandbox antes de abrir.

#### Ejemplo de llamada (vishing)

Persona: “Soy de IT, necesitamos tu usuario para aplicar un parche ahora. Si no lo doy la actualización falla.”
Respuesta segura: “Gracias, prefiero que me envíen la solicitud por el sistema de tickets y que lo confirme con mi jefe.” — y reportar llamada.

### Cómo protegerse (personas y organizaciones)

#### Medidas personales (fáciles de aplicar)

- **Pausar**: antes de responder, haz una pausa de 30–60 segundos.
- **Verificar por otro canal**: si alguien pide algo crítico, llama al número oficial o contacta por chat corporativo.
- **No compartir contraseñas ni códigos MFA**.
- **Usar gestor de contraseñas** y autenticación multifactor (MFA).
- **Limitar lo que publicas en redes**: menos información pública = menos material para atacantes.
- **No conectar memorias USB encontradas**.

#### Medidas organizacionales (estratégicas)

- **Políticas de seguridad claras** (p. ej. “transferencias > X requieren doble autorización presencial/por llamada segura”).
- **Entrenamiento continuo** (phishing simulations, role playing).
- **Canal de reporte sencillo** y recompensa por reportar intentos de fraude.
- **Controles técnicos**: filtros antiphishing, EDR/XDR, DLP, WAF, registros centralizados (SIEM).
- **Principio de mínimo privilegio** y segmentación de red.
- **Verificación obligatoria para visitantes** (acreditación, registro, acompañamiento).
- **Test y auditoría de procesos**: revisar excepciones y casos en que se violan los procesos.

### Respuesta a incidentes (mini-playbook para cuando ocurre algo)

1. Aislar el equipo o cuenta afectada.
2. Revocar sesiones y credenciales comprometidas; forzar restablecimiento de contraseñas.
3. Activar MFA si no está activo.
4. Reunir evidencia (emails, cabeceras, registros de llamada).
5. Notificar a seguridad / TI y, si aplica, a clientes o autoridades.
6. Restaurar desde backup si hubo cifrado.
7. Aprender: actualizar políticas, comunicar lecciones a la organización.

### Cómo medir la efectividad de la defensa (KPIs)

- **Tasa de clics en simulacros** (phish-prone %).
- **Tasa de reporte** (cuántos empleados reportan correos sospechosos).
- **Tiempo medio de detección y respuesta** (MTTD / MTTR).
- **Número de incidentes reales por trimestre**.
- **Porcentaje de cuentas con MFA**.

### Plantillas útiles (copia/pega)

#### Mensaje rápido para verificar una solicitud interna

```
Hola [Nombre],

¿Puedes reenviarme esta solicitud a través del sistema oficial / ticket #[ID] o confirmarla por llamada al número corporativo? Necesito validar el origen antes de proceder.

Gracias,
[Tu nombre]
```

#### Reporte de incidente a TI (si recibes un phishing)

```
Asunto: Reporte: posible phishing recibido – [fecha]

Equipo de seguridad,
He recibido un correo con asunto "[asunto]" de "[remitente]". No hice clic en enlaces. Adjunto captura y cabeceras. Por favor, indíquenme próximos pasos.

Saludos,
[nombre] – [puesto]
```

### Buenas prácticas de formación (qué funciona)

- Simulacros regulares + feedback inmediato.
- Escenarios realistas (spear phishing, vishing, USB baiting).
- Formación basada en casos reales (qué pasó y lecciones).
- Incluir ejecutivos en entrenamientos (whaling).
- Métricas públicas internas (sin avergonzar) para motivar mejora.

### Mensaje final — cómo pensar ante una posible manipulación

Antes de actuar, pregúntate:

1. ¿Esto es habitual?
2. ¿Puedo verificarlo por otro canal?
3. ¿Por qué me lo piden a mí y no al proceso oficial?
4. ¿Siento presión o urgencia innecesaria?

Si alguna respuesta es “no” o “sospechosa”, detente y verifica.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 9**             | **Siguiente 11**               |
| ------------------ | ----------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./9_9_Zero_Day.md) | [⏩](./9_11_Reconnaissance.md) |
