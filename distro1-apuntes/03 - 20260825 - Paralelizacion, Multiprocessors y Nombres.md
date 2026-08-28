## Clase 3 - Paralelizacion, Multiprocessors y Nombres

### Paralelización de tareas

- Reducir tiempo de cómputo **(latencia)**
- Incrementar cantidad de tareas que se pueden hacer en paralelo **(throughput)**
- Reducir energía consumida al realizar tareas

**Camino crítico**: Máxima longitud de tareas secuenciales a computar

#### Ley de Amdahl

- Una parte del trabajo es serial y otra paralela.
- Ganar tiempo en la parte serial para que el camino crítico sea menos costoso.
- Utilizando $P$ unidades de cómputo, el tiempo de trabajo puede reducirse.
- *Speedup* máximo: acotado por la **fracción de tiempo que no puede ser paralelizable**.

#### Ley de Gustafson

- Aumentar el paralelismo puede permitir la modificación del problema original para **ejecutar más trabajo**, en lugar de usarlo para achicar el tiempo de ejecución.

#### Modelo Work-Span

- Modelo más cercano a la realidad para estimar optimizaciones que el usado por Amdahl.
- Analizar un algoritmo en **caja blanca**: ver el contenido del código.
- *Paralelismo imperfecto*: no todo el trabajo paralelizable se puede ejecutar al mismo tiempo.
- *Greedy Scheduling*: si hay proceso disponible, se ejecuta.
- Se plantean cotas inferiores y superiores para el *Speedup*

#### Estrategias de Particionamiento

- Descomposición funcional
- Particionamiento de Datos

#### Patrones de Procesamiento

- Basados en algoritmos
	- No tan abstracos como los *patrones de diseño*
	- No incluyen detalles de implementación
	- Agnósticos a lenguajes de programación
- Patrones deben poder incluir otros patrones (*nesting*)
- Herramientas básicas de trabajo también en *multi-computing*

### Multiprocessors

#### Taxonomía de Flynn

Clasificación de sistemas según la **cardinalidad de flujos de instrucciones** (procesadores) y **flujos de datos** (memoria).
- **SISD** (Single Instruction Single Data): modelo estándar de **un procesador sin paralelismo**.
- **SIMD** (Single Instruction Multiple Data): se pueden sacar varios datos a la vez y aplicarle la misma instrucción.
- **MISD** (Multiple Instruction Single Data): no son usuales (computación redundante). Aunque se haga lo mismo para el mismo dato, sirve por si una unidad falla, y tener alternativas funcionales.
- **MIMD** (Multiple Instruction Multiple Data): dividida en dos modelos.
	- *Multiprocessors*: con memoria / clock compartido
	- *Multicomputers*: sin memoria ni clock compartidos

#### Multiprocessors (Memoria Compartida)

- *Sytmettric Multiprocessing*: todos los procesos en el mismo *bus*, compartiendo unidades de memoria y otros I/O.
- *Asymetric Multiprocessing*: hay un **puente** entre distintos buses, y los procesos viven en un bus determinado. Quizás un proceso no tiene permisos para pedir recursos de otro bus.

#### Procesadores NUMA

**Non Uniform Memory Access (NUMA)**:
- Cada CPU controla un bloque de memoria local como su *home agent*.
- Mayor ancho de banda si se respeta acceso a memoria local.
- Hoy en día ofrecido en Cloud, luego de años de desuso.

**Uniform Memory Access (UMA)**:
- Tiempo de acceso a memoria **idéntico para todos los procesadores**.
- Ancho de banda compartido por todos.
- *Performance* balanceada 

#### Multicomputers

Cada **computadora**:
- Propia memoria local
- Puede fallar de forma independiente
- No poseen reloj central de ejecución de instrucciones

Se requiere **comunicación entre computadoras**: Networking (LAN, MAN, WAN)

### Nombres y Direccionamiento

#### Nombres (Naming)

- Identificar **unívocamente a una entidad** dentro de un sistema
- Deben **describir** a la entidad
- **Abstraen al recurso** de las propiedades que lo atan al sistema (direcciones de red, lugares geográficos)

#### Direccionamiento (Addressing)

- Mapeo entre un nombre y una dirección
- Dirección de una entidad puede cambiar, no así el nombre
- Dirección puede ser reutilizada

#### Ejemplos entre names y addresses

- **IP Address (name)** --- **Ethernet Address (address)**
- **Domain Name (name)** --- **IP Address (address)**
	- Mapeo de un servicio/nodo u otra entidad a una dirección IP
	- Traducción a través de protocolo DNS
- **Server (name)** --- **Endpoint (host:port)**
	- Mapeo del nombre de un servicio a alguna instancia
	- Service Discovery: cuáles son los addresses disponibles actualmente, de todos los servicios que están corriendo ahora mismo.

### Documentación

#### Diseño y Documentación

- **Evolutivo**:
	- Adaptarse rápido, tomar feedback y aportar valor iterativamente
	- No buscar el entendimiento del todo y ni demorar la arquitectura
- Necesario para **coordinación, coherencia y cohesión**:
	- Sin un diseño preliminar, probablemente jamás haya diseño.

#### Modelos de documentación

##### Vistas de Arquitectura 4+1

- Punto de vista **central** (círculo): **escenarios, casos de uso**
- Cuatro rectángulos:
	- Vista **Lógica**: clases, estados, módulos
	- Vista de **Desarrollo**: componentes, paquetes, librerías
	- Vista de **Procesos**: secuencia, actividad
	- Vista **Física**: despliegue, robustez

##### C4 Model

- Contexto
- Containers
- Components
- Code