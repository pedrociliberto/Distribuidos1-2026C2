## Clase 4 - Capas, Interfaces y Protocolos

### Arquitecturas de Capas

- Permiten dividir el problema en *subproblemas*.
- Fomentan el uso de interfaces.
- Permiten intercambiar componentes reutilizando conectores y protocolos ya definidos.
- Dos tipos de separación por capas:
	- **Layers**: capas lógicas
	- **Tiers**: capas físicas

### Layers

- Agrupación lógica de componentes y funcionalidades de un sistema.
- Se identifican como capas **verticales** u **horizontales**.
- Las verticales representan jerarquías de comunicación. 
	- Las más bajas son más simples y básicas, y las de arriba utilizan las inferiores (*downcall*) para agregar más complejidad. 
	- Si las de abajo llaman a las superiores (*upcall*), se generan **dependencias circulares**. Cuando ambas se necesitan, quizás hay suficiente cohesión para que sean **una misma capa**.
- Cada módulo debe tener responsabilidades limitadas, coherencia y cohesión.

### Tiers

- Describen la **distribución física** de componentes y funcionalidad de un sistema.
- Cada caja es un nodo (servidor, computadora, etc).
- Se habla de relaciones de dependencia entre distintos nodos físicos.
- **2-Tier Deployment**: uno es la base de datos real, y el otro son los clientes reales que piden información. Ambos manejan lógica de negocio.
- **3-Tier Deployment**: entre los dos anteriores hay un Tier de lógica de negocio que la maneja por completo.

![[layers y tiers.png]]

En esta imagen se juntan los **Layers y Tiers**:
- Por fuera aparecen los **Tiers** (cajas) y sus relaciones de dependencia.
- Dentro de cada una aparecen **Layers**, tanto verticales como horizontales.

### Interfaces

- Permiten la comunicación entre dos o más componentes/servicios/sistemas.
- Diferentes contratos permiten diferentes clientes.
- Solo se expone **una parte** del sistema.
- Esconden implementación:
	- Puede ser moficiada sin cambiar contrato
	- Cambio de contrato implica una nueva versión

Existen interfaces:
- **Inter-Aplicaciones (APIs)**: cliente por consola consultando Web Server, Servicio consultando otro Servicio, etc.
- **Intra-Aplicaciones**: Facades, Mediators, Interfaces. Layer 2 consultando Layer 1, ensaje enviado a un objeto local/remoto, etc.

#### Problemas a resolver

- **Software es difícil de cambiar**: ¿qué pasa si un sistema está altamente acoplado a una API y esta cambia?
- **Software es difícil de integrar**: no todos los componentes exponen interfaces útiles, y la complejidad aumenta con la cantidad de componentes.

#### Orientación del Contrato

**Orientados a entidades**:
- Desacoplamiento entre sistemas
- Flexibilidad como objetivo
- Funcionalidad estándar

**Orientadas a procesos**:
- Componentes altamente acoplados: se define la interfaz en función a lo que se hace
- Alta performance como objetivo
- Funcionalidad diversa 

#### Clasificación

- **Web APIs** (cliente final)
	- Web Services based APIs (HTTP+SOAP)
	- REST based APIs 
- **Remote APIs** 
	- Custom TCP/UDP services
	- Object oriented: CORBA, JavaRMI
	- Procedure oriented: RPC, gRPC
- **Library-based / Frameworks** (internas)
	- Java API (Ej.: OpenJDK vs Oracle JDK)
	- Android API 
- **OS related** (sistemas operativos)
	- POSIX (Ej.: Linux vs OpenBSD)
	- WinAPI

### Protocolos

#### Modelo HTTP

Características:
- Modelo Client-Server
- Modelo Request-Reply
- Sin estado (servidor)

#### Responsabilidades por capa

En la capa **Application**:
- Servicios
- Core (modelo)
- Comunicación (pasaje de bytes)

#### Protocol Data Unit (PDUs)

- Se agregan headers a los paquetes para transportarlos por las capas.
- Encapsulación:
	1. Encapsulación exacta
	2. Segmentación de paquetes
	3. Blocking de paquetes

### Mensajes

#### Formato de paquetes

##### Binario

- **Alta performance**
	- Tamaño de mensajes eficientes
	- Compresión puede no ser necesaria
- **Serialización**
	- Autogeneración de código
	- No siempre existe soporte en todos los lenguajes
- **Interacción**
	- Cliente específico para cada aplicación
	- Decoder para intepretar los mensajes

**Ejemplo**: Protobuf
##### Texto plano

 - **Baja performance**
	 - Throughput bajo
	 - Compresión agrega overhead 
 - **Serialización**
	 - Formatos human-readable (JSON, XML)
	 - Serialización básica (e.g. HTTP, SMTP)
 - **Interacción**
	 - Cliente único si se conoce el protocolo (e.g. cURL + REST API)
	 - Fácil de debuggear

**Ejemplo**: CURL

#### Longitud de paquete

 - **Bloques fijos**
	 - Cada dato a enviar posee una longitud fija
	 - Fácil de serializar
	 - Subóptimo con tipos de longitud variable (e.g. strings)
 - **Bloques dinámicos**
	 - Separador para delimitar comienzo y terminación de un campo
	 - Longitud del campo para delimitar longitud del campo
	 - Pequeño overhead al agregar bytes extra
 - **Esquema mixto**
	 - Parámetro fijos (e.g. integers) no llevan delimitadores / longitud
	 - Parámetros variables llevan delimitadores / longitud

Ejemplo: **TLV (Type-Length-Value)**
- Todos los parámetros siguen el formato Type - Length - Value.
	- **Type**: Indica el tipo de dato / entidad. Tamaño fijo.
	- **Length**: Longitud del tipo sin contar el tipo y el length. Tamaño fijo.
	- **Value**: Dato a enviar. Tamaño variable. Admite subtipos. 

### RESTful

- Tipo de API (protocolo) orientado a entidades (*Web Resources*). 
- Cada Web Resource es representado por una URI.
- **HTTP/HTTPS** usado como protocolo de comunicación
- **JSON/XML** usado como protocolo de serialización
- Cambio de estados a través de operaciones **CRUD** (create, read, update, delete)

#### Principios RESTful

- Alta performance, escalabilidad, confiabilidad, etc.
- **Principios de Arquitectura**:
	- Cliente/servidor
	- Recursos se pueden cachear
	- Interface uniforme
		- HATEOAS: links dinámicos en respuestas del servidor
	- Statelessness: no se mantienen sesiones entre un pedido entre cliente y servidor
	- Layered system

#### Versionado de APIs

**Semantic Versioning (semver)**:
- Estándar más utilizado para definir versiones de APIs y Librerías.
- Foco en brindar información de retrocompatibilidad de la interfaz.
- **Incremento de números de versión**:
	- **Major**: al introducir cambios incompatibles con la versión anterior.
	- **Minor**: al agregar funcionalidad pero mantener retrocompatibilidad.
	- **Patch** (aka build): al introducir correcciones que no afectan la interfaz.

![[versionado APIs.png]]