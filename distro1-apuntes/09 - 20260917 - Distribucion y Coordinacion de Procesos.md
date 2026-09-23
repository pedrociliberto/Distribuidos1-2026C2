## Clase 9 - Distribución y Coordinación de Procesos

### Coordinación de Actividades

- **Coordinación**: se despachan tareas distintas, cada una se ejecuta, y luego se consolidan en un solo resultado.
- **Replicación**: al despachar se ejecuta la misma tarea varias veces, y luego se consolidan. Tener réplicas es seguro en caso de fallas.
- **Acceso a recursos compartido**: se serializan de forma secuencial y luego se ejecutan uno a uno.

### MapReduce

- ***Parallel Computing***: partir procesamiento en partes que puedan ser ejecutadas concurrentemente en múltiples cores.
- No todos los problemas pueden ser paralelizados, y la concurrencia implica sincronización y retención de procesos.
- Identificar **tareas** que se ejecuten en paralelo, y **grupos de datos** que puedan ser procesados en paralelo.
- Se desarrolla en 2004 por Google.
- Funciones **`map`** y **`reduce`**.

#### Parallel Computing - Caso ideal

- Cuando los datos pueden ser partidos, cada proceso puede trabajar con un ***chunk/chard***.
- **Master**: encargado de partir la data en *chunks*, y enviar sus ubicaciones a los Workers.
- **Workers**: reciben ubicación de los *chunks*, los procesan, y envían la ubicación del resultado al Master.
#### Función `map`

- Data es particionada en $K$ *chunks* y procesada por $M$ workers, que ejecutan la función `map`.
- Esta función es proporcionada por el usuario, y ejecutada en todos los *chunks* de data.
- El **usuario decide cómo filtrar** la data provista en los *chunks*.
- Se agrupan todos los valores asociados con una misma *key*.
- Lo ideal tendría ser tantos Mappers como *chunks* ($M=K$).
#### Función `reduce`

- La función `reduce` realiza una **agregación** de los datos para obtener un resultado final.
- Es llamada por cada ***unique key***.
- Realiza un merge de los datos recibidos para formar un **set de datos menor**.
- Es distribuida particionando las $L$ *keys* en $R$ Reduce Workers, y ese $R$ se especifica por el usuario.
-  Lo ideal sería tener tantos Reducers como *keys* ($R=L$).