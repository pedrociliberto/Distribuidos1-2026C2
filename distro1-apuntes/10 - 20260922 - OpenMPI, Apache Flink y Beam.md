## Clase 10 - OpenMPI, Apache Flink y Beam

### Open MPI (Message Passing Interface)

- Transmisión y recepción de mensajes
- Ejecución transparente de $1$ a $N$ nodos
- Se utiliza como una librería con abstracciones de uso general con foco en el cómputo distribuido
- Implementa un middleware de comunicación de grupos:
	- MPI_Recv, MPI_Send
	- MPI_Bcast, MPI_Scatter, MPI_Gather
	- MPI_Reduce

### Apache Flink

- Plataforma de procesamiento distribuido de datos
- Incluye motor de ejecución de *pipelines* de transformación
- Define *framework* Java/Scala para crear pipelines:
	- SQL y Table API permiten definir tablas dinámicas (lógicas) con los flujos de datos y utilizar álgebra relacional
	- Dataset y DataStream API permiten definir secuencias de procesamiento con formato DAG
- **Dataflow**:
	- ***Streams***: flujo de información que puede no finalizar
	- ***Batchs***: conjunto de datos de tamaño conocido (*dataset*)
- **Bloques** de un *Pipeline*:
	- **Source**: inyecta datos al *pipeline*
	- **Transformation** (operador): modo de modificación/filtrado de datos
	- **Sink**: destino de la información, almacenamiento final
- **Ventanas**:
	- **Por tiempo**: fija un corte cada $X$ unidades de tiempo (cierra sin importar la cantidad de datos que entraron)
	- **Por cantidad**: depende de la velocidad de los *pushes* en el Stream, se espera hasta recibir $X$ cantidad de elementos.
- Casos de uso:
	- **Extract Transform Load (ETL)**: operaciones programadas de carga y modificación de datos para posterior análisis con origen y destino definidos en una DB.
	- **Data Pipelines**: tareas de procesamiento recurrentes, basadas en la ocurrencia de eventos.