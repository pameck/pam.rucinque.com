+++
date = "2016-12-16T21:53:41+11:00"
draft = true
slug = "serverless-101"
title= "Y a todas estas, qué es eso de serverless?"
tags = ["spanish", "tech"]
image = "images/post-cover.jpg"
comments = true
share = true
menu = ""
+++
<br>
_Serverless_ es un concepto tan abstracto como _la nube_ y con un nombre igual de perverso, pero en la práctica es bastante útil.
Este post consiste en una introducción muy simplificada al tema y una mirada a las tecnologías existentes.<!--more-->

_Serverless_ significa, literalmente, -_sin servidor_-, y es allí donde mi problema con el nombre empieza, "_servidor_" puede hacer referencia a:

  * El _backend_ de una aplicación, de acuerdo con los conceptos de la arquitectura cliente-servidor.
  * El computador, la máquina física, donde corren las aplicaciones, donde están instaladas las bases de datos, etc.

Entonces a qué definición se refiere? Pues bueno, a la segunda.

**_Serverless_ es un tipo de arquitectura donde los servidores físicos dejan de existir y el código corre en "ambientes de ejecución"
que administran proveedores como Amazon, Google, IBM, etc.**

Cuando su código es invocado, ya sea por un request HTTP, u otro evento, el ambiente de ejecución se activa,
el código se ejecuta e inmediatamente el ambiente desaparece.

Si el código es invocado mil veces, el proveedor se encarga de escalar y proveer los ambientes de ejecución necesarios para responder
a las mil invocaciones.


#####TODO
- funciones pequeñas y con un único propósito, mientras más rapido se ejecuten, más barato sale. (microservice)
- Ejemplos reales de posibles usos (un proceso que lea un archivo cada cierto tiempo, image thumbnails)

#####TODO

### Ventajas

- Ya no se tiene que preocupar por mantener los servidores donde corre su código. Adiós a instalar software, abrir o cerrar puertos, correr actualizaciones, etc.
- Es horizontalmente escalable, no tiene que preocuparse por clusters, balanceo de cargas, etc.
- Se paga solamente por el tiempo en ejecución, contrario a pagar un computador incluso por el tiempo en que no es usado.

### Desventajas
- Si no se desarrolla con cuidado, su código puede terminar bastante acoplado al proveedor.
- Al ser un servicio tan reciente, los lenguajes que se pueden usar para implementar las funciones están limitados por lo que esté soportado por el proveedor.

### Proveedores

* [Amazon, AWS Lambdas](https://aws.amazon.com/lambda/)
* [Google, Cloud Functions](https://cloud.google.com/functions/)
* [Microsoft, Azure Functions](https://azure.microsoft.com/en-us/services/functions/)
* [IBM, OpenWhisk actions](https://console.ng.bluemix.net/openwhisk/)


## Serverless en AWS
