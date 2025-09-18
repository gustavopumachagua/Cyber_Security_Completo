| **Inicio**         | **atrás 2**                                  | **Siguiente 4**                             |
| ------------------ | -------------------------------------------- | ------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_2_False_Negative_False_Positive.md) | [⏩](./8_4_Basics_of_Threat_Intel_OSINT.md) |

---

## **Índice**

| Temario                                                                                                                                                  |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [204. Falsos positivos y falsos negativos en la seguridad de la información](#204-falsos-positivos-y-falsos-negativos-en-la-seguridad-de-la-información) |
| [205. Falsos positivos y falsos negativos](#205-falsos-positivos-y-falsos-negativos)                                                                     |

# **Verdadero negativo / Verdadero positivo**

## **204. Falsos positivos y falsos negativos en la seguridad de la información**

![Verdadero negativo / Verdadero positivo](/img/8_Security_Skills_and_Knowledge/Verdadero_negativo_Verdadero_positivo.png "Verdadero negativo / Verdadero positivo")

### 📌 Descripción general

En ciberseguridad, los sistemas de detección como **antivirus, EDR, IDS/IPS, SIEM o firewalls** deben diferenciar entre **actividades legítimas** y **amenazas reales**.

- Cuando el sistema se equivoca, genera **falsos positivos** o **falsos negativos**.
- Encontrar un equilibrio entre ambos es clave para la **eficacia y eficiencia** de un SOC (Security Operations Center).

### ✅ Falsos positivos: ¿qué son?

Un **falso positivo** ocurre cuando una herramienta de seguridad **marca una actividad legítima como maliciosa**.

📌 Ejemplos:

- Un antivirus bloquea un instalador oficial porque lo confunde con un troyano.
- Un IDS detecta tráfico normal de backup como un ataque de exfiltración.
- Un correo corporativo legítimo llega a la carpeta de spam.

👉 Aunque no representan una amenaza real, consumen tiempo y recursos del equipo de seguridad.

### 🔍 Cómo identificar mejor los falsos positivos

- **Correlación de alertas:** comparar con otras fuentes de telemetría (logs de endpoints, firewalls, SIEM).
- **Contexto del sistema:** verificar qué usuario/proceso originó la actividad.
- **Análisis de threat intelligence:** comprobar reputación de IPs, dominios o hashes.
- **Whitelisting / Allowlisting:** marcar aplicaciones legítimas para que no se repitan falsas alarmas.

### ❌ ¿Qué son los falsos negativos?

Un **falso negativo** ocurre cuando una amenaza real **no es detectada** por el sistema.

📌 Ejemplos:

- Un malware polimórfico evade las firmas del antivirus.
- Un atacante realiza movimiento lateral sin generar alertas.
- Un correo phishing pasa desapercibido por el filtro de spam.

👉 Son **más peligrosos** que los falsos positivos, porque el ataque ocurre sin ser detectado.

### ⚠️ Las consecuencias de los falsos negativos

- **Intrusiones exitosas:** robo de datos, ransomware, persistencia del atacante.
- **Daños financieros:** multas, pérdida de clientes y reputación.
- **Detección tardía:** cuanto más tarde se descubre, más costosa es la remediación.

### 🔧 Cómo reducir los falsos negativos

- **Defensa en capas (defense in depth):** combinar detección basada en firmas, comportamiento y anomalías.
- **Uso de inteligencia de amenazas:** actualizar constantemente indicadores de compromiso (IOCs).
- **Simulaciones de ataque (Red Teaming / Purple Teaming):** probar la eficacia real de las defensas.
- **Machine learning adaptativo:** modelos que aprenden de patrones nuevos para no depender solo de firmas.

### 😵‍💫 El coste de la fatiga por alerta

La **fatiga por alerta** ocurre cuando los analistas reciben demasiados falsos positivos.

- Se pierde tiempo revisando incidentes que no son amenazas.
- El equipo puede **empezar a ignorar alertas reales**.
- Aumenta el **burnout del personal de SOC**, lo que impacta en la rotación de empleados.

### 💼 Impacto empresarial de la fatiga por alertas

- **Productividad reducida:** los analistas dedican horas a alarmas irrelevantes.
- **Costos de operación elevados:** más personal necesario para filtrar ruido.
- **Mayor riesgo de brechas:** amenazas críticas pueden pasar desapercibidas.
- **Decisiones erróneas:** se pierde confianza en las herramientas de seguridad.

### 🛠️ Cómo reducir las falsas alarmas

- **Optimizar reglas de detección:** ajustar firmas y políticas de seguridad.
- **Integración de fuentes de datos:** enriquecer alertas con contexto (usuario, host, historial).
- **Automatización (SOAR):** playbooks que clasifican y descartan falsos positivos automáticamente.
- **Entrenamiento del personal:** mejorar la capacidad de análisis de los equipos SOC.

### ⚙️ Optimizar la pila de tecnología de ciberseguridad

- Consolidar herramientas para evitar **alertas duplicadas**.
- Escoger soluciones con **detección basada en comportamiento** además de firmas.
- Priorizar plataformas con **análisis automático y machine learning**.
- Evaluar la integración con SIEM y SOAR para centralizar correlación.

### 🎚️ Optimizar los umbrales de alerta

El umbral de detección determina el balance entre **sensibilidad** y **precisión**.

- **Umbral bajo:** detecta más amenazas (menos falsos negativos) pero genera más falsos positivos.
- **Umbral alto:** reduce ruido (menos falsos positivos) pero aumenta el riesgo de falsos negativos.

📌 Estrategia:

- Ajustar umbrales según el **riesgo empresarial** (ej. sistemas críticos → tolerancia cero a FNs).
- Revisar continuamente los parámetros a medida que cambian los patrones de ataque.

### 📌 Conclusión

- **Falsos positivos = ruido** → consumen tiempo y generan fatiga.
- **Falsos negativos = ceguera** → permiten ataques reales.
- La clave está en **optimizar tecnología + procesos + personal** para reducir ambos.
- Empresas exitosas logran un equilibrio: **mínimos falsos negativos sin inundar al equipo con falsos positivos**.

---

[🔼](#índice)

---

## **205. Falsos positivos y falsos negativos**

### 1) Concepto simple

- **Falso positivo (FP):** el sistema **avisa que hay una amenaza** cuando en realidad **no la hay**.
  → “¡Alarma falsa!”
- **Falso negativo (FN):** el sistema **no avisa** aunque **sí existe** una amenaza.
  → “La amenaza pasa desapercibida.”

### 2) Matriz de confusión (visual)

|                   |   Predicho Positivo |   Predicho Negativo |
| ----------------- | ------------------: | ------------------: |
| **Real Positivo** |  TP (True Positive) | FN (False Negative) |
| **Real Negativo** | FP (False Positive) |  TN (True Negative) |

Donde:

- TP = detectó y era real.
- TN = no detectó y no era amenaza.
- FP = detectó pero no era amenaza.
- FN = no detectó pero sí era amenaza.

### 3) Métricas clave (fórmulas)

- **Accuracy** = (TP + TN) / Total
- **Precision** = TP / (TP + FP)
- **Recall (Sensitivity)** = TP / (TP + FN)
- **Specificity** = TN / (TN + FP)
- **FPR (False Positive Rate)** = FP / (FP + TN) = 1 − Specificity
- **FNR (False Negative Rate)** = FN / (FN + TP) = 1 − Recall

### 4) Ejemplo numérico — paso a paso (long division / digit-by-digit)

Supongamos un detector con estos resultados en 1000 eventos:

- TP = 160
- FP = 60
- FN = 40
- TN = 740
- Total = 160 + 60 + 40 + 740 = 1000 (comprobado)

Calculamos métricas:

**Accuracy**

- Suma TP + TN = 160 + 740 = 900.
- Accuracy = 900 / 1000 = 0.9 → **90.00%**

**Precision**

- Denominador TP + FP = 160 + 60 = 220.
- Precision = 160 / 220.
  Simplifico: dividir numerador y denominador entre 20 → 160/20 = 8 ; 220/20 = 11 → 8/11.
  División larga 8 ÷ 11:

  - 11 × 0 = 0 → resto 8 → punto decimal.
  - 80 ÷ 11 = 7 (7×11=77) → resto 80−77 = 3.
  - 30 ÷ 11 = 2 (2×11=22) → resto 30−22 = 8.
  - Vuelve a repetirse: 80 → 7 ... es periódico: 0.727272...
    Redondeando a 2 decimales → **72.73%**

**Recall (Sensibilidad)**

- Denominador TP + FN = 160 + 40 = 200.
- Recall = 160 / 200 = 16/20 = 4/5 = 0.8 → **80.00%**

**Specificity**

- Denominador TN + FP = 740 + 60 = 800.
- Specificity = 740 / 800.
  Simplifico dividiendo por 20 → 740/20 = 37 ; 800/20 = 40 → 37/40.
  37 ÷ 40 = 0.925 → **92.50%**

**FPR (tasa de falsos positivos)**

- FPR = FP / (FP + TN) = 60 / 800 = 6 / 80 = 3 / 40 = 0.075 → **7.50%**

**FNR (tasa de falsos negativos)**

- FNR = FN / (FN + TP) = 40 / 200 = 1 / 5 = 0.2 → **20.00%**

— Comprobaciones rápidas:

- Precision ≈ 72.73% (buen equilibrio pero hay ruido).
- Recall = 80% → el sistema captura 4 de cada 5 amenazas reales.
- FNR = 20% → 1 de cada 5 amenazas pasa desapercibida (riesgo).

### 5) Ejemplos concretos por dominio

**a) Seguridad de la información (IDS, EDR, SIEM)**

- FP: Un proceso legítimo de backup abre muchas conexiones remotas (puede parecer exfiltración) → IDS genera alerta.
- FN: Un malware polimórfico ejecuta cargas sin firmas conocidas → EDR no lo detecta → atacante obtiene persistencia.

**b) Machine Learning (clasificadores)**

- FP: Correo legítimo marcado como spam → pérdida de comunicación.
- FN: Phishing que pasa al inbox → un empleado puede caer.

**c) Medicina**

- FP: Test da positivo en una persona sana → pruebas y ansiedad innecesarias.
- FN: Test no detecta una enfermedad real → tratamiento retrasado, consecuencias graves.

**d) QA / testing**

- FP: Test automático “falla” por flaky test → ingeniero investiga bug inexistente.
- FN: Bug real no cubierto por tests → falla en producción.

### 6) Causas comunes de falsos positivos y falsos negativos (seguridad)

**Falsos positivos**

- Reglas/siganturas genéricas o mal calibradas.
- Heurísticas que confunden admin tools con malware (“dual-use tools”).
- Cambios legítimos en software no reconocidos por el motor.
- Actualizaciones defectuosas de firmas.

**Falsos negativos**

- Malware nuevo o ofuscado que evade firmas.
- Técnicas de living-off-the-land (usar herramientas legítimas).
- Telas de bajo ruido: actividad que no rompe umbrales.
- Falta de telemetría (pocos logs, ciegos en hosts).

### 7) Consecuencias operativas y de negocio

- **Coste de los falsos positivos:** tiempo perdido, sobrecarga del SOC, tickets, reducción de confianza en alertas.
- **Coste de los falsos negativos:** brechas no detectadas, robo/exfiltración, ransomware, multas, daño reputacional.
- **Fatiga por alertas (alert fatigue):** analistas ignoran alertas tras muchos FPs → aumenta probabilidad de FNs efectivos.

### 8) Cómo identificar mejor los falsos positivos (buen workflow de triage)

1. **Correlación**: Ver si hay alertas relacionadas (firewall, proxy, endpoint).
2. **Contexto**: revisar proceso padre, usuario, horario y servidor origen.
3. **Reputación**: comprobar IPs, dominios y hashes en threat intel / VirusTotal.
4. **Sandboxing**: ejecutar muestra en entorno aislado y observar comportamiento.
5. **EDR telemetry**: revisar registros de procesos, conexiones, tasa de creación de archivos.
6. **Reproducibilidad**: intentar reproducir la actividad en test controlado.
7. **Contactar vendor**: reportar la muestra al proveedor si hay sospecha de FP.
8. **Documentar y whitelist**: si es legítimo, crear excepción controlada con ticket y expiración.

### 9) Cómo reducir falsos negativos (estrategias defensivas)

- **Defense-in-depth**: combinar red + host + aplicación + detección en la nube.
- **Detección basada en comportamiento** (anomalías) además de firmas.
- **Threat intelligence**: ingestión continua de IOCs y TTPs.
- **Simulaciones (Red Teams / Purple Teams)**: validar detecciones contra técnicas reales.
- **Mejorar telemetría**: más logs, mejor retention, instrumentation.
- **Modelos ML mejor entrenados**: datos representativos, reentrenamiento regular.

### 10) Cómo reducir falsos positivos (práctico)

- Afinar reglas (menos genéricas).
- Enriquecer alertas con contexto (usuario, proceso, asset criticality).
- Implementar SOAR para playbooks que filtren/triage automáticamente.
- Implementar whitelisting/allowlisting para herramientas admin conocidas (con control).
- Piloto de actualizaciones de firmas antes de desplegar globalmente.
- Retroalimentación continua del SOC al equipo que mantiene las reglas.

### 11) Trade-offs y ajuste de umbrales

- Bajar el umbral → menos FNs (mejor recall) pero más FPs (peor precision).
- Subir el umbral → menos FPs pero más FNs.
  Herramientas: **ROC curve** (TPR vs FPR) y **Precision-Recall curve** (mejor cuando hay desbalance de clases).
  Decisión: fijar umbrales según **coste relativo** de FN vs FP en tu organización (ej.: para sistemas críticos priorizas minimizar FNs).

### 12) Checklist operativo para SOC (acción inmediata)

- Medir **precision y recall** de reglas críticas periódicamente.
- Mantener métricas de **volumen de alertas** y % de falsos positivos detectados.
- Implementar **playbooks SOAR** para triage automático.
- Mantener un proceso claro para **reportar FPs** a vendors y documentar excepciones.
- Realizar **purple teaming** trimestralmente en assets críticos.

### 13) Resumen ejecutivo (3 frases)

- **Falso positivo** = alarma donde no hay amenaza → ruido y coste operativo.
- **Falso negativo** = amenaza no detectada → riesgo directo y potencialmente grave.
- La clave es medir (precision/recall), entender el _coste_ de cada error y ajustar tecnología/procesos para minimizar el impacto empresarial.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**                                  | **Siguiente 4**                             |
| ------------------ | -------------------------------------------- | ------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_2_False_Negative_False_Positive.md) | [⏩](./8_4_Basics_of_Threat_Intel_OSINT.md) |
