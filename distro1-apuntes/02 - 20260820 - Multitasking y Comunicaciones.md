## Clase 2 - Multitasking y Comunicaciones

### Modelos de multiprogramación

- **Multi-threading**: múltiples hilos simultáneos en un mismo proceso, que comparten memoria. 
	- Comparten heap, File Descriptors, Data Segment y Code Segment (read-only).
	- Soporte threading del SO y del Runtime
	- Inter Process Communication (IPC)
	- Sencillo compartir información entre threads, pero ante la falla de uno hay problemas en todo el sistema.
	- Escalabilidad limitada
- **Multi-processing**: distintos procesos con su propia memoria que pueden correr simultáneamente.
	- Comparten Code Segment (read-only) y File Descriptors.
	- IPCs (signals, pipes/FIFOs, queues, shared memory, semáforos, locks, sockets)
	- No es trivial compartir información entre procesos.
	- Los componentes están separados, por lo general son más simples.
	- Más escalable y estable que multi-threading.
- **Multi-computing**: se tienen múltiples máquinas distintas, cada una con un proceso distinto, y se comunican por la red.
	- Ningún recurso compartido
	- Necesidad de implementar mecanismos de sincronización
	- Comunicación por red: limitaciones de ancho de banda, latencia y pérdida de mensajes.
	- Alta escalabilidad y tolerancia a fallos.

### Propiedades de programas concurrentes

**Safety properties**: siempre verdadera
- Exclusión mutua
- Ausencia de deadlocks

**Liveness properties**: eventualmente se vuelven verdaderas
- Ausencia de starvation
- Fairness

### Asegurar estado *safety*

#### Basado en algoritmos

- **Busy-Waiting**: responsable de la mayoría de problemas de performance en sistemas concurrentes.
	- **Spin-lock**: caso más simple de Busy Wait (`while(flag)`)
	- **Algoritmos de espera**: Dekker, Lamport, Peterson 

#### Basado en abstracciones

- **Operaciones atómicas**: Mecanismos provistos por un lenguaje para actualizar variables/objetos **sin utilizar mecanismos de sincronización**
	- **Contadores atómicos** de tipos POD (int, char, double, etc.)
	- **CAS (Compare and Swap)**: Actualizar contenedores de forma segura en ambientes multithreading.

### Mecanismos de sincronización

#### Semáforo

- Variable entera utilizada para acceder a recursos compartidos (e.g. Shared Mem) 
- Definido por los valores que puede adoptar (e.g. S = {0,1,2})
- Operaciones válidas:
	- `signal (P)`: Incrementa el valor de S
	- `wait (V)`: Decrementa el valor de S 
- **Mutex ($S = \{0,1\}$)**: Utilizado para acceder a secciones críticas

#### Monitor

- Agrupa datos compartidos y un conjunto de procedimientos (operaciones).
- Garantiza **exclusión mutua**: solo un hilo/proceso puede ejecutar los procedimientos a la vez.
- Permite la espera mediante **variables de condición**.

##### Variables de Condición

- Ejemplo práctico de monitores
- Adquirir Mutex antes de realizar una operación
- Operaciones válidas:
	- **wait**: bloquea al proceso hasta que otro proceso lo despierte
	- **notify / notify_all**: despierta a ***un proceso*** / ***todos los procesos*** esperando que se cumpla una condición.

#### Barreras

- Esperar a que $N$ procesos estén bloqueados para poder liberarlos todos al mismo tiempo.
- **Rendezvous** (encuentro): un proceso le envía un mensaje a otro indicando que terminó su tarea, para que pueda resumir sus actividades.

### Inter Process Comunication (IPCs)

- Permiten comunicación entre dos o más procesos
- Provistos por el SO (kernel)
- Creación y destrucción exceden la vida del proceso:
	- El usuario es responsable de la vida de los mismos
	- Proceso *Launcher* y *Terminator* para administrar sus vidas
- Usualmente se identifican por nombre
- En Linux se ven como **diferentes tipos de archivos**

![[Mecanismos de sincronizacion - IPCs.png]]

#### Signals

- Existen 31 señales distintas
- Cada proceso decide cuáles handlear
- SIGSTOP y SIGKILL son excepciones: terminan "ya" el proceso, sin importar la actividad.
- Signals estándar:
	- SIGINT y SIGTERM: graceful quit
	- SIGSEGV: problemas de memoria
	- SIGABRT: code assertions

#### Shared Memory

- Mecanismo provisto por el SO (Linux) para **compartir recursos**
- **Abstracción inexistente en threads**: heap entre dos threads de un mismo proceso es compartido, ya no es necesario.
- Su **tamaño** se define al ser creada
- Mutex es necesario solo si dos procesos no pueden acceder a la memoria al mismo tiempo (e.g. shared counter)

#### File Locks

- Bloquear archivos en modo lectura o escritura con su FD
- En lenguaje C: `int flock(int fd, int operation)`
- Tipos:
	- ***Shared Lock (R)***: read-only lock (múltiples permitidos)
	- ***Exclusive Lock (W)***: RW lock (uno solo a la vez)

#### Pipes / FIFOs

- Pasaje de información directa entre 2 procesos
- Linux: API de un archivo para lectura/escritura
- **Unnamed Pipes (Pipes)**:
	- Comunicación entre procesos padre e hijo
	- Dejan de existir al finalizar el proceso
- **Named Pipes (FIFOs)**:
	- Comunicación entre procesos cualesquiera
	- Viven en el SO, exceden la vida del proceso

#### Message Queues (System V)

- Procesos escriben/reciben **bloques de bytes**
- Campo ***mtype***:
	- Identifica el tipo de mensaje
	- Sender debe enviar mensajes con mtype > 0
	- Receptor con mtype = 0 recibe mensajes sin importar el tipo
- Mensajes leídos son removidos de la cola
- Buffer size definido en la creación

#### Sockets

- Comunicar dos procesos a través de un canal de comunicación (endpoint)
- `int socket(int domain, int type, 0);`
- **Domain**:
	- AF_UNIX - Unit Socket
	- AF_INET / AF_INET6 - Network Socket
- **Type** (protocolos de comunicación):
	- SOCK_DGRAM => UDP
	- SOCK_STREAM => TCP
	- SOCK_RAW

### Problemas clásicos

#### Productor-consumidor

- Productores agregan paquetes en el buffer
- Consumidores extraen paquetes del buffer
- Situaciones de **bloqueo**: 
	- Productor intenta agregar un paquete cuando el buffer está lleno
	- Consumidor intenta extraer un paquete cuando el buffer está vacío
- El acceso al buffer debe ser sincronizado 
- El buffer puede ser **acotado** o **infinito**

#### Lector-escritor

- Procesos intentan acceder a una memoria compartida
- Dos tipos de procesos (**Lectores** y **Escritores**)
- Tipos de problemas definidos en función de las propiedades ***fairness*** y ***starvation***:
	- **Prioridad Lectores**: Escritores esperan a que lectores liberen recurso compartido 
	- **Prioridad Escritores**: Lectores esperan a que Escritores liberen recurso compartido
	- Lectores y Escritores acceden a recurso compartido por **tiempo limitado**.

### Comunicaciones

#### Modelo TCP/IP

- **Application**: Aplicaciones de usuario, representación de datos (Capa 5-6-7 de OSI)
- **Transport**: Comunicación punto a punto (Capa 4 de OSI)
- **Internet**: Lógica de transmisión de datos sobre la red (Capa 3 de OSI)
- **Network Access**: Transferencia física confiable, libre de errores. (Capas 1 y 2 de OSI)

#### Modelo OSI

![[Modelo OSI.png]]

#### TCP y UDP

- **TCP**: orientado a conexión, asegura entrega y orden
- **UDP**: orientado a datos, sin garantías (*best-effort*)

#### Flujo de Sockets en TCP

![[Flujo Sockets TCP.png]]

#### Flujo de Sockets en UDP

![[Flujo Sockets UDP.png]]

- Aquí no está orientado a conexión: simplemente el cliente conoce al servidor y le envía paquetes.

#### Congestión de redes

- **Throughput**: cantidad real de datos que pasan con éxito por una red en un tiempo determinado.
- **Throttling**: bajada de velocidad que un proveedor aplica a propósito para frenar el tráfico.
- **Delay**: cuánto tardan los paquetes en llegar.
- **Latencia**: tiempo que tardan los paquetes en realizar un viaje de ida y vuelta (RTT - Round Trip Time).