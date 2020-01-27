# Workshop de Postman
En este workshop haremos un repaso de las funcionalidades que nos ofrece la herramienta Postman a la hora de abordar las pruebas de un API REST.
Partiremos de los conceptos más básicos hasta llegar a casos de uso más complejos.

## Entornos y variables
Postman permite la gestión de diferentes entornos. Podemos entender un entorno como un contener de variables. 

Un ejemplo típico podría ser declarados los entornos de Pruebas, Preproducción y Producción y las variables correspondientes en cada uno de los casos. Como por ejemplo: URL, usuarios, claves, tokens, IPs, etc..

Tener las peticiones y los test parametrizados resulta muy útil que nos permitirá ejecutar las mismas pruebas sobre diferentes entornos con muy poco esfuerzo de mantenimiento.

### Acceso a variables desde peticiones
Desde la URL, parámetros o body de las peticiones podremos acceder a las variables del los entornos mediante: <b>{{NombreVariable}}</b>

### Acceso a variables desde los testing
Desde los scripts de test podremos acceder acceder a las variable mediante: 

`pm.environment.get("variable_key");
`
## Debugging
Abriendo la consola de Postman podremos visualizar el detalle de todas las peticiones y acciones realizadas.
Esto resulta muy útil cuando queremos analizar datos concretos de las peticiones y de sus respuestas (cabeceras, códigos de retorno, encoding, cookies, etc). 
Por otro, la consola nos será de gran ayuda cuando estemos depurando nuestros tests, ya que nos mostrará la información que incluyamos como "<b>console.log()</b>"

## Asertos
Postman utiliza la librería 'Chai' para implementar los asertos, por tanto, podremos utilizar cualquiera de sus [posibilidades](https://www.chaijs.com/api/bdd/)

La manera de añadir tests a nuestra peticiones es mediante el comando pm.tests. Por ejemplo así:

```
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

[Ejemplos de test en Postman](https://learning.getpostman.com/docs/postman/scripts/test-examples/)

## Preparación de la prueba
El flujo de ejecución de las peticiones en Postman es el siguiente:

`Pre-request Script - Request - Tests`

En muchas ocasiones, nos será muy útil introducir scripts en la parte de PreRequest para prepara la prueba. Ya sean variables, entornos, datos etc. Esto lo podemos hacer de varias maneras, pero pueden existir situaciones donde la propia preparación de la prueba requiera realizar una llamada HTTP a otro servicio.

Desde Postman, podremos realizar estas acciones de la siguiente manera:

```
pm.sendRequest(baseURL + "/users/1", function (err, response) {   
    const resp = response.json();  
    pm.environment.set("first_email", resp.data.email);
    console.log(resp);
}); 
```

## Random Testing
En algunas ocasiones puede resultar interesante realizar pruebas con datos pseudo-aleatorios para así introducir una incertidumbre controlada en nuestros casos de prueba. 

Puede ser útil en diferentes tipos de escenarios: cuando el valor concreto de los datos no sea importante (ids, fechas, números de teléfono, etc.) o cuando siempre importante, nos gustaría añadir cierta incertidumbre al test para comprobar que el sistema se comporta correctamente de manera consistente.

Postman ofrece multitud de datos pseudo-aleatorios que podemos [utilizar](https://learning.getpostman.com/docs/postman/variables-and-environments/variables-list/), como por ejemplo:

{{$randomZipCode}}

{{$randomFirstName}}

{{$randomJobType}}

{{$randomProductName}}

{{$randomMonth}}

...


## Data Driven Testing
Como su nombre indica el Data-Driven Testing se basa en la idea de que sean los propios datos los que dirijan nuestras pruebas. Lo más habitual en estos casos es definir los inputs y resultado esperado en forma de tabla, donde cada una de las filas representa un caso de prueba.

Postman permite este moto de ejecución gracias a la variables y a la lectura de ficheros JSon y csv de manera automática.

Para ello, en primer lugar debemos hacer que la aparezca las variables entre llave '{{nombreVariable}}' y en el fichero que utilicemos de entrada, deben aparecer nombres de campos con el mismo nombre. Posteriormente, desde el runner de Postman indicaremos el nombre del archivo a utilizar durante la prueba.

También, desde el script de test podremos acceder a los datos del fichero mediante el comando: <b>pm.iterationData.get("nombreVariable")</b>

## Línea de comandos
Para ejecutar las pruebas de Postman desde línea de comandos debemos hacerlo mediante el comando "newman".

Newman es un paquete de NodeJs, por tanto, debemos tenerlo instalado previamente. [Más info](https://learning.getpostman.com/docs/postman/collection-runs/command-line-integration-with-newman/)

Una vez tenemos los necesario podremos ejecutar nuestra colección de dos maneras: directamente con el ficheros JSon previamente exportado o haciendo referencia a su URL (de Postman o de cualquier otro servidor)

`$ newman run mycollection.json -e MiEntorno.json
`

`$ newman run https://MiColeccion -e https://MiEntorno
`
## CI con Jenkins
Uno de los servidores más utilizados para tareas de integración continua es Jenkins. Éste, no deja de ser un gestor de tareas, por lo que podremos hacer que las pruebas de APIs se ejecuten como paso de nuestro pipeline de integración.

Para probar el ejemplo:
- 1 Construir la imagen docker: Contiene Jenkins con nodeJS y newman instalado

`docker build --rm -f "Dockerfile" -t jenkinsnewman "."
`
- 2 Ejecutar la imagen creada

`docker run --rm -d -p 50000:50000/tcp -p 8080:8080/tcp jenkinsnewman:latest
`
- 3 Conectarse a localhost:8080 , completar la instalación de Jenkins 
- 4 Crear una tarea que contenta la ejecución de un comando shell
- 5 Introducir el comando newman para ejecutar las pruebas en modo consola. Por ejemplo:
  
 ```
newman run https://raw.githubusercontent.com/morvader/PostmanWorkshop/master/Req-ResAPI.postman_collection.json \
   -e https://raw.githubusercontent.com/morvader/PostmanWorkshop/master/Req-RES.postman_environment.json
 ```

## Reporting
Al ejecutar las pruebas desde Newman, veremos el resultado de las mismas en pantalla. Pero si además queremos generar un informe debemos especificar su formato al lanzarlas. Mediante la opción "-r" , podremos indicar tanto formato como ruta de destino. [Más info](https://www.npmjs.com/package/newman#reporters)

Uno de los más interesantes y útiles es el desarrollado por Danny Dainton y que está disponible en [GitHub](https://github.com/DannyDainton/newman-reporter-htmlextra)

## Docker
Podremos ejecutar las pruebas por línea de comandos utilizando la imagen docker [oficial](https://github.com/postmanlabs/newman/tree/develop/docker/).

Para ello, podemos utilizar el comando:

```
docker run -v ${PWD}:/etc/newman --rm -t postman/newman:ubuntu run https://raw.githubusercontent.com/morvader/PostmanWorkshop/master/Req-ResAPI.postman_collection.json -e https://raw.githubusercontent.com/morvader/PostmanWorkshop/master/Req-RES.postman_environment.json
```

Para ficheros locales:
- En Windows
```
docker run --rm -v ${PWD}:/etc/newman -t postman/newman:ubuntu run 'Req-ResAPI.postman_collection.json' -e 'Req-RES.postman_environment.json'
```

