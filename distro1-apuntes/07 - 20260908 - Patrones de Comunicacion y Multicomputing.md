## Clase 7 - Patrones de Comunicacion y Multicomputing

### Patrones de Comunicación

#### Request-Reply

- Protocolo usado en modelo Cliente-Servidor
- Es **sincrónico (bloqueante)** por defecto
	- Cliente envía Request Message
	- Servidor recibe Request, procesa mensaje y envía Reply
	- Cliente queda bloqueado hasta recibir Reply Message
- ACK triviales (el Reply es un ACK) aún teniendo información valiosa
##### Implementar **operación asincrónica**

- 2 Request-Reply sincrónicos necesarios
- Primero se envía operación a realizar
- Luego se obtiene el resultado de la operación

Consideraciones:
- El servidor encola la acción; no tiene por qué resolverla en el momento.
- Luego el cliente sigue con su funcionamiento (aprovecha para hacer otras tareas), y en algún momento le pregunta el Estado (resultado) al servidor.
- El servidor obtiene el Estado y se lo envía al cliente.
##### Estructura de mensajes

Campos obligatorios:
- **messageID**: `0` (Request); `1` (Reply)
- **requestID**: identifica unívocamente al mensaje
	- Auto-incremental
	- UUID: IDs únicos y universales
- **operationID**: identifica acción/operación a realizar
- **args**: atributos asociados a la acción/operación
##### Tolerancia a Fallos

Momentos para esperar por un Reply:
- Timeouts con *Retries*
- Algoritmo de Backoff: el sistema **espera más tiempo** antes de repetir una acción fallida
	- Agregar *jitter* (varianza): los mensajes se desparraman más

Al perder un Request o un Reply:
- **Sin control**: no hay Retry ni se pueden implementar filtros de duplicados. Aquí **quizás** se recibe el mensaje.
- **Re-ejecución**: se hace Retry sin filtros de duplicados. Se recibe el mensaje **al menos una vez** (quizás más).
- **Retransmisión**: se hace Retry filtrando duplicados. Se recibe el mensaje **exactamente una vez**.
- El chequeo de duplicados se realiza del lado del **servidor**.
#### Fire-and-Forget

- Protocolo utilizado para *streaming* o despacho de eventos
- Es **asíncrono** por naturaleza
	- Cliente envía Request Message
	- Cliente continúa realizando sus tareas
	- Servidor recibe Request y procesa mensaje **cuando puede**
- Sobre UDP no existe ningún ACK.
- Sobre TCP existe únicamente por el protocolo mismo: da garantía de la tira de *bytes* fue enviado y recibido exitosamente.
#### Producer-Consumer

- Modelo basado en **comunicación por tareas** entre productores y consumidores.
- El **productor** crea los datos, eventos o mensajes, y los envía. Meten materia prima para ser utilizada.
- El **consumidor** toma los datos del almacenamiento y realiza el trabajo necesario.
##### ZeroMQ

- Comunicación de tareas de un productor a un consumidor.
- Admite múltiples consumidores y/o múltiples productores.
- Garantiza *fairness* en la entrega de mensajes: **round robin**.
- Usa los sockets PUSH/PULL para marcar el rol de cada extremo.
#### Publisher-Suscriber

- Modelo basado en **comunicación por eventos** entre productores y consumidores.
- **Publishers**: emisores, tienen la posibilidad de generar algún elemento de interés.
- **Subscribers**: receptores, esperan la aparición de algún evento de su propio interés sobre el cual efectuarán alguna acción.
##### Arquitectura

- **Basada en tópicos**: publicación y subscripción indicando el tipo de evento, tópico o tag.
- **Basada en canales**: publicaciones y subscripciones orientadas a canales específicos.
##### Implementacion con **MOMs**

- **Bus**: cada departamento crea sus propios tags para poder publicar determinados eventos, otros pueden simplemente escuchar sus preferidos.
- **Colas**: en cada una hay que colocar mensajes duplicados si hay varios interesados.
##### ZeroMQ

- Un **PUB** socket publica mensajes.
- $N$ **SUB** sockets se suscriben a los Eventos que desean recibir suscribiendose al ID del evento.
- La suscripción se puede **cancelar** en cualquier momento.
- Mensaje es enviado a todos los sockets suscriptos a un **evento determinado**.
### Aplicación con **Multicomputing**

#### Web Crawler
##### Requerimientos funcionales

- Dada una URL, obtener el HTML y analizarlo.
- Detectar nuevas URLs e identificar archivos multimedia, documentos y otras páginas Web.
- Descargar los archivos multimedia (*.jpg*, *.png*, *.mp3*, *.ogg*, etc).
- Descargar los documentos (*.pdf*, *.odt*, etc).
- Agendar el procesamiento de las nuevas páginas Web encontradas.
- Detener el análisis de nuevos links luego de $N$ anidaciones.
##### Requerimientos no funcionales

- Descargas y análisis ejecutadas **en paralelo**.
- Monitoreo constante en un archivo de texto con cantidades de:
	- **URLs** analizadas
	- **Archivos** descargados por extensión
	- **Procesos** en cada uno de los siguientes estados:
		- Obteniendo HTML
		- Procesando HTML
		- Descargando recurso