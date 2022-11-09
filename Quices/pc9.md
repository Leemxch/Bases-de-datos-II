# Prueba corta #9
## Max Richard Lee Chung - 2019185076
1. Suponiendo que un sistema de bases de datos relacional que presenta un read-heavy workload y los queries son muy diferentes, explique detalladamente ¿porque el uso de caches puede afectar el rendimiento del sistema de forma negativa? (30 pts)

El uso del caché puede afectar negativamente al rendimiento porque las lecturas en disco tienen un gran costo de tiempo dado que debe de buscar la tabla y extraer la información, por lo que atrasa las siguientes lecturas de la cola. De esta forma, la carga de trabajo aumenta según las peticiones diferentes de información que entran, dado que es diferente a la que estaba cargada previamente, entonces debe de volver a revisar las tablas y conseguir los nuevos datos sucesivamente. 

2. El particionamiento de tablas en bases de datos relacionales es un concepto muy parecido al de shards en bases de datos NoSQL, explique detalladamente ¿Cómo afecta el particionamiento y el sharding en el rendimiento de bases de datos SQL y NoSQL? (30 pts)

El particionamiento y sharding afecta el rendimiento a las bases de datos por su conectividad entre las máquinas, por lo que se debe de tener un buen ancho de banda para poder ubicar, leer, escribir y guardar la información en su respectivo grupo de tabla o máquina encargada. Además, si tiene una consistencia fuerte, relentiza aún más el proceso por las esperas en la cola para dejar los registros correctos.

3. En un sistema de bases de datos con Strong Consistency cuyo workload es de read-heavy y write-heavy, ¿Cómo afectan los exclusive locks el rendimiento de las bases de datos NoSQL? (20 pts)

Puede afectar altamente en el rendimiento de las bases dedatos noSQL, ya que al tener un bloqueo exclusivo, podría perfectamente realizar todas las lecturas en la cola. No obstante, si la petición está en modo de escritura, no se puede escribir ni leer porque se debe de terminar el proceso anterior para liberarlo y seguir con el flujo. 

4. Explique detalladamente, ¿Cómo afecta la selección de discos físicos el rendimiento de una base de datos SQL y NoSQL? (20 pts)

La selección de discos físicos afecta el rendimiento dependiendo de los datos a almacenar (hot, warm, cold, frozen), ya que se pueden crear espacios para cada tipo necesitado, como por ejemplo, los discos hot, tienden a utilizar alta RAM y memoria para cambiar constantemente datos. Si se utiliza una base de datos en la nube, la localidad afecta exponencialmente el rendimiento con respecto al tiempo de respuesta al usuario y exportación de información hacia otros data centers.