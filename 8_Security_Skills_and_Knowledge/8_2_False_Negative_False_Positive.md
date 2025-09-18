| **Inicio**         | **atrás 1**                                         | **Siguiente 3**                            |
| ------------------ | --------------------------------------------------- | ------------------------------------------ |
| [🏠](../README.md) | [⏪](./8_1_Blue_Team_vs_Red_Team_vs_Purple_Team.md) | [⏩](./8_3_True_Negative_True_Positive.md) |

---

## **Índice**

| Temario                                                                                                        |
| -------------------------------------------------------------------------------------------------------------- |
| [201. Diferencia entre falso positivo y falso negativo](#201-diferencia-entre-falso-positivo-y-falso-negativo) |
| [202. ¿Qué es un virus falso positivo?](#202-qué-es-un-virus-falso-positivo)                                   |
| [203. False positives and false negatives](#203-false-positives-and-false-negatives)                           |

# **False Negative / False Positive**

## **201. Diferencia entre falso positivo y falso negativo**

![False Negative / False Positive](/img/8_Security_Skills_and_Knowledge/False_Negative_False_Positive.jpg "False Negative / False Positive")

### ✅ Falso Positivo

Un **falso positivo** ocurre cuando el sistema **detecta una amenaza o condición que en realidad no existe**.

📌 Ejemplo en ciberseguridad:

- Un antivirus bloquea un archivo legítimo pensando que es malware.
- Un SIEM genera una alerta de ataque, pero solo era tráfico normal de red.

👉 Problema: genera **alertas innecesarias** que pueden causar pérdida de tiempo y recursos.

### ❌ Falso Negativo

Un **falso negativo** ocurre cuando el sistema **no detecta una amenaza real** (pasa desapercibida).

📌 Ejemplo en ciberseguridad:

- Un malware entra en la red pero el antivirus no lo identifica.
- Un ataque de phishing llega al correo y no es marcado como sospechoso.

👉 Problema: es **más peligroso** porque permite que el ataque ocurra sin que nadie lo note.

### 🔄 Diferencia entre Falso Positivo y Falso Negativo

| Aspecto          | Falso Positivo                                        | Falso Negativo                          |
| ---------------- | ----------------------------------------------------- | --------------------------------------- |
| Definición       | Alerta incorrecta: detecta algo que no es una amenaza | No alerta: deja pasar una amenaza real  |
| Efecto           | Sobrecarga de alertas, pérdida de tiempo              | Brechas de seguridad, daños potenciales |
| Ejemplo          | Antivirus marca un programa seguro como virus         | Antivirus no detecta un troyano real    |
| Riesgo principal | Fatiga de alertas (ignorar amenazas reales)           | Compromiso de sistemas sin detección    |

### 📌 Resumen

- **Falso positivo**: el sistema ve un ataque que no existe. → Molesto, pero no crítico.
- **Falso negativo**: el sistema no ve un ataque real. → Muy peligroso.

👉 En seguridad, **los falsos negativos son más graves**, pero demasiados falsos positivos también afectan porque el equipo puede **ignorar alertas reales** (fatiga de alertas).

---

[🔼](#índice)

---

## **202. ¿Qué es un virus falso positivo?**

Un **falso positivo** en el contexto antivirus ocurre cuando un motor de detección marca **un archivo, programa, script o actividad legítima** como si fuera malware (virus, troyano, ransomware, etc.), pero en realidad **no hay infección ni intención maliciosa**. Es decir: el sistema “alarma” cuando no debería.

### ¿Por qué ocurren los falsos positivos? — Causas comunes

- **Firmas demasiado genéricas:** una regla de firmas que coincide con código común provoca detecciones en software legítimo.
- **Heurísticas y detección basada en comportamiento:** actividades sospechosas (p. ej. modificación masiva de archivos, ejecución de PowerShell codificado) pueden parecer maliciosas aunque sean administrativas.
- **Ofuscación / empaquetado (packers):** binarios comprimidos u ofuscados (para proteger IP) suelen parecer malware porque los atacantes usan técnicas similares.
- **Herramientas legítimas “dual-use”:** utilidades de administración (PsExec, herramientas de debugging, Sysinternals) pueden usarse tanto para administrar como para atacar. Algunos AVs las marcan.
- **Actualizaciones defectuosas de la base de firmas:** una actualización de definiciones con un error puede generar detecciones masivas equivocadas.
- **Modelos ML sesgados:** detectores con machine learning pueden aprender patrones que incluyen software legítimo.
- **Scripts/macros corporativos:** macros de Office o scripts PowerShell diseñados para automatizar tareas internas pueden disparar alertas.
- **Reglas YARA o reglas personalizadas mal calibradas** en el SOC.

### Ejemplos concretos (reales/hipotéticos) — para entender mejor

1. **Instalador nuevo de software interno**: un .exe generado por tu equipo de desarrollo (no firmado) es subido al servidor y al instalarlo, el antivirus lo marca como “Trojan.Gen” por contener cierto patrón de bytes similar a una firma conocida.
2. **Script PowerShell de administración**: un script que descarga parches desde un repositorio y ejecuta tareas se detecta como “Downloader” por usar técnicas de descarga/execución dinámica.
3. **Herramienta de diagnóstico**: una utilidad de Sysinternals o una build de PuTTY distribuida internamente es identificada como “hacktool” porque contiene APIs que suelen usar los atacantes.
4. **Macro de Excel legítima**: plantilla conteniendo macros para generar reportes será marcada como “macro malware” si la heurística bloquea macros por defecto.
5. **Actualización de firmas defectuosa**: tras un update del motor AV, cientos de PCs reportan que Chrome o Outlook son maliciosos (caso típico de update problemático).

> Nota: los ejemplos anteriores son de uso ilustrativo. En incidentes reales conviene confirmar con hashes y análisis adicionales.

### ¿Qué riesgos ocasiona un falso positivo?

- **Interrupción del negocio**: aplicaciones críticas bloqueadas o removidas.
- **Pérdida de productividad**: usuarios y equipos de soporte saturados.
- **Decisiones peligrosas**: eliminación masiva de archivos legítimos si se confía ciegamente en la alerta.
- **Fatiga de alertas**: el SOC puede empezar a ignorar alertas por demasiados falsos positivos.
- **Impacto reputacional**: clientes/usuarios afectados por fallas.

### Cómo investigar un posible falso positivo — paso a paso (checklist)

1. **Aislar / poner en cuarentena** (si existe riesgo): no ejecutar el archivo en producción.
2. **Obtener el hash del archivo**

   - Windows PowerShell: `Get-FileHash -Algorithm SHA256 C:\ruta\archivo.exe`
   - Linux/macOS: `sha256sum archivo.bin` o `shasum -a 256 archivo.bin`

3. **Buscar el hash en servicios multi-AV** (VirusTotal u otros) para ver cuántos motores lo detectan y con qué nombre.
4. **Comprobar la firma digital** (si aplica)

   - PowerShell: `Get-AuthenticodeSignature C:\ruta\archivo.exe`

5. **Analizar comportamiento en sandbox** (aislado): ejecución controlada para ver llamadas a red, creación/alteración de archivos, procesos hijos.
6. **Revisar contexto y telemetría EDR**: procesos padres, comandos, IPs de conexión, persistencia.
7. **Reproducir en entorno de pruebas** si se necesita más evidencia.
8. **Comparar con builds conocidos**: ¿es una build reciente? ¿cambió el empaquetado?
9. **Contactar al vendor del AV**: enviar muestra + hashes + logs + pasos para reproducir (muchos vendors tienen formularios de “false positive submission”).
10. **Si urgente y seguro**: crear excepción temporal (whitelist) en EDR/AV mientras se valida con vendor — **documentar y auditar** la excepción.
11. **Comunicar** a usuarios/claves afectadas y registrar lecciones aprendidas.

### Ejemplo de flujo de resolución (mini-caso)

Detección: el SOC reporta que el instalador del ERP fue puesto en cuarentena.

Triage: se calcula SHA256 y se consulta VirusTotal → 2/70 motores lo marcan (posible FP).

Sandbox: ejecución en entorno controlado muestra **ninguna** actividad de exfiltración ni persistencia.

Firma: el instalador no está firmado (riesgo mayor de FP).

Acción: se abre ticket con el vendor AV y se agrega una excepción temporal para los servidores de actualización si el riesgo es alto.

Resolución: vendor confirma falso positivo tras análisis y publica actualización; se elimina la excepción y se documenta el incidente.

### Buenas prácticas para **evitar** falsos positivos (desarrollo y operaciones)

Para desarrolladores y equipos IT:

- **Firmar digitalmente** binarios e instaladores con certificados válidos (code signing).
- **Evitar empaquetadores/obfuscators** innecesarios en builds de producción; si se usan, documentarlo.
- **Incluir información clara en instaladores** (publisher, checksum, URL oficial).
- **Probar builds contra motores AV** (multi-engine scan) en la pipeline CI antes de release.
- **Distribuir builds desde repositorios confiables** (HTTPS, repos privados).
- **Mantener una política de whitelisting/allowlisting** para herramientas internas; catalogar herramientas admin.
- **Tener procedimientos de rollback y comunicación** para cuando un update de firmas cause problemas.
- **Enviar muestras a vendors anticipadamente** si se va a distribuir software inusual.

### Recomendaciones para SOC/administradores

- No borrar archivos masivamente: preferir cuarentena y análisis.
- Mantener un canal/documentación para reportar FPs a vendors y seguimiento.
- Automatizar comprobaciones de hashes y búsqueda en servicios de reputación.
- Configurar exclusiones con control y auditoría (excepciones temporales con ticket).
- Implementar pruebas de actualizaciones de firmas en un entorno piloto antes de desplegar globalmente.

### Resumen (en 3 frases)

Un **virus falso positivo** es cuando una solución de seguridad marca como malicioso algo que es legítimo. Sucede por heurísticas, firmas genéricas, ofuscación o errores de actualización y puede causar interrupciones importantes si no se gestiona bien. La respuesta correcta combina **triage técnico** (hashes, sandbox, EDR) + **comunicación con el vendor** + medidas de mitigación controladas (cuarentena o whitelist temporal).

---

[🔼](#índice)

---

## **203. False positives and false negatives**

### 1) Definiciones sencillas

- **Falso positivo (FP):** el sistema **avisa** que hay un problema (p. ej. malware, spam, enfermedad), pero **en realidad no lo hay**.
- **Falso negativo (FN):** el sistema **no avisa** aunque **sí existe** el problema (p. ej. malware pasa desapercibido, enfermedad no detectada).

En resumen:

- FP = alarma falsa → perturbación/ruido.
- FN = fallo silencioso → riesgo real.

### 2) Confusion matrix (matriz de confusión)

|                   |   Predicho Positivo |   Predicho Negativo |
| ----------------- | ------------------: | ------------------: |
| **Real Positivo** |  TP (True Positive) | FN (False Negative) |
| **Real Negativo** | FP (False Positive) |  TN (True Negative) |

Fórmulas útiles:

- Precision (precisión) = TP / (TP + FP)
- Recall (sensibilidad) = TP / (TP + FN) (también llamado _sensitivity_)
- Specificity (especificidad) = TN / (TN + FP)
- FPR (tasa de falsos positivos) = FP / (FP + TN) = 1 − specificity
- FNR (tasa de falsos negativos) = FN / (FN + TP) = 1 − recall
- Accuracy = (TP + TN) / Total

### 3) Ejemplo numérico — paso a paso (cálculos **digit-by-digit**)

Supongamos un detector que clasifica 1000 muestras:

- Total = 1000
- Reales positivos = 150
- Reales negativos = 850
- Predichos positivos = 180
- Verdaderos positivos (TP) = 120

Calculamos FP, FN, TN:

- **FN** = reales positivos − TP = 150 − 120 = 30.
- **FP** = predichos positivos − TP = 180 − 120 = 60.
- **TN** = reales negativos − FP = 850 − 60 = 790.

Comprobación totales:

TP + FN = 120 + 30 = 150 (ok).

FP + TN = 60 + 790 = 850 (ok).

TP + TN + FP + FN = 120 + 790 + 60 + 30 = 1000 (ok).

Ahora métricas:

**Accuracy** = (TP + TN) / Total
\= (120 + 790) / 1000
\= 910 / 1000
\= 0.91 → **91.00%**

**Precision** = TP / (TP + FP)
\= 120 / (120 + 60)
\= 120 / 180.

Divido 120 entre 180: puedo simplificar dividiendo num. y den. por 60 → 120/60 = 2, 180/60 = 3 → 2/3.
2/3 = 0.666666... → redondeando al 2.º decimal → **66.67%**

**Recall (Sensibilidad)** = TP / (TP + FN)
\= 120 / (120 + 30)
\= 120 / 150.

Divido 120 entre 150: simplifico dividiendo por 30 → 120/30 = 4, 150/30 = 5 → 4/5 = 0.8 → **80.00%**

**Specificity (Especificidad)** = TN / (TN + FP)
\= 790 / (790 + 60)
\= 790 / 850.

Simplifico dividiendo por 10 → 79 / 85.

Ahora hago la división larga (79 ÷ 85):

- 85 × 0 = 0 → resto 79 → decimales.
- 790 ÷ 85 = 9 (porque 85×9=765); resto 790−765 = 25 → 0.9...
- 250 ÷ 85 = 2 (2×85=170); resto 250−170 = 80 → 0.92...
- 800 ÷ 85 = 9 (9×85=765); resto 800−765 = 35 → 0.929...
- 350 ÷ 85 = 4 (4×85=340); resto 10 → 0.9294...
  Continuando → 0.9294117... → redondeando al 2.º decimal → **92.94%**

**FPR (tasa de falsos positivos)** = FP / (FP + TN)
\= 60 / (60 + 790) = 60 / 850.
Divido por 10 → 6/85. División: 6 ÷ 85 = 0.0705882... → **7.06%** (aprox.)

**FNR (tasa de falsos negativos)** = FN / (FN + TP)
\= 30 / (30 + 120) = 30 / 150 = 1/5 = **20.00%**

Relaciones fáciles:

- FPR ≈ **7.06%**, Specificity = **92.94%** (porque Specificity = 1 − FPR).
- FNR = **20%**, Recall = **80%** (porque Recall = 1 − FNR).

### 4) Ejemplos reales por dominio

**Ciberseguridad (IDS / AV)**

- FP: el IDS marca tráfico de backup legítimo como “comando remoto malicioso” → muchas alertas innecesarias → _alert fatigue_.
- FN: un exploit real pasa sin ser detectado → atacante logra persistencia → **alto riesgo**.

**Machine Learning (clasificador de spam)**

- FP: correo legítimo llega a la carpeta spam → pérdida de comunicación importante.
- FN: un correo phishing llega a la bandeja principal → riesgo de compromiso.

**Medicina (test diagnóstico)**

- FP: test detecta enfermedad en sano → ansiedad, pruebas innecesarias.
- FN: test negativo en persona enferma → falta de tratamiento → **consecuencia grave**.

  En medicina se suele priorizar baja FNR (alta sensibilidad) cuando perder un caso es crítico.

**QA / pruebas de software**

- FP: test automático falla por un flaky test → gasto de tiempo investigando bug que no existe.
- FN: un bug real no es detectado → falla en producción.

### 5) Trade-offs y decisiones (¿qué priorizar?)

- **Bajar umbral de detección** → disminuye FNs (mejora recall) pero aumenta FPs (peor precision).
- **Subir umbral** → menos FPs pero más FNs.
  La elección depende del **coste relativo**: perder un caso (FN) puede ser más caro que tratar una falsa alarma (FP), o viceversa. Ejemplos:
- Medicina grave → priorizar **baja FNR** (sensitivity alta).
- Sistemas con capacidad humana limitada (SOC pequeño) → intentar **reducir FPs** para evitar fatiga.

Herramientas de análisis: **ROC curve** (trade-off TPR vs FPR), **Precision-Recall curve** (mejor cuando los positivos son raros).

### 6) Cómo reducir falsos positivos y falsos negativos (práctico)

**Reducir falsos positivos (menos ruido):**

- Afinar reglas / firmas (menos genéricas).
- Enriquecer alertas con contexto (telemetría, reputación IP, proceso padre).
- Whitelist / allowlist para herramientas legítimas (con control y auditoría).
- Calibrar modelos ML, usar validación cruzada y más datos representativos.
- Implementar verificación humana para alertas dudosas (human-in-loop).

**Reducir falsos negativos (no dejar pasar amenazas):**

- Añadir detección basada en comportamiento (EDR/XDR) además de firmas.
- Bajar umbral en escenarios críticos o usar múltiples detectores en paralelo.
- Aumentar diversidad y calidad del set de entrenamiento (en ML).
- Implementar detección en capas (network + host + application).
- Usar threat intelligence y señales externas para enriquecer detección.

**En ML:**

- Rebalancear clases (oversampling/undersampling), penalizar errores en la función de pérdida (cost-sensitive learning).
- Ensembles y stacking suelen mejorar robustez frente a FNs/FPs.

### 7) Recomendaciones prácticas (resumen accionable)

- Define **qué coste tiene un FP vs un FN** en tu contexto.
- Mide siempre **precision & recall** y usa PR-curve para datasets desbalanceados.
- No ignores las alertas por muchos FPs: instrumenta triage automático y escalado.
- Ten procesos para **reportar y corregir falsos positivos** (p. ej. enviar muestras a vendor AV).
- Usa _purple teaming_ (Rojo + Azul) para probar detección realista y reducir FNs.

### 8) Resumen rápido

- **Falso positivo = alarma donde no hay problema** (ruido).
- **Falso negativo = problema que no se detectó** (peligro).
- Prioriza reducir FNs cuando las consecuencias de “no detectar” son graves; prioriza reducir FPs cuando el volumen de alertas impide la operación.
- Métricas clave: **precision, recall, specificity, FPR, FNR** — mide y elige umbrales según coste.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**                                         | **Siguiente 3**                            |
| ------------------ | --------------------------------------------------- | ------------------------------------------ |
| [🏠](../README.md) | [⏪](./8_1_Blue_Team_vs_Red_Team_vs_Purple_Team.md) | [⏩](./8_3_True_Negative_True_Positive.md) |
