| **Inicio**         | **atrás 1**                          | **Siguiente 3**                                    |
| ------------------ | ------------------------------------ | -------------------------------------------------- |
| [🏠](../README.md) | [⏪](./1_1_Fundamental_IT_Skills.md) | [⏩](./1_3_Connection_Types_and_their_function.md) |

---

## **Índice**

| Temario                                                                                              |
| ---------------------------------------------------------------------------------------------------- |
| [4. ¿Qué es el hardware de una computadora?](#4-qué-es-el-hardware-de-una-computadora)               |
| [5. Componentes de computadora para principiantes](#5-componentes-de-computadora-para-principiantes) |

---

# **Componentes de hardware de computadora**

## **4. ¿Qué es el hardware de una computadora?**

### ¿Cuál es la diferencia entre hardware y software de computadora?

- **Hardware** = las partes **físicas** de una computadora (puedes tocarlas).
- **Software** = los **programas y datos** que hacen que el hardware haga cosas (no los puedes tocar).

  Piensa: el hardware es el cuerpo, el software es la “mente” o las instrucciones.

#### ¿Qué es el _hardware_?

**Definición:** componentes físicos que forman la computadora o dispositivo.

**Características:** tangible, se desgasta con el tiempo, tiene versión física (modelo), necesita electricidad.

**Tipos y ejemplos fáciles:**

- **CPU (procesador):** “cerebro” que ejecuta instrucciones.
- **RAM (memoria):** memoria temporal para procesos en ejecución.
- **Almacenamiento (SSD/HDD):** guarda archivos y programas.
- **Placa madre (motherboard):** conecta todo.
- **GPU:** procesa gráficos (juegos, render).
- **Dispositivos de entrada/salida:** teclado, ratón, monitor, impresora.
- **Fuente de poder, ventiladores, cables, router:** soporte físico y conectividad.

**Ejemplo:** si pulsas una tecla, el teclado (hardware) envía una señal física al computador.

#### ¿Qué es el _software_?

**Definición:** programas, aplicaciones y datos que indican al hardware qué hacer.

**Características:** intangible, se actualiza con parches/versions, puede contener bugs, depende del hardware para ejecutarse.

**Tipos y ejemplos:**

- **Sistema operativo (system software):** Windows, Linux, macOS — administra hardware y recursos.
- **Aplicaciones (application software):** Chrome, Word, Spotify — resuelven tareas del usuario.
- **Drivers:** programas que permiten que el sistema operativo use un componente (ej.: driver de la tarjeta gráfica).
- **Firmware:** software embebido dentro de hardware (ej.: BIOS/UEFI de la placa madre, firmware del router). Es “software” pero muy cerca del hardware.
- **Scripts/utilidades:** pequeños programas que automatizan tareas (por ejemplo, un script que renombra archivos).

**Ejemplo :** abrir Word (software) hace que la CPU y la RAM trabajen para mostrar el documento en el monitor (hardware).

#### Cómo trabajan juntos — ejemplo paso a paso (arranque)

1. Enciendes el computador → la **fuente de poder** entrega energía (hardware).
2. El **firmware** (BIOS/UEFI) inicia y comprueba componentes (POST).
3. El firmware carga el **bootloader** que inicia el **sistema operativo** (software).
4. El sistema operativo carga **drivers** para usar el teclado, disco, tarjeta de red (software que controla hardware).
5. Finalmente abres una **aplicación** (p. ej. navegador): el OS le asigna memoria en la **RAM** y la **CPU** ejecuta sus instrucciones.

#### Analogías para entenderlo mejor

- **Coche:** hardware = el coche (motor, ruedas), software = el conductor + GPS + instrucciones de mantenimiento.
- **Cocina:** hardware = la cocina y utensilios; software = la receta y quien la sigue.

#### ¿Cómo distinguirlos cuando algo falla? (ejemplos prácticos)

- **El PC no enciende (sin luces):** probablemente **hardware** (fuente, cable, placa).
- **Enciende pero no aparece imagen:** puede ser cable/monitor (**hardware**) o driver de la GPU (**software**).
- **PC muy lento con muchas apps:** suele ser software (procesos que consumen CPU/RAM) **o** poca RAM/SSD lento (hardware).
- **Pantalla azul o errores del sistema:** frecuentemente **drivers** defectuosos (software) o problemas de memoria/placa (hardware).

  **Consejo práctico:** pruebas simples ayudan a aislar: cambiar cable o monitor (hardware), iniciar en modo seguro (software), revisar el Administrador de dispositivos/Registros del sistema.

#### Mantenimiento y mejoras

- **Software:** actualizaciones del sistema operativo, parches de seguridad, actualizar drivers y aplicaciones, limpiar bloatware.
- **Hardware:** limpiar polvo, revisar ventilación, cambiar batería CMOS, actualizar RAM/SSD o reemplazar disco/ventilador si falla.
- **Backup:** siempre copia tus datos antes de cambios importantes (formateo, reemplazo de disco).

#### Punto interesante: límites borrosos

- **Firmware** es software pero viene “pegado” al hardware.
- **Virtualización:** software puede “emular” hardware (una máquina virtual actúa como un computador dentro de otro), así que a veces el software simula partes físicas.

### ¿Qué es una **placa base** (motherboard)?

Es la **tarjeta principal** dentro de la computadora.

- Conecta y comunica todos los componentes: CPU, RAM, GPU, discos, fuente de poder, etc.
- Tiene ranuras (slots) y puertos (USB, HDMI, Ethernet).
- Piensa en ella como el **“esqueleto y sistema nervioso”** del PC.

**Ejemplo:** si la placa base es como una ciudad, las calles son los circuitos que permiten que la información viaje entre casas (CPU, RAM, GPU).

![motherboard](/img/1_Fundamental_IT_Skills/placa_base.jpg "motherboard")

### ¿Qué es una **unidad central de procesamiento (CPU)**?

Es el **cerebro del computador**.

- Ejecuta instrucciones de los programas (sumas, comparaciones, mover datos).
- Velocidad medida en **GHz** (gigahercios).
- Tiene múltiples **núcleos** (cores) que permiten hacer tareas en paralelo.

**Ejemplo:** cuando abres Word, la CPU procesa la orden de mostrar el documento en pantalla.

![CPU](/img/1_Fundamental_IT_Skills/cpu.jpg "CPU")

### ¿Qué es la **memoria RAM (Random Access Memory)?**

Es la **memoria temporal** donde el sistema guarda lo que necesita en este momento.

- Es muy rápida pero **volátil**: al apagar la PC se borra.
- Cuanta más RAM tienes, más programas puedes abrir a la vez sin que la PC se ponga lenta.

**Ejemplo:** al tener 8 GB de RAM puedes abrir 10 pestañas de Chrome; con 4 GB, quizá solo 3–4 sin que se congele.

![RAM](/img/1_Fundamental_IT_Skills/ram.avif "RAM")

### Diferencia entre **SSD** y **HDD**

Son los dispositivos de **almacenamiento** (donde guardas el sistema operativo, fotos, videos).

- **HDD (Hard Disk Drive):**

  - Tiene discos magnéticos que giran y un cabezal que lee/escribe.
  - Más barato, más capacidad.
  - Más lento y frágil porque tiene partes mecánicas.
  - Ejemplo: tarda 1–2 minutos en iniciar Windows.

- **SSD (Solid State Drive):**

  - No tiene partes mecánicas, usa chips de memoria flash.
  - Mucho más rápido y resistente.
  - Más caro por GB, pero mejora el rendimiento notablemente.
  - Ejemplo: Windows inicia en 10–15 segundos.

**Analogía:** un HDD es como un tocadiscos que gira, un SSD como una memoria USB gigante.

![SSD y HDD](/img/1_Fundamental_IT_Skills/SSD_HDD.png "SSD y HDD")

### ¿Qué es una **GPU (Unidad de Procesamiento Gráfico)?**

Es el chip especializado en **gráficos e imágenes**.

- Inicialmente servía para mostrar videos y juegos.
- Hoy también se usa en inteligencia artificial, minería de criptomonedas, cálculos 3D, diseño, etc.
- Puede estar integrada en la CPU (gráficos básicos) o ser una tarjeta aparte (NVIDIA, AMD).

**Ejemplo:** en un juego la GPU dibuja los escenarios, luces y movimientos en tiempo real.

![GPU](/img/1_Fundamental_IT_Skills/gpu.jpg "GPU")

### ¿Qué es una **fuente de poder (PSU – Power Supply Unit)?**

Es el componente que **convierte la electricidad** de la pared (220V o 110V) en voltajes más pequeños y seguros que usan los demás componentes.

- Proporciona energía estable a la CPU, RAM, GPU, discos, ventiladores, etc.
- La potencia se mide en **watts** (ej. 500W, 750W).
- Si la PSU falla, el PC puede apagarse de golpe o ni encender.

**Ejemplo:** es como el “corazón” que bombea electricidad a todo el sistema.

![PSU](/img/1_Fundamental_IT_Skills/psu.jpg "PSU")

### ¿Cómo puedes ver qué hardware tiene tu computadora?

#### 📌 En **Windows**

1. Presiona **Win + R**, escribe `dxdiag` → verás CPU, RAM, GPU, placa base (fabricante).
2. O usa **Administrador de tareas** (Ctrl + Shift + Esc) → pestaña **Rendimiento**.
3. En **Configuración → Sistema → Acerca de**, aparecen procesador, RAM y versión de Windows.
4. Para ver discos: abre **Explorador → clic derecho en disco C → Propiedades**.

#### 📌 En **Linux**

- Usa comandos:

  - `lscpu` → info del procesador.
  - `lsblk` → discos.
  - `free -h` → memoria RAM.
  - `lspci | grep VGA` → GPU.

#### 📌 En **macOS**

- Clic en ** → Acerca de esta Mac** → muestra CPU, RAM, GPU y almacenamiento.

✅ **Resumen final rápido:**

- **Placa base:** conecta todo.
- **CPU:** cerebro que procesa instrucciones.
- **RAM:** memoria temporal rápida.
- **HDD vs SSD:** ambos guardan datos; SSD = más rápido, HDD = más barato.
- **GPU:** procesa gráficos y cálculos paralelos.
- **PSU:** da electricidad a todos.
- **Ver hardware:** Windows (dxdiag / administrador de tareas), Linux (comandos), macOS (Acerca de esta Mac).

---

[🔼](#índice)

---

## **5. Componentes de computadora para principiantes**

Una computadora está formada por **componentes físicos (hardware)** que trabajan juntos para ejecutar **software**. Los componentes principales son: **placa base, CPU, RAM, almacenamiento (SSD/HDD), GPU, fuente de poder, caja, refrigeración y periféricos**.

### Componentes esenciales

#### Placa base (motherboard)

- **Qué es / hace:** La tarjeta principal que conecta todo: CPU, RAM, discos, GPU y puertos.
- **Ejemplo:** Es la “ciudad” y las conexiones son las “calles” por las que viaja la información.
- **Consejo:** Verifica el _socket_ (compatibilidad con la CPU) y el factor de forma (ATX, micro-ATX) antes de comprar.

#### Unidad Central de Procesamiento (CPU)

- **Qué es / hace:** El “cerebro” que ejecuta instrucciones (programas). Mide velocidad en GHz y tiene núcleos (cores).
- **Ejemplo:** Al abrir el navegador, la CPU procesa lo que la aplicación necesita mostrar.
- **Consejo:** Para tareas diarias, una CPU de 4–6 núcleos está bien; para edición o render, busca más núcleos.

#### Memoria RAM

- **Qué es / hace:** Memoria temporal y super-rápida donde el sistema guarda lo que está en uso ahora (programas y datos activos).
- **Ejemplo:** Abrir muchas pestañas consume RAM; si se acaba, el sistema irá lento.
- **Consejo:** 8 GB mínimo para uso básico; 16 GB recomendado para multitarea y juegos; 32+ GB para edición/VMs.

#### Almacenamiento: HDD vs SSD (y NVMe)

- **HDD:** disco mecánico, barato y con mucha capacidad; más lento.
- **SSD (SATA):** memoria flash, rápido; mejora tiempos de arranque y carga.
- **NVMe (M.2):** SSD aún más rápido que usa PCIe; ideal para cargas intensas.
- **Ejemplo:** Windows en HDD tarda más en iniciar que en un SSD.
- **Consejo:** Si puedes, pon el sistema operativo en SSD/NVMe y usa HDD para archivos grandes.

#### Unidad de Procesamiento Gráfico (GPU)

- **Qué es / hace:** Procesador especializado en imágenes y cálculos paralelos (gráficos, video, IA).
- **Ejemplo:** En juegos la GPU dibuja los escenarios; en edición de video acelera el render.
- **Consejo:** Para ofimática basta GPU integrada; para juegos/edición busca GPU dedicada (NVIDIA/AMD).

#### Fuente de Poder (PSU)

- **Qué es / hace:** Convierte la energía de la pared en voltajes para los componentes.
- **Ejemplo:** Si la PSU no da suficiente potencia, la PC puede apagarse cuando la GPU demanda energía.
- **Consejo:** Compra PSU de buena marca y con potencia suficiente (+20–30% de margen) y certificación 80+.

#### Caja / Chasis

- **Qué es / hace:** Estructura que alberga componentes y facilita flujo de aire.
- **Ejemplo:** Una caja pequeña puede limitar qué GPU o cooler puedes instalar.
- **Consejo:** Revisa compatibilidad de tamaño (GPU length, cooler height).

#### Refrigeración (coolers, ventiladores, pasta térmica)

- **Qué es / hace:** Mantiene las temperaturas seguras para rendimiento estable.
- **Ejemplo:** CPU sin buen cooler alcanzará altas temperaturas y reducirá su velocidad (thermal throttling).
- **Consejo:** Añade ventiladores bien colocados (entrada frontal / salida trasera/techo) y cambia la pasta térmica si es antigua.

#### Almacenamiento secundario y puertos (SATA, M.2, USB)

- **Qué es / hace:** Cables y ranuras que permiten conectar discos, unidades ópticas, y periféricos.
- **Ejemplo:** Un SSD M.2 va directo a la placa (sin cables) y ocupa menos espacio.
- **Consejo:** Verifica cuántos puertos M.2 / SATA trae tu placa si planeas varios discos.

#### Periféricos (monitor, teclado, ratón, altavoces)

- **Qué es / hace:** Dispositivos para interactuar con la PC y ver resultados.
- **Ejemplo:** Un monitor con mayor tasa de refresco (Hz) mejora la experiencia en juegos.
- **Consejo:** Para productividad prioriza buena resolución (1080p+); para gaming añade tasa de refresco alta.

### Cómo trabajan juntos (arranque simplificado)

1. Enciendes → la **PSU** da energía.
2. **Firmware (BIOS/UEFI)** en la placa hace checks básicos.
3. BIOS carga el **bootloader** desde el disco.
4. El **sistema operativo** (software) arranca y usa **RAM** y **CPU** para ejecutar aplicaciones.
5. Si necesitas gráficos complejos, la **GPU** hace la mayor parte del trabajo gráfico.

### Qué componentes afectan más según el uso (ejemplos prácticos)

- **Ofimática / navegar / multimedia:** CPU modest, 8–16 GB RAM, SSD = rapidez percibida.
- **Gaming:** GPU dedicada (primaria), CPU decente, 16 GB RAM, SSD para tiempos de carga.
- **Edición de video / render 3D:** CPU con muchos núcleos, mucha RAM (32+ GB), SSD/NVMe, GPU potente.
- **Trabajo con datos / ML:** GPU (para modelos) y/o CPU fuerte + RAM y almacenamiento rápido.

### Mejores actualizaciones para principiantes (mayor impacto)

1. **Instalar un SSD** si aún usas HDD (arranque y respuestas mucho más rápidos).
2. **Aumentar RAM** (si tienes <8 GB).
3. **Actualizar GPU** si quieres jugar o hacer edición.
4. **Cambiar PSU** sólo si es débil o antigua antes de cambiar GPU.

### Mantenimiento básico y seguridad

- Apaga y desconecta antes de abrir la caja.
- Evita electricidad estática: descarga tocando metal.
- Limpia polvo con aire comprimido (ventiladores y radiadores).
- Cambia pasta térmica cada 2–4 años si notas temperaturas altas.
- Mantén drivers y BIOS/UEFI actualizados (con precaución y leyendo instrucciones).
- Haz backups antes de cambios de hardware o formateos.

### Cómo comprobar el hardware que tienes (rápido)

- **Windows:** Administrador de tareas → Rendimiento; `dxdiag`; Configuración → Acerca de.
- **macOS:**  → Acerca de esta Mac.
- **Linux:** `lscpu`, `lsblk`, `free -h`, `lspci | grep -i vga`.

### Problemas comunes y primeras pruebas

- **PC no enciende:** comprobar cables, interruptor de PSU, conexión de botón frontal, prueba otro enchufe.
- **Enciende pero sin imagen:** revisar conexión del monitor, GPU en su slot, probar con otra salida/monitor.
- **Muy lento:** mirar uso de CPU/RAM/disk en el administrador de tareas; SSD suele mejorar esto.
- **Apagados inesperados / reinicios:** revisar temperaturas y PSU.

### Glosario corto (para recordar)

- **Socket:** conector de la placa para la CPU.
- **Form factor:** tamaño/estándar de la placa (ATX, micro-ATX).
- **SATA / M.2 / NVMe:** tipos de conexión para discos.
- **Thermal throttling:** reducción de velocidad por calor.
- **BIOS / UEFI:** firmware inicial de la placa.

### Mini-proyectos para aprender (prácticos)

1. Abrir la caja y **identificar** cada componente (hacer fotos y etiquetarlas).
2. **Instalar un SSD** y clonar tu sistema (o instalar limpio) — sensación inmediata de mejora.
3. Monitorizar temperaturas con una herramienta (HWMonitor/`sensors`) mientras ejecutas una tarea pesada.
4. Cambiar RAM o agregar otra memoria y verificar funcionamiento.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**                          | **Siguiente 3**                                    |
| ------------------ | ------------------------------------ | -------------------------------------------------- |
| [🏠](../README.md) | [⏪](./1_1_Fundamental_IT_Skills.md) | [⏩](./1_3_Connection_Types_and_their_function.md) |
