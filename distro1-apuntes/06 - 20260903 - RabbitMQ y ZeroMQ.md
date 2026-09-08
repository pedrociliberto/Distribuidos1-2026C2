## Clase 6 - RabbitMQ y ZeroMQ

### RabbitMQ (MOM Centralizado)

**RabbitMQ** es un middleware orientante a mensajes (MOM) de arquitectura centralizada basado en un _broker_.

- **Componentes principales:**
    - **Producers (Productores):** Generan y envían mensajes al _broker_ (específicamente a los _exchanges_).
    - **Exchanges (Intercambiadores):** Reciben mensajes de los productores y aplican distintas estrategias para distribuirlos hacia las colas. Los tipos principales son:
        - **Fanout:** Realiza un _broadcast_ enviando todos los mensajes a todas las colas vinculadas (_bindeadas_).
        - **Direct:** Redirige los mensajes a colas específicas evaluando una clave de ruteo (_routing_key_) asignada al mensaje.
        - **Topic:** Rutea mensajes según patrones de palabras clave en la _routing_key_ separadas por puntos, utilizando comodines como `*` (reemplaza una palabra) y `#` (reemplaza una o más palabras).
        - **Headers:** Basado en atributos en la cabecera del mensaje.
    - **Queues (Colas):** Almacenan los mensajes hasta ser procesados. Pueden ser nombradas, de tareas (_TaskQueues_) o anónimas. Por defecto, la confirmación (_acknowledgement_) es automática, y la durabilidad debe configurarse explícitamente tanto en la cola como en el mensaje.
    - **Consumers (Consumidores):** Procesan los mensajes recibidos desde las colas.
        
- **Patrones de mensajería soportados:**
    - **Publisher-Subscriber:** El publicador envía mensajes a un _exchange fanout_, y los suscriptores crean colas anónimas vinculadas a dicho _exchange_ para recibir todos los mensajes.
    - **Routing:** El productor adjunta una _routing_key_ a un _exchange direct_, y las colas de los consumidores realizan el _binding_ únicamente a las claves que desean procesar.
    - **Topic:** Permite filtrado de mensajes mediante expresiones y patrones con comodines a través de un _exchange topic_.
### ZeroMQ (MOM Distribuido)

**ZeroMQ** (ØMQ) es una biblioteca de red de alto rendimiento diseñada para construir middlewares sin necesidad de un _broker_ central (_brokerless_). Actúa como un conjunto de "sockets avanzados" (_sockets on steroids_) donde la serialización de datos queda completamente a cargo del usuario.

- **Tipos de conexiones:**
    - **TCP:** Para comunicación entre múltiples computadoras (unicast / punto a punto).
    - **IPC:** Para comunicación entre procesos en la misma máquina a través de Unix Sockets.
    - **Inproc:** Para comunicación de alta velocidad entre hilos (_multithreading_) dentro de un mismo proceso.
    - **Multicast:** Basado en el protocolo PGM.
- **Patrones de mensajería y tipos de sockets:**
    - **Request-Reply:** Modelo cliente-servidor mediante sockets `REQ` y `REP`. No utiliza la primitiva `accept`; `bind` combina encolado y aceptación, mientras que `send` es no bloqueante.
    - **Producer-Consumer / Pipeline:** Utiliza sockets `PUSH` y `PULL` para distribuir tareas garantizando un reparto equitativo (_fairness_) mediante la técnica de _round-robin_. Permite encadenar etapas (Ventilator $\rightarrow$ Workers $\rightarrow$ Sink).
    - **Publisher-Subscriber:** Un socket `PUB` transmite publicaciones identificadas por un ID de evento, y los sockets `SUB` filtran y reciben los mensajes a los que están suscriptos.
    - **Router-Dealer**: Sockets asincrónicos para arquitecturas avanzadas o intermediación (_brokers_). `ROUTER` agrega/gestiona IDs de origen y destinatario, mientras que `DEALER` distribuye los mensajes de forma justa e idéntica.