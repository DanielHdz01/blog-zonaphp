---
author: Nok
comments: true
date: 2003-03-04 20:31:43+00:00
layout: post
slug: noktemplate-facil-rapida-y-en-castellano
title: NokTemplate. Fácil, rápida y en castellano.
wordpress_id: 12
categories:
- Templates
---

En esta serie de artículos, trataré de explicarles como utilizar plantillas o templates en sus aplicaciones por medio de la clase [NokTemplate](http://www.jpw.com.ar/index.php?lugar=noktpl). El uso de plantillas es una técnica bastante utilizada por los desarrolladores de grandes y complejas aplicaciones, pero no por esto tiene que ser utilizada en aplicaciones complejas, sino que me parece una buena práctica para hacer nuestras aplicaciones más profesionales. En esta oportunidad empezaremos viendo las bases y fundamentos de esta técnica, para esto y durante toda la serie nos basaremos en un motor de Templates que es relativamente nuevo, fácil de usar y está en castellano. Cabe destacar que yo soy el creador de esta clase y que trataré de ser lo más objetivo posible.<!-- more -->

**¿Cuál es la idea?**

La idea de trabajar con templates o plantillas es de separar la aplicación (codigo php, jsp, o sino tienen otra asp) del diseño gráfico (Html, Css, Javascript, etc.), para que luego si en un futuro es necesario cambiar el diseño del sitio, no sea necesario cambiar la aplicacion. Lo mismo pasa, si se cambia algo en la aplicacion, de esta manera no tendremos de preocuparnos por el diseño del html sino que solamente de que el codigo funcione. Esto es bastante relativo y depende de como se trabajo en un principio, ya que hay casos en los que si o si es necesario cambiar la aplicación, porque no se plantearon bien las cosas desde un principio.

Las plantillas, como la palabra lo dice son plantillas, o sea archivos de código HTML que contienen el "molde" de un sitio, ya sea el cuerpo, encabezado, pie, etc.. Estas plantillas pueden ser editadas por cualquier editor HTML, y en general estan a cargo del equipo de diseño grafico (siempre y cuando estemos hablando de un equipo de desarrollo sino, nosotros hacemos ambas cosas.). En estas plantillas se definen "variables" propias del template que luego serán manejadas desde la aplicacion.
Por otro lado esta el codigo de la aplicacion que aparte de hacer las consultas a bases de datos y todo lo que la aplicacion involucra, maneja la interface por medio del motor de templates sin preocuparse del diseño o html.

Todo muy bonito, pero que gano y que pierdo con esta técnica?. Bueno, como en la vida no se puede ganar siempre, tenemos algunos pros y algunas contras:

**Ventajas:**



	
  * Independencia entre la aplicacion y la interface. Lo recomendado en todo tipo de software.

	
  * Puedes rediseñar tu sitio sin tener cambiar practicamente nada de tu código.

	
  * Las actualizaciones a tu sitio seran mas faciles de realizar. Solamente cambias el contenido y no el diseño.

	
  * El mantenimiento del código es más facil y rápido. No tienes que preocuparte por el Html.


**Desventajas:**



	
  * Puede que programar utilizando Templates se torne un poco más pesado. Pero lo Vale.

	
  * El tiempo de procesamiento del Template puede hacer caer el rendimiento de tu sitio. La utilización de un sistema de cache puede solventar la perdida de rendimiento.


Como ven no es tanto.
**Conociendo a NokTemplate 1.2**

Bueno, algunas de las caracteristicas de la Clase que utilizaremos en este articulo son:



	
  * Modo Debug. Lo que posibilita la optimización de tu script.

	
  * Sistema de Cache temporal en archivo Externo. Lo que incrementa considerablemente la velocidad de procesamiento.

	
  * Velocidad de interpolación. Utiliza expresiones regulares compatibles con Perl, mucho mas veloces que las Posix.

	
  * Fue desarrollada para PHP 4.x. Lo que la hace superior sobre su par FastTemplate que fue desarrollada para PHP 3.

	
  * Es compatible con FastTemplate. Utiliza la misma lógica.

	
  * Soporta definición de bloques anidanos dentro de las plantillas.


Estas son a grandes rasgos las caracteristicas de una clase tipica, otras como Smarty tienen más herramientas y por lo tanto son mas complicadas y se utilizan para proyectos de gran envergadura. Por ahora no he recibido quejas de los usuarios de la clase, por lo que supongo que para aplicaciones de tamaño mediano y chico cubre todas las espectativas.

La logica que utiliza NokTpl es sencilla:

	
  * Cargar Templates/Plantillas.

	
  * Asignar valores a las variables.

	
  * Expandir Templates/Plantillas (analizar el contenido).

	
  * Mostrar el resultado.


Con esos 4 simples pasos ponemos formar nuestra primera pagina utilizando templates, que por lo que ven no es muy complicado.
Podran bajarse esta clase desde [http://www.jpw.com.ar/noktemplate](http://www.jpw.com.ar/noktemplate)
**Nuestro primer ejemplo**

Bueno, para iniciarnos en el mundo de los templates vamos a partir con un ejemplo bien sencillo. Antes de empezar, nos falta saber como se definen las variables (anteriormente nombradas). Las variables dentro de las plantillas se definen entre llaves ('{' y '}') y puede contener letras mayusculas, minusculas, numero y underscore ('_'). Es importante tener en cuenta que las variables son sencibles a las mayusculas y minusculas, por lo que no es lo mismo {MiVariable} que {MIVARIABLE}. Sabiendo eso, pongamos manos a la obra:

Para nuestro primer ejemplo utilizaremos 2 templates: **cuerpo.html** y **contenido.html** (la extension del archivo template puede ser .tpl o cualquier otra, en mi caso prefiero .html porque es mas facil de relacionar con el editor HTML)

**_cuerpo.html_**`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
<head>
<title>{TITULO} - Powered by NokTemplate</title>
</head>
<body>
{CONTENIDO}<br />
</body>
</html>`

**_contenido.html_**`Hola, mi nombre es {NOMBRE}.`

Bien, como se darán cuenta tenemos 2 variables en una plantilla (_{TITULO}_ y _{CONTENIDO}_) y 1 en la otra (_{NOMBRE}_). Estas variables serán referenciadas desde nuestra aplicación que veremos a continuacion.

[php]< ?php
// Incluimos la Clase obviamente.
include ('Class.NokTemplate.php');

// Creamos una instancia del objeto.
// Definimos el lugar donde se encuentran los templates, en este caso './templates'.
$html = new NokTemplate('./templates');

// Cargamos los templates necesarios y le asignamos una clave,
// o sea tCuerpo hace referencia a cuerpo.html y tContenido a contenido.html
$html->cargar('tCuerpo','cuerpo.html');
$html->cargar('tContenido','contenido.html');

// Asignamos a la variable TITULO un texto. Titulo puede estar definida en
// Cualquiera de las Plantillas.
$html->asignar('TITULO','Ejemplo número 1');

// Hacemos lo propio con Nombre.
$html->asignar('NOMBRE','Nok');

// Ahora, expandimos el contenido del template tContenido con sus
// variables ya asignadas, es decir, al hacer esto todo el contenido de la
// Plantilla cuerpo.html se "asignará" a la variable contenido, con la
// Salvedad que las variables que esten definidas dentro de la plantilla
// Se les asignará su valor. O sea NOMBRE = 'Nok'.
$html->expandir('CONTENIDO', 'tContenido');

// Para ir terminado expandimos el contenido del template tCuerpo
// en una variable cualquiera, que puede no estar en ningun Template.
// Simplemente para que se asignen los valores a las variables definidas
// dentro de tCuepo. O sea: TITULO='Ejemplo número 1' y
// En CONTENIDO se vuelca lo que recien expandimos.
$html->expandir('FINAL', 'tCuerpo');

// Y por ultimo imprimimos la varible que contiene todo ya procesado.
// En este caso se llama FINAL pero puede ser cualquiera.
$html->imprimir('FINAL');
?>[/php]

Bien, ese fue el primer ejemplo con templates. Al final de la ejecución del script obtendremos algo asi:

`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.0 Transitional//EN">
<html>
<head>
<title>Ejemplo número 1 - Powered by NokTemplate</title>
</head>
<body>
Hola, mi nombre es Nok.<br />
</body>
</html>`
**Construyendo tablas**

Ahora que hemos entrado en el mundo de los templates, vamos a hacer algo un poco mas complejo y util: las archiconocidas tablas. Para hacer una tabla utilizando templates necesitaremos de 2 plantillas más, una para la estructura de la tabla y otra para las filas de la tabla. Estas serán tabla.html y fila.html. En nuestro ejemplo llenaremos la tabla con nombres y apellidos de personas. Recordemos que tambien utilizaremos la plantilla cuerpo.html del ejemplo anterior.

**_tabla.html_**`<table>
<tr>
<td>Nombre:</td>
<td>Apellido:</td>
</tr>
{FILAS}
</table>`

**_fila.html_**`<tr>
<td>{EL_NOMBRE}</td>
<td>{EL_APELLIDO}</td>
</tr>`

Bien, como se habrán dado cuenta, tenemos 3 variables, FILAS en tabla.html y El_NOMBRE y EL_APELLIDO y como su nombre lo indica serán utilizadas para las filas, nombre y apellido. Ahora veremos un poco el código para formar las tablas.

[php]< ?php
// Incluimos la Clase.
include ('Class.NokTemplate.php');

// Creamos una instancia del objeto.
// Definimos el lugar donde se encuentran los templates.
$html = new NokTemplate('./templates');

// Cargamos los templates necesarios y le asignamos una clave,
// o sea tTabla hace referencia a tabla.html y tFila a fila.html
$html->cargar('tCuerpo','cuerpo.html');
$html->cargar('tTabla','tabla.html');
$html->cargar('tFila','fila.html');

// Asignamos a TITULO un texto.
$html->asignar('TITULO','Ejemplo número 2');  

// Formaremos una tabla a partir de un array asociativo.
// Que contiene la informacion de las personas. Este podría ser el
// caso de el result de una consulta a un motor de base de datos.
$datos = array('Jose' => 'Perez',      'Carlos' => 'Gonzales',      'Anibal' => 'Hugo');    

// Con un bucle formamos la tabla.  
foreach ($datos as $nombre => $apellido)
{  
	// Asignamos nombre y apellido correspondientemente  
	$html->asignar('EL_NOMBRE', $nombre);  
	$html->asignar('EL_APELLIDO', $apellido);      
	
	// Ahora al expandir la variable FILAS con el template tFila  
	// le indicamos por medio del signo '+' o '.' que le  
	// agregue o concatene al contenido ya existente en FILAS el contenido de tFila  
	// con sus respectivas variables expandidas. O sea, vamos agregando filas a la tabla
	// a medida que se ejecuta el bucle
	$html->expandir('FILAS', '+tFila');  
}

// Una vez armada la tabla, volcamos el contedido de la tabla
// dentro de la variable CONTENIDO  
$html->expandir('CONTENIDO', 'tTabla');

// Para ir terminado expandimos el contenido del template tCuerpo
// en una variable cualquiera, que puede no estar en ningun Template.
// Simplemente para intercambiar los valores de las variables que contenga
// tCuepo, que en este caso son TITULO y CONTENIDO.
$html->expandir('FINAL', 'tCuerpo');

// Y por último imprimimos la varible que contiene todo ya procesado.
$html->imprimir('FINAL');
?>[/php]

Bien, con eso hemos armado una tabla utilizando simplemente 2 plantillas. Es evidente que a estas plantillas se les puede modificar el aspecto para hacerlas mas bonitas, y nuestro código seguira funcionando sin problemas, lo único que hay que respetar son las variables definidas en los templates.
**Partiendo en bloques**

Hasta ahora utilizamos plantillas que se encontraban en archivos separados, pero que sucede si queremos, para falicitar el diseño, trabajar con varios templates en un mismo archivo?. Por ejemplo una tabla, en el ejemplo anterior utilizamos 2 plantillas separadas para poder recrear la tabla, lo que imposibilita ver "todo junto" en el diseño. Para solvertar estos, NokTemplate brinda la posibilidad de definir Bloques dentro de las plantillas para trabajar como si cada uno de estos bloques fueran templates en si mismos. En la ultima actualizacion de la clase se posibilita la definición de bloques anidados, es decir uno dentro de otro, lo que facilita la visualización en el editor HTML.

Para definir un bloque dentro de una plantilla o template, se utilizan unos tags especiales ya predefinidos por NokTemplate, estos son **** para el inicio del bloque y **** para el fin. Presten atencion al fotmato de los tags, ya que debe ser de esa manera unicamente, respetando espacios, mayúsculas y minúsculas, porque de otra manera NokTpl no reconocera los bloques como tales. Esta restriccion es por motivos de eficiencia en el uso y no abuso de las expresiones regulares, simplemente por eso.
Bueno, veamos como se constituyen los bloques dentro de nuestra plantilla, en este caso utiltabla.html que contiene 2 bloques anidados que nos servirán para formar nuevamente la tabla del ejemplo anterior. Recordemos tambien que nuevamente utilizaremos la plantilla cuerpo.html.

**_utiltabla.html_**`<!-- inicioBloque: tTabla -->
<table>
<tr>
<td>NOMBRE</td>
<td>APELLIDO</td>
</tr>
{FILAS}
   <!-- inicioBloque: tFila -->
   <tr>
       <td>{EL_NOMBRE}</td>
       <td>{EL_APELLIDO}</td>
   </tr>
   <!-- finBloque: tFila -->
</table>
<!-- finBloque: tTabla -->`

Una vez que tenemos tenemos las plantilla con sus respectivos bloques dentro, pasemos al código:

[php]< ?php
// Incluimos la Clase.
include ('Class.NokTemplate.php');

// Creamos una instancia del objeto.
// Definimos el lugar donde se encuentran los templates.
$html = new NokTemplate('./templates');

// Cargamos los templates necesarios y le asignamos una clave,
// o sea tCuerpo hace referencia a cuerpo.html y tUtilTabla a utiltabla.html
$html->cargar('tCuerpo','cuerpo.html');
$html->cargar('tUtilTabla','utiltabla.html');

// Aqui viene lo diferente:
// Una vez cargados los templates, definimos los bloques.
// Le indicamos que existen dos bloques dentro de tUtilTabla.
// tTabla y tFila. A partir de ahora, estos seran tratados como templates.
$html->definirBloque('tTabla', 'tUtilTabla');  
$html->definirBloque('tFila', 'tUtilTabla');

//Luego el código sigue practicamente igual

// Asignamos a TITULO un texto.
$html->asignar('TITULO','Ejemplo número 3');  

// Formaremos una tabla a partir de un array asociativo.
// Que contiene la información de las personas. Este podría ser el
// caso de el result de una consulta a un motor de base de datos.
$datos = array('Jose' => 'Perez',      'Carlos' => 'Gonzales',      'Anibal' => 'Hugo');    

// Con un bucle formamos la tabla.  
foreach ($datos as $nombre => $apellido)
{  
	// Asignamos nombre y apellido correspondientemente  
	$html->asignar('EL_NOMBRE', $nombre);  
	$html->asignar('EL_APELLIDO', $apellido);      
	
	// Ahora al expandir la variable FILAS con el template tFila  
	// le indicamos por medio del signo '+' o '.' que le  
	// agregue o concatene al contenido ya existente en FILAS el contenido de tFila  
	// con sus respectivas variables expandidas. O sea, vamos agregando filas a la tabla
	// a medida que se ejecuta el bucle
	$html->expandir('FILAS', '+tFila');  
}

// Una vez armada la tabla, volcamos el contedido de la tabla
// dentro de la variable CONTENIDO  
$html->expandir('CONTENIDO', 'tTabla');

// Para ir terminado expandimos el contenido del template tCuerpo
// en una variable cualquiera, que puede no estar en ningun Template.
// Simplemente para intercambiar los valores de las variables que contenga
// tCuepo, que en este caso son TITULO y CONTENIDO.

$html->expandir('FINAL', 'tCuerpo');

// Y por último imprimimos la varible que contiene todo ya procesado.
$html->imprimir('FINAL');
?>[/php]

Como ven no cambia mucho el manejo entre tener bloques o archivos separados, por lo que todo depende que es lo que se necesite. Por ejemplo los bloques se pueden utilizar para ubicar dentro de un solo archivo varios bloques que son muy chicos y que no vale la pena tenerlos en archivos separados, por ejemplo mensajes largos de texto, que son engorrosos de poner en el código de la aplicación, tal es el caso de "_Gracias por suscribirse a... etc._" y otros mensaje que es mas como tenerlos guardados en archivos que ponerlos en la aplicación.
**Conclusión**

Bien, esta fue la primera parte de una serie de articulos acerca de como utilizar templates en sus aplicaciones web. En esta ocasion vimos la idea de la tecnica y las operaciones basicas que nos proporciona NokTemplate. En las proximas entregas seguiremos analizando un poco mas a fondo las herramientas que brinda NokTpl y finalizaremos esta serie con una aplicacion completa utilizando este Motor para el manejo de templates. Espero les sea de utilidad y que empiecen a adentrarse en el mundo de los templates haciendo pruebas con aplicaciones sencillas, pronto descubriran la utilidad y practicidad de esta tecnica. Los espero en el proximo artículo.

Mas info:
    **NokTemplate** - [http://www.jpw.com.ar/noktpl.php](http://www.jpw.com.ar/noktpl.php)
    **FastTemplate** - [http://www.thewebmasters.net](http://www.thewebmasters.net)
