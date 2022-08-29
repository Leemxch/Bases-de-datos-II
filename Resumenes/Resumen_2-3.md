# Resumen 2 y 3
## Max Richard Lee Chung - 2019185076
### Introduction
Bigtable está diseñado para escalar con confianza a petabytes de datos y muchas otras máquinas. Ha adquirido varios logros tales como aplicabilidad universal, escalabilidad, alto rendimiento y alta disponibilidad. Se utiliza por su variedad de altas demandas de trabajo y configuraciones personalziadas parecido a una base de datos. Ofrece un sistema que permite un control dinámico sobre el diseño y el formato de los datos. Funciona de igual manera con índices de filas y columnas que permiten el formato de datos estructurados o semiestructurados, también los clientes pueden seleccionar los esquemas a utilizar.

### Data Model
Bigtable es una escasa, distruibuida, persistene y multidimensional mapa ordenada, el cual está indexado por una row key, column key y una marca de tiempo en donde cada valor del mapa se encuentra almacenado en un arreglo de bytes no interpretados. 
* Filas: Las llaves de la tabla son strings arbitrarios con una capacidad máximade 64KB de tamaño. Cada lectura o escritura bajo una sola llave es atómica para conocer bien el comportamiento del sistema en presenca de varias actualizaciones en la misma fila. Mantiene el orden de las llaves de forma lexicográfico. El rango de la tabla se realiza por medio de partición dinámica, llamada como "tablet"(unidad del load balancing). Como resultado, las lecturas de pequeños rangos son más eficientes y ocupa la comunicación con pocas máquinas. De esta forma, los usuarios pueden seleccionar las llaves para usar una buena localidad de los archivos para mayor eficiencia de acceso. 
* Columnas: Las columnas son agrupadas en "column families" (unidad básica de control de acceso), en donde normalmente todos los datos son iguales. Estas columnas deben ser creadas antes para poder almacenar y posteriormente, usar los datos. Se intenta que sean pocas las distintas columnas que se utilicen en una tabla y que rara vez cambien durante ejecución. El nombre de una columna se basa en la familia a la cual pertenece y un calificatorio. Toda familia de columna debe de poder ser imprimible pero los calificadores pueden ser strings arbitrarios. El acceso de control y almacenamiento de disco y memoria se realiza en este nivel. 
* Marcas de tiempos: Cada celda en la Bigtable puede contener múltiples versiones del mismo tipo de dato y estas versiones están indexadas por marcas de tiempos (enteros de 64 bits). Los tiempos pueden ser establecidos de forma automática (en microsegundos y que represetan el tiempo real) o de forma manual por medio de una aplicación del cliente. Aplicaciones que requieran evitar colisiones deben de generar marcas únicas de tiempo ellos mismos. Las versiones son almacenadas de forma descendiente para ver los más recientes primero. Para hacer que el manejo de las versiones de datos sea menos exigente, se puede realizar el uso de 2 configuraciones por familia de columnas para que el sistema active el recolector de basura automáticamente. Otra forma es que el cliente especifique mantener cierta cantidad de versiones para eliminar los más viejas.
>>

### API
Ofrece funciones para crear y eliminar tablas y familias de columnas, así como funciones para cambiar el cluster, tablas, meta datos de las columnas y los derechos de control de acceso. Las aplicaciones pueden escribir o eliminar valores, buscar valores de una fila o iterar un subcojunto de datos de la tabla. Bigtable soporta muchas otras características que permiten la manipulación de datos en muchas otras formas complejas, tales como: 
* Transacciones de una fila que lleva a cabo una lectura, modificación y escritura atómica, ya que no soporta transacciones generales de varias llaves. 
* Permite que las celdas puedan usarse como un contador de enteros.
* Permite la ejecución de guiones suministrados por el cliente en espacios habilitados del servidor.

### Building Blocks
Utiliza la distribución del sistema de archivos de Google (GFS, Google File System) para almacenar archivos de datos y registros. Un cluster de Bigtable usualmente opera en un grupo de máquinas compartido que se encuentran en ejecución con una variedad de otras aplicaciones distribuidas y a veces los processos son compartidos entre las aplicaciones. Bigtable depende de un sistema de administración de clústeres para definir los trabajos, administración de recursos en máquinas compartidas, administración de errores y monitoreo de la máquina. Utilza el formato de archivo de Google SSTable, ya que ofrece un mapa persistente, inmutable y ordenada de las llaves a los valores. Las operaciones son proporcionados para buscar el valor asociado con una clave específica y para iterar sobre todos los pares clave/valor en un determinado
rango clave. Internamente, cada SSTable contiene una secuencia de bloques (64KB pero es modificable) y un índice de bloques para localizar cada uno. De forma opcional, una SSTable puede ser completamente mappeado a memoria, el cual permite el escaneo sin tocar el disco. Bigtable utiliza un servicio distribuido persistente y con alta disponiblidad llamada "Chubby", en donde consiste de 5 réplicas (uno máster) y utiliza el algoritmo de "Paxos" para mantener las réplicas en caso de fallos. Los namespaces consisten en directorios o archivos pequeños. Cada directorio o archivo puede ser utilizado como lock. La librería de cliente ofrece almacenamiento en caché consistente de archivos Chubby. Cada cliente usa un servicio y cada sesión expira si no se puede actualizar en el tiempo establecido y pierde el lock. 

### Implementation
Se implemente con tres componentes principales: una librería contectada a todos los clientes, un servidor máster y muchos servidores tablas (pueden ser añadidas o eliminadas de forma dinámica). El máster es el responsable para asiganr tablas a un servidor de tablas, detectar la expiración de un servidor, balanceo de cargas de trabajo, recolector de basura en el GFS y maneja cambios en los esquemas. Los servidores de tablas manejan un conjunto de tablas. Los clientes interactúan directamente con los servidores y no con el máster. 

* Table Location: Se usa la estructura de un árbol B+ de tres niveles. El primer nivel almacena archivos en Chubby que contienen la raíz de la tabla que contienen todas las localizaciones de las tablas en una tabla especial. Guardan la localización bajo una llave de fila codificada de la identificación y el final de la fila. La librería obtiene las localizaciones de forma secuencial y se dan tres casos diferentes: si no se sabe la localización se va a la siguiente tabla, si se encuentra vacío se ocupa 4 viajes de ida y vuelta viajes y si la caché es obsoleto se toma 6 viajes de ida y vuelta. También se almacenan otros datos en las tablas especiales como los registros del historial. 
* Table Assignment: Cada tabla es asignada a un servidor de tablas y el máster realiza un seguiminto de las tablas asignadas y no asignadas para hacer balance de carga de trabajo. Chubby inicializa y obtiene un lock únio en el directorio porque sin él, el servidor se detiene. Si lo anterior ocurre, el servidor trata de conseguir otro lock y si no lo encuntra, no puede volver a servir. El máster se da cuenta si un servidor está activo mediante consultas de estado y si está inactivo, mueve los pedidos a otro servidor no asignado. Cada vez iniciado el administrador del sistema clúster, se usa un único máster lock en Chubby, el máster escanea los directorios de los servidores, comunica todos los involucrados y escanea las tablas especiales para aprender los conjuntos de tablas. El máster monitorea todas las actividades como si se combinan dos tablas o se separan para juntar o dividir las cargas de trabajo. 
* Tablet Serving: Almacena el estado persistente en GFS. Registra los registros en un historial por si es necesario recuperarlos en memoria (memtable) en orden SSTable. La recuperación de datos se realiza a partir de los datos de las tablas especiales de los punteros de rehacer para reconstruir el memtable. Cuando encuentra una función de escritura y escritura, verifica si está bien formado para luego autorizarlo. 
* Compactions: Cuando se llena la memoria, se crea otro espacio de memoria. Los datos del bloque lleno se convierten a una SSTable escrito en GFS. Se realiza este proceso para reducir el espacio y uso de memoria. Luego de compactar muchos bloques a SSTables, se juntan algunos bloques para tener que realizar una lectura de una nueva SSTable (merging compaction para bloques pequeños y major compaction para una sola SSTable).  

### Refinements
Los siguientes refinamientos es para conseguir alto rendimiento, disponibilidad y confiabilidad requerida.
* Locality groups:
* Compression:
* Caching for read performance:
* Bloom filters:
* Commit-log implementation:
* Speeding up tablet recovery:
* Exploiting immutability:

### Performance Evaluation
* Single tablet-server performance
* Scaling

### Real Aplications
1. Google Analytics
2. Googel Earth
3. Personilized Search 

### Lessons 


