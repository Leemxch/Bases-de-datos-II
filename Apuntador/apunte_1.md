# Apunte #1 
## Max Richard Lee Chung - 2019185076
> La entrega del Proyecto 1 se traslada para semana 13, martes 18, a la misma hora.

## Índices
Los índices se diferentes en cada sistema, en donde se represetan como árboles binarios y buscan mejorar el rendimiento reduciendo la duración de búsqueda.

### Clustered index
Se crea un único indice dentro del archivo sin importar el órden y dura menos buscando los datos. 

Se debe utilizar dependiendo de la carga de trabajo (workload) del rendimiento, como por ejemplo: 
```
SELECT nombre, edad, fechaN FROM Persona
WHERE edad >= 30 AND provincia = 'x'
```
donde la sentencia tiende a ser muy rápido en modo clustered, sin embargo, si le agregamos a la consulta 
```
ORDER BY provincia
```
aumentará mucho el costo del rendimiento por el órden de las columnas a consultar. Si se hace un "ORDER BY" con columnas fuera de la tabla (INNER JOIN) es lo peor porque hay que hacer un ordenamiento secuencial para revisar las dos tablas. 

### Non-clustered index
Son archivos separados con datos, de los cuales tienen punteros con el siguiente archivo con la información (archivo apunta otro archivo). Funciona como un heap de datos, es decir, entra datos y se pega al final. Normalmente, se tienen que buscar los datos de forma aleatoria, sin embargo hay formas para evitarlo, como por ejemplo, cuando se crea un índice no clúster se usa la palabra reservada INCLUDE como se puede observar en el siguiente ejemplo
```
SELECT col1, col2, col3 FROM table WHERE edad >= 'x'
CREATE INDEX ON edad INCLUDE col1, col2, col3 
```
si crea el indice en edad, se guardan las columnas col1, col2 y col3 para devolver el dato de forma rápida si es una consulta común. 

Esto puede afectar a la lectura y escritura, ya que se obliga a escribir en tres archivos diferentes (), guardarlos y actualizarlos.

> Lo ideal con la lectura de datos es insertar la información en memoria principal, con el fin de nunca entrar a disco (consume tiempo tiempo de ejecución)
>>Memory footprint: Trata de tener los datos en disco y qué tanta memoria consume la base de datos.



### Hash index


### Exoression index


### Inverted index
