## Clase 1 - Introducción de Sistemas Distribuidos

### Bibliografía importante (recomendada)

- G. Coulouris, J. Dollimore, t. Kindberg, G. Blair: Distributed Systems. Concepts and Design, 5th Edition, Addison Wesley, 2012.
- M. Van Steen, A. Tanenbaum: Distributed Systems. 3rd Edition. Pearson Education, 2017. 
- McCool M., Robison A. D., Reinders J., Structured Parallel Programming Patterns for Efficient Computation, Elsevier-Morgan Kaufmann, 2012.
- Garg, V., Elements of Distributed Computing, 1st. Ed. Wiley IEEE Press, 2002. 
- Martin Kleppmann, Designing Data-Intensive Applications, O'Reilly Media, 2017.
- P. Verissimo, L. Rodriguez: Distributed Systems for Systems Architects, Kluwer Academic Publishers, 2001.
- D. Comer, D. Stevens, Internetworking with TCP/IP, Client-Server Programming and Applications, Linux/Posix Sockets Version, Prentice Hall, 2001.
- W. Stallings, Data and Computer Communication, 9th Edition, 2010.
- Gomaa, Hassan: Software Modeling & Design. UML, Use cases, pattern & software architectures. Cambridge, 2011.
- Ben-Ari, M. Principles of Concurrent and Distributed Programming, 2nd. Ed. Addison Wesley, 2006.

### Definiciones de sistemas distribuidos

- “Colección de computadoras independientes que el usuario ve como un solo sistema coherente” (Tanenbaum) 
- “Es un sistema de computadoras interconectadas por una red que se comunican y coordinan sus acciones intercambiando mensajes” (Coulouris) 
- “Aquel en el que el fallo de un computador que ni siquiera sabes que existe, puede dejar tu propio computador inutilizable” (Lamport)

Desglose de las definiciones:

- Colección de computadoras => multiprogramación 
- Independientes => autónomos
- Un solo sistema => el usuario no conoce su distribución
- Interconectadas por red => sistemas aislados no son distribuidos
- Comunican y coordinan acciones => colaborativos
- Intercambiando mensajes => protocolos de comunicación
- Fallo de un computador => nuevos problemas no determinísticos

### Parámetros de diseño

- Transparencia
- Escalabilidad
- Tolerancia a Fallos
	- Availability, Reliability, Safety, Maintainability 
- Acceso a Recursos Compartidos
- Sistemas distribuidos abiertos
	- Interfaces, Interoperability, Portability

### Ventajas de centralizar (sistemas centralizados)

- **Control**: lógica de control muy simple, efectiva y, en ocasiones, eficiente.
- **Homogeneidad**: la centralización incita a definir estándares para software y hardware.
- **Consistencia**: es posible definir fuertes políticas de consistencia de información y monitoreo del estado global del sistema.
- **Seguridad**: se disminuye la 'superficie de ataque' frente a amenazas.

### Ventajas de distribuir (sistemas distribuidos)

- **Disponibilidad**: aún frente a fallos aislados, el sistema general puede prestar servicios.
- **Escalabilidad**: mejores alternativas de adaptarse a nuevas escalas.
- **Reducción de Latencia**: al favorecer principios de localidad de recursos.
- **Colaboración**: permite interacciones entre sistemas de forma orgánica y natural.
- **Movilidad**: no están circunscriptos al alcance de un único computador.
- **Costo**: componentes más simples. Subsistemas delegados en servicios terceros.

### Descentralizar vs Distribuir 

- **Centralizar** implica la concentración de la autoridad en los niveles más altos de un jerarquía. 
- **Descentralizar** implica transferir la toma de decisiones a eslabones inferiores de cierta organización. 
- **Distribuir** implica utilizar un modelo descentralizado de control de computadoras para la coordinación de actividades con una coherencia dada.

### Virtualización

- Necesidad de independencia real de los recursos 
	- Seguridad en el acceso
	- Diferenciación entre instrucciones con privilegios vs sin privilegios. 
- **Hypervisor (Virtual Machine Monitor)**
	- Manager de VMs
	- Emulación de Hardware Capabilities: hacer de cuenta que tenemos algo pero hay que crearlo (es carísimo)
	- Administración de recursos del **Host OS** (gobierna la computadora) hacia los **Guest OS** (un cliente o VM que corre sobre la virtualización)
	- Implementación de mecanismos de seguridad

![[virtualizacion-inicios.png]]

![[virtualizacion-continuacion.png]]