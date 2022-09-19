# Resumen 4

> Fecha de entrega: 21/09/22 7:25 pm
> 
>>
* Max Richard Lee Chung - 2019185076

### Introduction
Subsistema de índices DocumentDB. El subsistema de índices ocupa soportar índices automáticos de documentos sin tener que usar esquemas o índices secundarios, lenguajes de consultas DocumentDB, consultas consistentes a tiempo real para procesar documentos pesados y multiservicios en recursos extremadamente frugales en donde se mantenga un rendimiento alto y efectivo. 

Está basado en el modelo de datos JSON y lenguaje de JavaScript en el mismo motor de bases de datos.
Capacidades: 
* Soporta consultas jerárquicas y relacionales. 
* El motor está optimizado para consultas de documentos de escrituras grandes. De forma predeterminada, el motor de la base le proporciona a todos los datos automaticamente un índice.
* Ejecución transaccional por medio de procedimientos almacenados y disparadores (triggers). 
* Ofrece la opción de escoger o personalizar el nivel de consistencia.
* Todas las máquinas y recursos son abstractos para todos los usuarios. 
* Permite que la habilidad de escalar elásticamente el rendimiento y almacenamiento con costos eficientes.

Un usuario de DocumentDB inicia con una cuenta proporcionada. Una cuenta maneja una o más bases de datos de DocumentDB. A su vez, gestiona los usuarios, permisos y colecciones. Una colección es un contenedor independiente del esquema de datos arbitrarios generados por usuarios, además gestiona los procedimientos almacenados, disparadores, funciones definidas de usuarios y archivos adjuntos. Todos las cuentas, bases de datos, usarios, colecciones, entre otros son recursos representados como un documento JSON. Los desarrolladores pueden interactuar con los recursos en HTTP usando la sintáxis estándar del CRUD, consultas y procedimientos almacenados. Se pueden crear particiones de los nuevos recursos para tener una escabilidad elástica. Cada partición tiene un único sistema de imágen y una réplica set. 

El servicio se encuentra desplegado en múltiples regiones Azure. El servicio se desplega y gestiona en un clústers en máquinas locales, cada una con su memoria dedicada. Luego de ser desplegado, se manidiesta como una red de máquinas (federaciones) el cual se extiene en una o más clústers. Las réplicas correspondientes a las particiones de recursos se colocan y equilibran la carga entre las máquinas de la federación. El motor de la base de datos DocumentDB, a su vez, consta de componentes que incluyen la máquina de estado replicada (RSM) para la coordinación, el tiempo de ejecución del lenguaje JavaScript, el procesador de consultas y los subsistemas de almacenamiento e indexación responsables del almacenamiento transaccional y la indexación de documentos.

### Schema agnostic indexing
El esquema del documento describe la estructura y el tipo de sistema del documento independiente de la instancia del documento. La simplicidad de la gramática de JSON es una de las razones de su adopción a pesar de la poca especificación del esquema. Esto permite procesar documentos sin asumir el tipo ya que cada documento puede variar. El sistema opera a nivel de la gramática JSON. 

La técnica que ayuda a desdibujar el límite entre el esquema de los documentos JSON y sus valores de instancia es representar los documentos como árboles. Esto permite normalizar la estructura y los valores intanciados en un concepto unificador de una estructura de ruta codificada dinámicamente. Cada etiqueta es un nodo del árbol y el contenido de cada etiqueta es un nodo hijo.

Uno de los requisitos principales de la indexación automática de documentos es garantizar que el costo de indexar y consultar un documento con una estructura profundamente anidada sea el mismo que el de un documento JSON plano que consta de pares clave-valor de solo un nivel de profundidad. Por lo tanto, una representación de ruta normalizada es la base sobre la cual se construyen tanto la indexación automática como los subsistemas de consulta. Hay dos asignaciones posibles de documentos y rutas: mapeo de índice hacia adelante (id, ruta) y mapeo de índice invertido (ruta, id). Dado que el lenguaje de consulta DocumentDB opera sobre las rutas de los árboles de documentos, el índice invertido es una representación muy eficiente. El índice del árbol crece dependiendo de cuántos documentos nuevos sean añadidas. 

### Logical index organization


### Physical index organization


### Insight from the production worloads


### Related Commercial systems


### Referencia
* Microsoft Corporation. Schema-Agnostic Indexing with Azure DocumentDB. 