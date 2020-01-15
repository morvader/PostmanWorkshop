# Workshop de Postman
En este workshop haremos un repaso de las funcionalidades que nos ofrece la herramienta Postman a la hora de abordar las pruebas de un API REST.
Partiremos de los conceptos más básicos hasta llegar a casos de uso más complejos.

## Entornos y variables
Postman permite la gestión de diferentes entornos. Podemos entender un entorno como un contener de variables. 

Un ejemplo típico podría ser declarados los entornos de Pruebas, Preproducción y Producción y las variables correspondientes en cada uno de los casos. Como por ejemplo: url, usuarios, claves, tokens, IPs, etc..

Tener las peticiones y los test parametrizados resulta muy útil que nos permitirá ejecutar las mismas pruebas sobre diferentes entornos con muy poco esfuerzo de mantenimiento.
### Acceso a variables desde peticiones
Desde la URL, parámetros o body de las peticiones podremos acceder a las variables del los entornos mediante: <b>{{NombreVariable}}</b>

### Acceso a variables desde los testing
Desde los scripts de test podremos acceder acceder a las variable mediante: <b>pm.environment.get("variable_key");</b>

## Debugging
Abriendo la consola de Postman podremos visualizar el detalle de todas las peticiones y acciones realizadas.
Esto resulta muy útil cuando queremos analizar datos concretos de las peticiones y de sus respuestas (cabeceras, códigos de retorno, enconding, cookies, etc). 
Por otro, la consola nos será de gran ayuda cuando estemos depurando nuestros tests, ya que nos mostrará la información que incluyamos como "<b>console.log()</b>"

## Asertos


## Preparación de la prueba

## Random Testing

## Data Driven Testing

## Línea de comandos

## Reporting
Al ejecutar las pruebas desde Newman, veremos el resultado de las mismas en pantalla. Pero si además queremos generar un informe debemos especificar su formato al lanzarlas. Mediante la opción "-r" , podremos indicar tanto formato como ruta de destino. [Más info](https://www.npmjs.com/package/newman#reporters)

Uno de los más interesantes y útiles es el desarrollado por Danny Dainton y que está disponible en [GitHub](https://github.com/DannyDainton/newman-reporter-htmlextra)

## Docker
Podremos ejecutar las pruebas por línea de comandos utilizando la imagen docker [oficial](https://hub.docker.com/r/postman/newman/).

Para ello, podemos utilizar el comando:

<b>docker run -t postman/newman:alpine <URL_Coleccion> -e <URL_Entorno> </b>

Para ficheros locales:

<b>docker run -v ~/collections:/etc/newman -t postman/newman:alpine <Coleccion.json> -e <Entorno.json> </b>

