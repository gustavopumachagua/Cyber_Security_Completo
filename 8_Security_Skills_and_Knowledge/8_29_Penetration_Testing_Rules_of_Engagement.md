| **Inicio**         | **atrás 28**                                  | **Siguiente 30**                                 |
| ------------------ | --------------------------------------------- | ------------------------------------------------ |
| [🏠](../README.md) | [⏪](./8_28_Basics_of_Reverse_Engineering.md) | [⏩](./8_30_Perimeter_vs_DMZ_vs_Segmentation.md) |

---

## **Índice**

| Temario                                                                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [260. ¿Por qué son importantes las reglas de compromiso para una prueba de penetración?](#260-por-qué-son-importantes-las-reglas-de-compromiso-para-una-prueba-de-penetración) |
| [261. CompTIA Pentest+: Reglas de participación](#261-comptia-pentest-reglas-de-participación)                                                                                 |

# **Reglas de actuación en las pruebas de penetración**

## **260. ¿Por qué son importantes las reglas de compromiso para una prueba de penetración?**

![pruebas de penetración](/img/8_Security_Skills_and_Knowledge/pruebas_de_penetracion.jpg "pruebas de penetración")

### 🔑 ¿Por qué son importantes las Reglas de Compromiso?

1. **Aspecto legal y ético**

   - Evitan que el pentester sea acusado de **acceso no autorizado**, ya que definen por escrito el permiso explícito.
   - Protegen al cliente y al proveedor en caso de disputas o consecuencias no deseadas.

2. **Alcance claramente definido**

   - Establecen **qué sistemas, aplicaciones, redes, servicios o entornos** serán evaluados y cuáles no.
   - Esto previene pruebas en sistemas críticos fuera de alcance que podrían interrumpir operaciones.

3. **Gestión del riesgo**

   - Determinan hasta dónde se puede llegar (por ejemplo: explotación limitada vs. sin explotación).
   - Previenen daños colaterales, como caída de servicios, pérdida de datos o impacto en clientes finales.

4. **Coordinación y comunicación**

   - Incluyen protocolos de comunicación (contactos de emergencia, horarios permitidos, cómo reportar hallazgos críticos en vivo).
   - Esto asegura que no haya malentendidos durante la prueba.

5. **Confianza y profesionalismo**

   - El cliente sabe exactamente lo que se hará.
   - El pentester se asegura de que sus acciones están respaldadas por autorización oficial.

### 📋 ¿Qué incluyen las Reglas de Compromiso?

Aunque pueden variar, un buen documento de **RoE** incluye al menos:

1. **Objetivo de la prueba**

   - Ejemplo: _“Evaluar la resistencia de la aplicación web de e-commerce contra ataques comunes OWASP Top 10”_.

2. **Alcance**

   - Sistemas, aplicaciones, IPs, dominios, APIs, redes, dispositivos o entornos que se pueden atacar.
   - Qué está **fuera de alcance** (ejemplo: la red de producción de clientes, servidores de nómina, sistemas SCADA sensibles).

3. **Limitaciones técnicas**

   - Qué tipos de ataques están permitidos o prohibidos (ejemplo: no hacer DoS, no explotar vulnerabilidades que provoquen pérdida de datos).
   - Nivel de intrusión permitido: prueba de **caja negra**, **caja gris** o **caja blanca**.

4. **Reglas de explotación**

   - ¿Se permite explotar vulnerabilidades o solo identificarlas?
   - ¿Se pueden elevar privilegios y acceder a datos sensibles?
   - Ejemplo: _“El pentester puede demostrar acceso a datos de un usuario ficticio, pero no de clientes reales”_.

5. **Ventanas de tiempo permitidas**

   - Horarios de prueba para minimizar riesgos (ejemplo: fuera de horario laboral).
   - Duración de la prueba y fechas de inicio/fin.

6. **Protocolos de comunicación**

   - Quién es el contacto principal del cliente.
   - Cómo se reportan hallazgos críticos en tiempo real (por ejemplo, una vulnerabilidad que expone datos sensibles).
   - Procedimiento en caso de incidente no planificado (ejemplo: caída accidental de un servidor).

7. **Requisitos legales y de confidencialidad**

   - Firmas de autorización explícita.
   - Acuerdo de confidencialidad (NDA).
   - Cláusulas sobre uso y almacenamiento de datos sensibles.

8. **Criterios de éxito**

   - Qué resultados espera el cliente (ejemplo: informe con riesgos clasificados según CVSS y recomendaciones de mitigación).

9. **Entrega de resultados**

   - Qué tipo de reporte se entregará:

     - Informe técnico (para el equipo de TI).
     - Resumen ejecutivo (para gerencia).

   - Formato y fecha de entrega.

### 📌 Ejemplo práctico

Un banco contrata un pentest a su **portal de banca en línea**.
En las **RoE** acuerdan:

- Alcance: solo _[https://online.banco.com](https://online.banco.com)_, no el core bancario.
- Técnicas prohibidas: **no DoS ni ataques de fuerza bruta masiva**.
- Explotación: permitido extraer datos de **usuarios de prueba creados para el pentest**, nunca de clientes reales.
- Horario: pruebas entre las **00:00 y 06:00** para evitar afectar clientes activos.
- Comunicación: si se detecta fuga de información, avisar **inmediatamente al CISO** por teléfono y correo.

Esto protege tanto al banco como al pentester y evita incidentes graves.

---

[🔼](#índice)

---

## **261. CompTIA Pentest+: Reglas de participación**

> Nota rápida: en el examen CompTIA Pentest+ se espera que sepas cómo **planificar y acotar** una prueba, reconocer responsabilidades legales/éticas y entender controles operativos (scopes, comunicación, limitaciones). Las RoE son la base que convierte una prueba en una actividad autorizada y segura.

### ¿Qué son las Reglas de Participación (RoE) y por qué importan?

Las **Reglas de Participación** son el **acuerdo formal** entre el cliente (propietario del sistema) y el equipo de pentest que define **qué** se va a probar, **cómo**, **cuándo**, **con qué límites**, **quiénes** participan, y **qué hacer** si algo sale mal.

Importancia práctica y para Pentest+:

- Protegen legalmente al pentester (autorización escrita).
- Protegen al cliente (limitan el impacto y definen comunicaciones).
- Alinean expectativas (entregables, SLAs, criterios de éxito).
- Son requisito para un pentest profesional y aparecen en los dominios de **scoping, legal, y ejecución** de CompTIA Pentest+.

### Elementos imprescindibles de un RoE (qué debe incluir)

1. **Identidad y autorizaciones**

   - Nombre del cliente, organización, persona autorizante (CISO) con firma.
   - Nombre del proveedor/consultora y equipo de pentest.
   - Fecha de vigencia y periodo de la prueba.

2. **Objetivo y alcance**

   - Objetivo general (ej.: “Evaluar seguridad del portal de clientes”).
   - Activos IN-SCOPE (IPs, FQDN, APIs, aplicaciones, entornos: prod/staging).
   - Activos OUT-OF-SCOPE (sistemas críticos, pasarelas de pago, SCADA, entornos de terceros).

3. **Tipo de prueba y nivel de intrusión**

   - Black box / Grey box / White box (y qué credenciales se entregan).
   - Nivel de explotación permitido (detección-only, exploit safe, full exploitation in staging).

4. **Limitaciones técnicas y prohibiciones**

   - Prohibido: DoS/DDoS, fuzzing destructivo, manipular DBs de clientes reales, ingeniería social sin permiso, etc.
   - Herramientas “sensible” solo con aprobación previa (ej.: Metasploit, password crackers en entornos prod).

5. **Ventanas de ejecución / horarios**

   - Fechas y horarios permitidos (ej.: 00:00–06:00 para prod) y ventanas para staging.
   - Mecanismo para solicitar ampliación o cambio de ventana.

6. **Comunicación y contactos**

   - Contacto técnico (on-call), contacto de negocio/CISO, SOC/NetOps.
   - Canales permitidos (teléfono directo, correo cifrado, chat corporativo).
   - Protocolo de reporte inmediato para hallazgos críticos (p. ej. exposición de PII).

7. **Escalación y “kill switch”**

   - Definición de condiciones para detener la prueba (caída de servicio, pérdida de datos).
   - Método de corta inmediata (kill switch): llamada telefónica + correo marcado URGENTE.

8. **Evidencia y manejo de datos**

   - Qué evidencia se recolecta (pcaps, logs, screenshots), cómo se almacena y por cuánto tiempo.
   - Reglas para el tratamiento de PII: anonimizar, no exportar fuera de la jurisdicción, cifrado en reposo.
   - Cadena de custodia para evidencias que puedan usarse legalmente.

9. **Privacidad y cumplimiento**

   - Confirmación de cumplimiento con leyes aplicables (GDPR, HIPAA, etc.) y restricciones geográficas.
   - ¿Se pueden tocar datos sensibles? (preferible usar cuentas/ datos de prueba).

10. **Términos de explotación y PoC**

    - Pueden demostrarse PoC (captura de pantalla, acceso a una cuenta de prueba), **no** extracción real de datos.
    - Si se extraen muestras, deben ser datos ficticios o enmascarados.

11. **Remediación y retesting**

    - ¿Se realiza retest? plazo para verificación tras remediaciones.
    - Prioridad y SLAs para reporte de hallazgos críticos.

12. **Entregables**

    - Informe técnico (detallado), resumen ejecutivo, anexos (pcap, PoC).
    - Formatos y fechas de entrega.

13. **Confidencialidad y propiedad intelectual**

    - NDA / uso y difusión de resultados.
    - Derechos sobre herramientas y scripts creados durante la prueba.

14. **Aceptación de riesgos y seguros**

    - Declaración de riesgos residuales.
    - Seguro de responsabilidad profesional (indicar cobertura si hay).

15. **Firmas y aceptación**

    - Firma del cliente, del proveedor y fecha de aceptación.

### Reglas prácticas (Do’s & Don’ts) — lo que CompTIA espera saber

Do’s:

- Documenta todo: comunicaciones, decisiones, evidencias.
- Usa cuentas de prueba siempre que sea posible.
- Coordina con SOC para evitar respuestas automáticas (bloqueos).
- Define claramente objetivos y métricas de éxito.

Don’ts:

- No realices pruebas fuera del horario/alcance acordado.
- No ejecutes técnicas destructivas (p. ej. borrado de datos) sin permiso expreso.
- No publiques vulnerabilidades sin proceso de divulgación responsable.

### Plantilla de RoE (compacta — para adaptar)

```
REGLAS DE PARTICIPACIÓN (Rules of Engagement)

1. Partes:
   - Cliente: ACME S.A. (CISO: María Pérez, maria.perez@acme.com)
   - Proveedor: SecTest S.L. (Lead: Juan López, phone: +51 9...)

2. Vigencia:
   - Inicio: 2025-09-15 00:00 UTC
   - Fin:    2025-09-19 23:59 UTC

3. Objetivo:
   - Evaluar seguridad de la aplicación web pública y su API (identificar vulnerabilidades de explotación real).

4. Alcance:
   - IN: https://www.acme.com (FQDN), API: api.acme.com (IPs: 198.51.100.10–12)
   - OUT: Pasarela de pagos (pci.acme.com), entornos IoT, sistemas HR (10.10.100.0/24)

5. Tipo de prueba:
   - Grey box: credenciales de usuario de prueba y documentación API entregadas.

6. Limitaciones:
   - Prohibido DoS/DDoS, manipular DB con datos reales, ataques físicos, ingeniería social, escaneo fuera de IPs acordadas.
   - Uso de exploit con payloads destructivos NO permitido en producción.

7. Comunicación:
   - Contacto cliente: SOC (soc@acme.com), móvil +51...
   - Reporte crítico: llamar inmediatamente al CISO y enviar correo cifrado.

8. Kill Switch:
   - Cualquier caída de servicio > 2 minutos: pentester debe detener actividad y notificar inmediatamente.
   - Procedimiento: llamada SOS → confirmación cliente → pausa de 60 min.

9. Evidencia y manejo:
   - Evidencia cifrada en S3 con KMS, acceso solo a equipo de pentest y CISO.
   - PII: no exportar sin enmascaramiento.

10. Entregables:
    - Informe técnico (30 días), resumen ejecutivo (7 días), anexos (pcap, PoC).

11. Firmas:
    - ____________________  Cliente (CISO)
    - ____________________  Proveedor (Lead Tester)
```

### Ejemplos reales de RoE (resumidos)

#### Ejemplo A — Pentest de **aplicación web pública** (e-commerce)

- **Scope IN:** FQDN pública, API, subdominios listados.
- **Scope OUT:** Entorno de pagos, CDN del proveedor, servicios de terceros.
- **Limitaciones:** No realizar ataque de carga ni manipular DB en vivo; permitir pruebas de inyección solo en endpoints de staging si se prueba explotación real.
- **Comunicación:** Reporte crítico inmediato si se descubre exposición de tarjetas.
- **Resultado esperado:** Informe con PoC limitado (screenshots) y recomendaciones.

#### Ejemplo B — Pentest de **red interna** (corporate LAN)

- **Scope IN:** Rango 10.20.0.0/24, servidores críticos listados.
- **Type:** Grey-box (credenciales de usuario no privilegiado entregadas).
- **Prohibición:** No ejecutar escáneres intensivos (Masscan con tasa alta) que saturen enlaces; evitar ataques que requieran reinicio.
- **Kill switch:** Si servidor de correo deja de aceptar conexiones, pausar y notificar SOC.
- **Evidencia:** Dump de memoria solo en sistemas aislados; cadena de custodia documentada.

### Comunicación en vivo: cómo reportar un hallazgo crítico (ejemplo)

**Situación:** pentester encuentra endpoint que filtra registros PII.

1. Call inmediata al contacto SOC (teléfono + email cifrado).
2. Envío de **nota corta**: “HIGH — Exposed PII on /api/users. Proof-of-concept captured. Stop-testing requested?”
3. Esperar respuesta del CISO/SOC (máx 30 min).
4. Si cliente solicita detener, cesar pruebas y preparar entrega inmediata con mitigación temporal (p. ej. WAF rule, bloqueo de IP).

### Prácticas para evidencias y reporting (detallado)

- **Registrar timestamps con zona horaria** y usar NTP sincronizado.
- **Hash** de archivos de evidencia (SHA256) al momento de la captura.
- **Almacenar evidencia cifrada** y con control de acceso (logs de acceso a evidencias).
- **Generar PoC reproducible** en entorno de staging si posible (para validar remedios).
- **Separar informes**: técnico (detalles, explotación, PoC) y ejecutivo (impacto, riesgo y mitigación priorizada).

### Errores comunes que debes evitar

- Empezar sin RoE firmadas.
- No coordinar con SOC: activas alertas y generas ruido innecesario.
- Usar credenciales de producción sin control.
- No tener un kill switch claro.
- Entregar PoC con datos reales sin enmascarar.

### Checklist rápido pre-ejecución (lista para usar)

- [ ] RoE firmadas por cliente y proveedor.
- [ ] Inventario de activos in-scope/out-of-scope final.
- [ ] Contactos de emergencia y SOC notificados.
- [ ] Ventanas de prueba definidas y aprobadas.
- [ ] Credenciales de prueba entregadas (si aplica).
- [ ] Plan de evidencia y almacenamiento cifrado.
- [ ] Kill switch y procedimiento de parada definidos.
- [ ] Aprobación por el área legal/privacidad si se manejará PII.
- [ ] Seguro de responsabilidad y cobertura revisados.

### Conexión directa con CompTIA Pentest+

- En el examen se evalúa tu capacidad de: definir alcance, distinguir tipos de pruebas (black/grey/white), identificar limitaciones legales/éticas y diseñar procedimientos de comunicación y recolección de evidencia — todo esto está contenido en y guiado por las **Reglas de Participación**.
- Apréndete los componentes clave (alcance, límites, comunicaciones, evidencia, kill switch) y practica redactar RoE simples: eso te da ventaja en preguntas prácticas del examen.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 28**                                  | **Siguiente 30**                                 |
| ------------------ | --------------------------------------------- | ------------------------------------------------ |
| [🏠](../README.md) | [⏪](./8_28_Basics_of_Reverse_Engineering.md) | [⏩](./8_30_Perimeter_vs_DMZ_vs_Segmentation.md) |
