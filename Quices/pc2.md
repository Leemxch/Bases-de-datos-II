# Prueba corta #2
## Max Richard Lee Chung - 2019185076
1. Explique el concepto de shard, replica y partition.
* Shard: Se da cuando se quieran hacer grupos de bases de datos más pequeños de una sola base de datos grande.
* Replica: Las réplicas son la cantidad de trabajadores que tiene un pod de Kubernetes.
* Partition: Se da cuando se quieran crear subgrupos de tablas de una tabla larga de datos.

2. Explique la diferencia entre Strong Consistency Eventual Consistency.
* Strong consistency: Se puede forzar. Obligar que se lea sólo si ya está escrito en ambos bases de datos (réplica o esclavo). 
* Eventual consistency: Si hay 1 o más bases de datos, un write ocupa n segundos.

3. ¿En que consiste warm replicas y hot replicas?
* Warm replicas: Sirven para almacenar datos que se utilizan una vez por semana, de los cuales son datos recientes pero no importantes. Ocupan bajo rendimiento de CPU y RAM pero alto consumo de disco.
* Hot replicas: Almacena el último dato más reciente, el cual pasa cambiando conforme un nuevo dato ingrese. Ocupan alto rendimiento de CPU, RAM y disco.  

4. ¿En que consiste consiste switch over y fail over?
* Fail over: Detecta automáticamente la caída de una aplicación para avisar el estado de la misma.
* Switch over: Realiza lo mismo que el fail over pero cambia el servidor de forma manual para adoptar la aplicación caída.
