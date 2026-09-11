## Clase 8 - Arquitecturas Distribuidas Simples

### Arquitecturas Distribuidas Simples
#### Cliente-Servidor

- **Servidor** como elemento pasivo y provee servicios
- **Clientes** activos que envían pedidos al servidor
- Permite centralización en toma de decisiones
- Suele asumirse que los servidores tienen más capacidades de *hardware* que los clientes.
##### Flujos de comunicación

- Clientes deben conocer la **ubicación del servidor** para poder usarlo.
- Clientes no entablan comunicaciones entre sí, salvo a través del servidor.
- Se usan modelos de ***callback*** (no de forma natural): *Long polling*, notificaciones Push.
#### Peer-to-Peer

- Red de nodos que se consideran **pares entre sí** 
- Asume capacidades de **recursos similares** entre los pares
- Muy útil cuando existen objetivos de colaboración: 
	- Protocolo acordado entre partes
	- La lógica distribuida requiere coherencia entre los nodos
- Auge en internet a partir de la invención de *Napster*, *BitTorrent*, etc
##### Flujos de comunicación

- Muy difícil de establecer la comunicación entre pares:
	- **Esquema mixto** como cliente-servidor para proveer un servicio de nombres
	- Grupo de comunicación donde se comparten dirección de miembros
- Requieren mayores permisos de *networking*
### Pipelines y DAGs

#### Pipelines

- Los datos de entrada forman un flujo donde distrintos *filters* se conectan entre sí para procesarlos de manera secuencial.
- Muy utilizado en entornos Unix:
	- `cat in | grep pattern | sort | uniq > out`
##### Modelos de procesamiento

- **Worker por Filter**: se asigna una unidad de procesamiento a cada etapa del pipeline. Los items son recibidos por el worker, procesados y enviados a la próxima etapa.
- **Worker por Item**: se asigna una unidad de procesamiento a cada item. Un worker toma al item ingresado y lo acompaña hasta el final del pipeline, aplicándole los filters paso a paso.
##### Etapas

Cada *filter* (processor) funciona como una etapa:

- **Paralela**: cada ítem a procesar es independiente de anteriores y posteriores, admite paralelismo.
- **Secuencial**: no puede procesar más de un ítem a la vez. Ya procesados, los puede retornar ordenados o desordenados.

##### Ventajas

- Algoritmos ***Online***: iniciar procesamiento antes de que estén disponibles todos los datos.
- **Información infinita**: 
	- Flujos ilimitados de información con cantidades constantes de memoria.
	- Procesamiento encadenado, con buffer mínimo para configurar el pipeline.
##### ZeroMQ

- **Patrón Productor-Consumidor**: se hace *chaining* de productores/consumidores que da como resultado un pipeline.
- Mensajes son consumidos de forma equitativa (**fairness**).

### DAGs (*Directed Acyclic Graphs*)

- Instrucciones modeladas mediante un **grafo de flujo de datos**.
- Los nodos indican **tareas** y las aristas el **flujo de información**.
- Acíclicos: para todo nodo, no hay un camino que inicie y termine en él.
- Permite calcular trabajo total y caminos críticos.
##### Ventajas

- Representación natural para *dataflows*.
- La carga de procesamiento se puede paralelizar.
- Admite *Lazy Loading* de las operaciones: solo procesa nodos requeridos por **dependencias**.
##### Dependencias y *non-DAGs*

- Usar DAGs para modelar dependencias entre procesos.
- Dependencias implican posibilidad de bloqueo frente al pedido del recurso de un proceso a otro
- Si el grafo es cíclico, existe **posibilidad de *deadlock***.
- Nos sirve para **detectar y recuperar sistemas frente a *deadlocks***.
### RPC (*Remote Procedure Calls*)

- **Ejecución remota** de procedimientos
- Modelo Cliente-Servidor:
	- Cliente realiza llamada a un Procedimiento
	- Servidor responde con el resultado de la operación
- Comunicación remota **transparente** para el usuario: que no se dé cuenta que se está llamando a una función remota.
- Portabilidad a través de implementación de **interfaces bien definidas** (puede haber distintos lenguajes en la comunicación).
#### IDL (*Interface Domain Languages*)

- Permitir que **diferentes lenguajes** puedan invocarse entre sí.
- Interfaz definida en función de *Input* y *Output*:
	- Acceso a métodos permitido
	- Pasaje de variables **por valor**
	- Punteros no permitidos (hay distintas computadoras)
- Definición de tipos de mensajes a enviar
- Ejemplo: Google Protocol Buffers
#### Tolerancia a Fallos

- A diferencia de LPCs (*Local Procedure Calls*), un procedimiento **puede o no ser ejecutado**.
- Diferentes estrategias para garantizar *delivery* de mensajes:
	- Request-Retry com *Timeout*
	- Filtrado de operaciones duplicadas
	- **Retransmisión** / **Re-ejecución** de operación si se pierde *retry*
#### Call Semantics

Al asegurar *delivery* de mensajes, pueden llegar a recibirse 0, 1 o muchas veces:

- Cuando **no hay** Retry-Request, no hay control y el filtro de duplicados no es implementable. El mensaje **quizás se recibe**.
- Cuando **hay** Retry-Request:
	- **Re-ejecución**: no hay filtro de duplicados, y el mensaje **se recibe al menos una vez** (posiblemente más).
	- **Retransmisión**: se filtran duplicados, por lo que el mensaje **se recibe exactamente una vez**.
#### Implementación

- **Cliente**: conectado a un *stub*; realiza lamadas de forma transparente al servidor.
- **Servidor**: conectado a un *stub* del cual recibe parámetros. Posee lógica particular del *remote procedure*.
- **Stubs**: administra el *marshalling* de información. Envía información de las *calls* al módulo de comunicación y al cliente/servidor.
- **Módulo de comunicación**: abstrae al *stub* de la comunicación con el servidor.
#### gRPC

- Definición de RPC basada en:
	- HTTP2 para transporte
	- Protocol Buffers para *encoding*
	- Conexión punto a punto basada en `server:port`
- Definición de Servicios y mensajes en **archivos `.proto`**
- Generación de código en distintos lenguajes
- Distribuido para alta performance y microservicios
### Objetos Distribuidos

- Los servidores ya no proveen servicios sino **objetos**.
- Existe un **middleware** que oculta la complejidad de:
	- Referencias a objetos remotos
	- Invocación de acciones
	- Errores (excepciones)
	- Recolección de basura
- Aquí **sí hay estado**, pero en los objetos. No están en las llamadas.
- Se desea tener **muchos servidores** que poseen muchos objetos. Los objetos podrían migrarse de uno a otro.
#### CORBA (*Common Object Request Broker Arquitecture*)

- Estándar definido por un **comité**
- Soporte en **múltiples lenguajes**
- Está en vías de deprecación
- Provee:
	- **Protcolo y serialización** del estado y mensajes del objeto
	- **Transporte** (coordinación)
	- **Seguridad**
	- ***Discovery*** de Objetos: servicio que llega a un objeto a través de algún identificador.
- Entre cliente y servidor hay un **ORB** (*broker*).
	- Del lado del cliente se inicializa el ORB
#### RMI (*Remote Method Invocation*)

- Versión optimizada de Distributed Objects **solo para Java**.
- Cliente y servidor deberían tener mismas versiones del lenguaje.
- No require el **POA** (Object Adaptor) del lado del servidor.
- Requiere:
	- **Registro** de servidor en un **directorio de servicios**.
	- **Consulta** del registro por parte del cliente.
	- **Invocación** desde el cliente objetos del servidor.