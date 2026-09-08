## Clase 5 - Grupos, Middlewares y MOMs

### Grupos de comunicación

- Elementos que deben hablarse entre sí, o eventualmente con otros grupos similares.
- Permiten ver a una **colección de procesos** como una **abstracción**.
- Mensaje es enviado a todas las entidades que componen el mismo grupo.
- Grupos **dinámicos**:
	- Se pueden crear y destruir en todo momento
	- Los procesos se pueden suscribir y desuscribir a los grupos (primitivas que lo permiten)

#### Difusión de mensajes

- **Uno a uno**
	- **Unicast**: Comunicación punto a punto
	- **Anycast**: Envío al nodo más cercano (ECMP, ¿cercanía?)
- **Uno a muchos**
	- **Multicast**: Solo aquellos que se encuentran en el grupo reciben el mensaje
	- **Broadcast**: todos reciben el mensaje

#### Topología

- **Anillos**: cada uno se conecta a sus dos adyacentes
- **Punto a Punto**: enlace directo entre todos los puntos
- **Grupos Jerárquicos**: organización en forma de árbol invertido por niveles/ramas

**Difusión**:
- **Descentralizada**: un nodo raíz le delega la responsabilidad a sus hijos para seguir enviando el mensaje,
- **Centralizada**: el nodo raíz se encarga de confirmar que se envien los mensajes al resto de nodos.

#### Atomicidad de mensajes

- Los mensajes se deben entregar **a todos o a ninguno** de los miembros
- Necesidad de realizar ***ACK*** de mensajes
- Necesidad de demorar el *delivery* de los paquetes recibidos
- Se hacen reintentos frente a:
	- Caída de receptores
	- Caída del coordinador
	- No recepción de mensajes
	- No recepción de ACKs
- Pagar el costo de atomicidad: aumentar cantidad de mensajes enviados (overhead)

### Middlewares

#### Definiciones

- *“... software de conectividad que ofrece un conjunto de servicios que hacen posible el funcionamiento de aplicaciones distribuidas sobre plataformas heterogéneas...”*
	- Hay componentes no homogéneos
- *“... módulo intermedio que actúa como conductor entre sistemas permitiendo a cualquier usuario de sistemas de información comunicarse con varias fuentes de información que se encuentran conectadas por una red”* 
	- Módulo para enviar y recibir información arreglando la comunicación
- *“... capa de software que se encuentra o sitúa entre el sistema operativo y las aplicaciones del sistema”* 
	- Capa media
- *“... software que permite conectar componentes softwares o aplicaciones. El mismo consiste en un conjunto de servicios que permiten que múltiples procesos corriendo en una o varias máquinas interactúen de un lado a otro de la red.”*

#### Vista lógica

![[middleware vista logica.png]]

- Todos igual pasan por los sistemas operativos y la capa de networking.
- La idea es **normalizar en una capa intermedia**. Se programa en esa capa haciendo de cuenta que las capas inferiores no están.

#### Objetivos

- **Transparencia**:
	- Se oculta la distribución.
	- El sistema actúa como si fuera una **única computadora**.
	- Respecto de: acceso a elementos, ubicación de programas, migración de procesos/memoria, replicación información, concurrencia, fallos y persistencia.
- **Tolerancia a fallos**:
	- Sistemas confiables, que se ejecuten y comporten de manera **predecible** aún cuando aparecen fallos.
	- Se abarcan: *Availability*, *Reliability*, *Safety*, *Maintainability*
- **Acceso a recursos compartidos**: eficiente, transparente y controlado
- **Sistemas abiertos (Interfaces)**: 
	- Estándares claros de sintaxis y semántica
	- Interoperabilidad y portabilidad
- **Comunicación de grupos**:
	- Permite *broadcasting* y *multicasting*
	- Facilita localización de elementos y coordinación de tareas

#### Ejemplos

- **Middleware Centralizado**: 
	- Los clientes ($N$) se conectan a una librería
	- Hay varios *host* centrales, ***brokers*** (varios servidores puntos de fallo) que lo administran. 
		- Discovery de servicios, seguridad, persistencia y orquestación para despachar mensajes.
	- Todos los nodos ($M$) tienen una pequeña noción del middleware.
- **Middleware Distribuido**:
	- Hay $M$ servidores hablándose entre ellos acerca de los cambios en las tareas.

#### Clasificación

- **Transactional Procedure**
	- Garantizar **transaccionalidad de operaciones** respecto de datos.
	- Conectan muchas fuentes de datos y permiten acceso transparente al grupo.
	- Poseen **políticas de reintentos** y retención de datos por caídas internas.
- **Object Oriented**
	- Mensajes hacia objetos distribuidos.
	- Los objetos viven dentro del *middleware host*.
	- Esquema de **marshalling** para transmitir información.
- **Procedure Oriented**
	- El *middleware* trabaja como un **servidor de funciones** que se pueden **invocar**.
	- Los servicios se exploran y ejecutan pero no presentan estado para futuras invocaciones.
- **Message Oriented**
	- Sistema de mensajería entre aplicaciones que usan el *middleware*.
	- Pueden enviarse mensajes con cierto *tópico* para los receptores interesados (***Information Bus***).
	- Pueden enviarse mensajes con un destinatario definido (***Queue***).
	- Comunicación de grupos por Anillos, Punto a Punto y Grupos Jerárquicos.
- **Reflective Middlewares** (de configuración dinámica)

### Message-Oriented Middlewares (MOMs)

- Implementan comunicación de grupo de forma transparente a las aplicaciones que la requieren.
- Basan su funcionamiento en **comunicar mensajes entre aplicaciones**.
- Resuelve problemas de transparencia respecto de ubicación, fallos, performance y escabilidad.

#### Bus de Información vs. Colas de Mensajes

- **Bus de Información**:
	- Distintos modelos desparramados quieren emitir y recibir información. 
	- Algunos publican y otros se suscriben al bus de mensajes.
- **Colas de Mensajes**:
	- Los modelos tienen colas (*buffers*) para recibir elementos de otros que los envían.

Todos deben tener la **misma configuración** antes de ser desplegados (alguna de las mencionadas).

#### Modelos de MOMs

- **Sincrónico**:
	- **Pros**: se modela como una conexión punto a punto, permite obtener respuestas instantáneas a pedidos concretos.
	- **Contras**: no permite implementar transparencia frente a errores.
- **Asincrónico**:
	- **Pros**: modelado con colas, soporta períodos de discontinuidad del transporte.
		- Se definen los **canales de comunicación** (Sender y Receiver), y pueden estar ambos activos, uno de ellos o ninguno.
	- **Contras**: complejo recibir respuesta a pedidos realizados (sí o sí tener colas para el retorno de información).

#### Operaciones comunes

- **put**: publicar un mensaje.
- **get**: esperar a detectar un mensaje (eliminarlo de la cola y retornarlo).
- **poll**: revisar mensajes pendientes sin bloquear.
- **notify**: asociar un *callback* usado por el MOM para que se ejecute frente a ciertos mensajes.

#### Colas de Mensajes y Broker

- Pueden existir varias definidas dentro del MOM
- Tienen nombre y longitud definidas
- Los clientes suelen contar con colas privadas intermedias 
- Garantía al Emisor de que el mensaje será insertado

#### Brokers

- Proveen **transparencia de localización** tanto al Emisor como al Receptor.
- Soportan lógica en el *middleware* para filtrar, modificar y enrutar mensajes.
- Brindan un punto de **control y monitoreo**.

![[mom brokers.png]]