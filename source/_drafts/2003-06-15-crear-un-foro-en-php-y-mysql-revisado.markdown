---
author: webstudio
comments: true
date: 2003-06-15 04:28:50+00:00
layout: post
slug: crear-un-foro-en-php-y-mysql-revisado
title: Crear un foro en PHP y Mysql [revisado]
wordpress_id: 8
categories:
- Principiantes
---

En esta segunda versión del tutorial original, crearemos un foro desde cero aprendiendo a separar la programación del diseño, y modularizando  nuestra aplicación para que sea simple de configurar y de adaptar.<!-- more -->

**La Estructura**

Primero y antes que nada, para aquellos que no deseen tipear todo el contenido del artículo en sus editores de texto, les dejo un link al [código del foro](http://zonaphp.com/archivos/foro.zip) para que descarguen y puedan revisar mientras leen el artículo.

A continuación debemos preparar la tabla en la base de datos que contendrá todos los temas de nuestro foro. Lo que esta tabla tendrá, es la información de nuestros mensajes, el autor del mismo, y otros datos que servirán para identificar a que Tema pertenece un mensaje. Pero mejor, veamos la estructura propuesta :

[sql]CREATE TABLE `foro` (
`id` int(7) NOT NULL auto_increment,
`autor` varchar(200) NOT NULL default '',
`titulo` varchar(200) NOT NULL default '',
`mensaje` text NOT NULL,
`fecha` datetime NOT NULL default '0000-00-00 00:00:00',
`respuestas` int(11) NOT NULL default '0',
`identificador` int(7) NOT NULL default '0',
`ult_respuesta` datetime default NULL,
KEY `id` (`id`)
) TYPE=MyISAM;[/sql]

Veamos en detalle que campos tendrá nuestra tabla de Foros:



	
  * **id** : Será e identificador principal de la tabla. Sirve para diferenciar cada uno de los mensajes.

	
  * **autor** : el autor del mensaje.

	
  * **titulo** : el titulo que tendrá el mensaje. Si este mensaje es el iniciador de un tema, será el que mostrará en el home del Foro.

	
  * **mensaje** : El mensaje en si mismo.

	
  * **fecha** : un campo DATETIME que indicará en que fecha se ha publicado este mensaje.

	
  * **respuestas** : Si este mensaje es el iniciador de un tema, aqui se acumularán la cantidad de respuestas que reciba.

	
  * **identificador** : este campo guarda el valor del id del mensaje que se está respondiendo. Si el mensaje es iniciador de un tema, entonces este campo valdrá cero.

	
  * **ult_respuesta** : si el mensaje es iniciador de tema, aqui se actualizará valor de acuerdo con la fecha de la última respuesta que haya recibido. Sirve para poder mantener un orden en los foros.


La sentencia SQL anterior, pueden guardarla en un archivo.sql y correrla en su base de datos desde el [phpMyAdmin](http://phpmyadmin.sourceforge.net), o si lo prefieren, pueden ingresarla linea por linea en un cliente de texto de mySQL ( como el mySQL monitor). Una vez que hayan creado la tabla en su base de datos, entonces podemos avanzar al siguiente paso.

Antes de ponernos a programar cualquier parte del foro, vamos a encargarnos de hacer un pequeño script que realice una tarea que vamos a repetir mucho, y que es conectarnos a la base de datos. Este script lo incluiremos en cada página en la que tengamos que acceder a la base de datos:

**_configuracion.php_**[php]< ?php
$bd_host = "localhost";
$bd_usuario = "user";
$bd_password = "password";
$bd_base = "nuestra_bd";

$con = mysql_connect($bd_host, $bd_usuario, $bd_password);
mysql_select_db($bd_base, $con);
?>[/php]
Cómo vemos, no hay gran ciencia en este script, tan solo unas variables conteniendo la configuración de nuestra base de datos, y el código mínimo y necesario para conectarnos y guardar el recurso de conexión en una variable **$con** que luego utilizaremos cuando hagamos nuestras consultas.
**Los Templates**

Antes de dar un paso más en la explicación, quiero hacerles unos comentarios respecto al "simple" sistemita de templates que utilizaremos en el foro. Primero, para aquellos que no sepan que es un Template, les recomiendo que lean los artículos [NokTemplates, fácil, rápida y en castellano](http://www.zonaphp.com/index.php?modulo=articulo&accion=leer&id=9) y [Smarty, instalacíon e introducción](http://www.zonaphp.com/index.php?modulo=articulo&accion=leer&id=16) en nuestra sección de [Templates](/categorias/php-avanzado/templates/), por lo menos para que tomen una idea de que son y para que sirven.

Ahora, mi idea al utilizar templates, fue la de que los usuarios, pudieran modificar a su gusto el aspecto del foro, sin por ello tener que tocar el código de la aplicación. Esto es algo que el tutorial anterior no tenía en cuenta e incluía todo el código HTML de salida dentro del código mismo del foro, lo cual no es siempre recomendable. Lo que haremos en este caso es utilizar archivos .html que dentro contendrán solo diseño ( o sea, código HTML ) y en los lugares en los que deseemos colocar contenido "dinámico", utilizaremos el formato simple para mostrar el contenido de variables, esto es : < ?=$variable?>.

Para **"interpretar"** un template, primero lo leemos en memoria utilizando la función **file()** y luego utilizaremos una simple función a la que le pasaremos como parámetro el template y las variables que hay que reemplazar. Para muestra, basta un botón:

**_ejemplo.html_**[php]Nombre: **< ?=$nombre?>**
Edad : **< ?=$edad?>**
Domicilio : < ?=$domicilio?>



* * *

[/php]
**_ejemplo.php_**[php]< ?php
function mostrarTemplate($tema, $variables)
{
//var_dump($variables);
extract($variables);
eval("?>".$tema."< ?");
}

$agenda = array(
"0" => array("nombre"=>"Marcelo", "edad"=>"25", "domicilio"=>"VeraCRuz 342"),
"1" => array("nombre"=>"Alejandra", "edad"=>"18", "domicilio"=>"Los Olmos 67"),
"2" => array("nombre"=>"Micaela", "edad"=>"23", "domicilio"=>"Prof. Mariño 8")
);
$tpl = implode("", file("ejemplo.html"));
foreach($agenda as $registro)
{
mostrarTemplate($tpl, $registro);
}
?>[/php]

Aquí vemos como, partiendo de los datos que tenemos en un array, los mostramos basándonos en un simple template. La función **mostrarTemplate** toma como parámetros el contenido del template y un array asociativo con los valores a reemplazar. La función de PHP **extract()** se encarga de pasar estos valores al ámbito en el que se llama la función, en este caso, el ámbito de la función. Por lo que si tenemos un arreglo del tipo **$a = array("clave" => "valor")**, al ejecutar **extract($a)**, entonces pasaremos a tener una variable disponible, de nombre **$variable** y con "valor" como contenido. Luego, la función **eval()** se encarga del resto, o sea, de ejecutar todo el código (el del template) que se le pasa como parámetro.

Algunos se preguntarán, ¿porqué no hacemos simplemente un "**include**"? La respuesta es muy siemple. Eficiencia. La función include implica un acceso a disco, lo cual la hace bastante "costosa" en términos de eficiencia. Así que leyendo el template una sola vez en memoria, y luego utilizándolo varias veces, hacemos un mejor uso de los recursos.

Teniendo estos temas en cuenta, es que podemos seguir ahora con el desarrollo el Foro.
**Un Tema por vez**

Ya teniendo las bases de nuestro foro, el diseño de la tabla en la base de datos y conociendo como funciona nuestro sistema de templates, podemos comenzar a crear la primera página, en la que mostraremos todos los temas del foro:

**_index.php_**[php]< ?php
require('configuracion.php');
require('funciones.php');
include('header.html');
/* Pedimos todos los temas iniciales (identificador==0)
* y los ordenamos por ult_respuesta */
$sql = "SELECT id, autor, titulo, fecha, respuestas, ult_respuesta ";
$sql.= "FROM foro WHERE identificador=0 ORDER BY ult_respuesta DESC";
$rs = mysql_query($sql, $con);
if(mysql_num_rows($rs)>0)
{
// Leemos el contenido de la plantilla de temas
$template = implode("", file("temas.html"));
include('titulos.html');
while($row = mysql_fetch_assoc($rs))
{
$color=($color==""?"#5b69a6":"");
$row["color"] = $color;
mostrarTemplate($template, $row);
}
}
include('footer.html');
?>[/php]
¿Eso es todo? Si, eso es todo. Ahora revisemos paso a paso lo que hace el script. Primero tenemos una serie de **require**s e **include**s. El primero incluye el primer Script que hicimos, que realiza la conexión a la base de datos. El segundo, incluye un archivo.php que contiene funciones importantes del foro, como por ejemplo, **mostrarTemplate**. El tercero, incluye un header genérico que utilizaremos para darle a todas nuestras páginas, un diseño similar. Allí podremos colocar un logo del sitio, links importantes, banners, etc.

[php]$sql = "SELECT id, autor, titulo, fecha, respuestas, ult_respuesta ";
$sql.= "FROM foro WHERE identificador=0 ORDER BY ult_respuesta DESC";
$rs = mysql_query($sql, $con);
if(mysql_num_rows($rs)>0)
{[/php]

Aqui lo que hacemos es ejecutar un query en la base de datos, que nos traerá todos los  mensajes que son iniciadores de un tema, o sea, cuyo identificador esté en cero. El resto de los mensajes, que sean respuestas a un tema en particular, tendrán en el campo identificador el valor del mensaje al que responden. A estos temas, le pedimos a la base que los ordene por la fecha de última respuesta, de manera descendente, así en nuestro foro, tendremos los mensajes más recientes primero. También realizamos una decisión, solo mostraremos los temas de nuestro Foro si la cantidad de filas recuperadas desde la base, son mayores a 0.

Luego, dentro del While principal del programa, hacemos toda la "magia" :

[php]    // Leemos el contenido de la plantilla de temas
$template = implode("", file("temas.html"));
include('titulos.html');
while($row = mysql_fetch_assoc($rs))
{
$color=($color==""?"#5b69a6":"");
$row["color"] = $color;
mostrarTemplate($template, $row);
}[/php]
Aqui comenzamos a trabajar por primera vez con los templates. Primero leemos el contenido del template en memoria y lo guardamos dentro de la variable **$template**. También incluimos un archivo, que contiene una fila de la tabla de Temas, con los titulos de las celdas. EL resto ya lo vimos anteriormente, llamando a la función **mostrarTemplate** mostramos los datos de cada tema.

Finalmente, solo agregamos otro archivo HTML, con el código para cerrar la página y mostrar algun que otro mensaje de Copyright ( o lo que queramos poner ). Con esto ya tenemos nuestra página inicial del Foro, mostrando los temas que haya. Ahora, vamos a crear el formulario necesario para ingresar nuevos temas o para responder algun tema existente.
**Participar es la Base**

El ahorro es la base de la fortuna, suelen decir, y esto se aplica a casi todo. Así que, haciendo caso al dicho, podemos utilizar el MISMO formulario para crear un nuevo tema y para contestar un tema en particular. Esto lo vamos a lograr, pasando una variable por el URL, indicando que estamos citando un mensaje anterior, sacando de la base de datos el mensaje que citaremos, y completando el formulario con esos datos. Si la variable no está presente, entonces no hacemos nada y mostramos el formulario.

_**respuesta.php**_[php]< ?php
require('funciones.php');
$id = $_GET["id"];
$citar = $_GET["citar"];
$row = array("id" => $id);
if($citar==1)
{
require('configuracion.php');
$sql = "SELECT titulo, mensaje, identificador AS id ";
$sql.= "FROM foro WHERE id='$id'";
$rs = mysql_query($sql, $con);
if(mysql_num_rows($rs)==1) $row = mysql_fetch_assoc($rs);
$row["titulo"] = "Re: ".$row["titulo"];
$row["mensaje"] = "[citar]".$row["mensaje"]."[/citar]";
if($row["id"]==0) $row["id"]=$id;
}
$template = implode("", file('formulario.html'));
include('header.html');
mostrarTemplate($template, $row);
include('footer.html');
?>[/php]
En el script vemos como primero capturamos de la URL, las variables **$id** y **$citar**, y si ésta última es igual a 1, entonces consultamos en la base de datos toda la información del tema que estamos citando, para agregarlo en el arreglo **$row**, que luego será pasado al template. Noten como al titulo del mensaje, le anteponemos la cadena "Re:", indicando que es una respuesta, y como al cuerpo del mensaje, si estamos citando, lo rodeamos por un tag **[ citar ]** y **[ /citar ]**. Esto lo veremos más adelante.

**_formulario.html_**[php]










Autor








Titulo








Mensaje


< ?=$mensaje?>










[/php]
Aqui vemos como colocamos las variables dentro de los atributos "**value**" de los **inputs** y el **textarea**. También podemos ver como tenemos un campo escondido, llamado "identificador", que solo tendrá un valor asignado, cuando estemos respondiendo a un mensaje, pero que no existirá cuando sea un mensaje nuevo. Solo queda ver el script que se encarga de grabar el mensaje en la base de datos, **agregar.php**.

**_agregar.php_**[php]< ?php
require('configuracion.php');
$autor = $_POST["autor"];
$titulo = $_POST["titulo"];
$mensaje = $_POST["mensaje"];
$ident = $_POST["identificador"];

//Hacemos algunas validaciones
if(empty($autor)) $autor = "Anónimo";
if(empty($titulo)) $titulo = "Sin título";
//Evitamos que el usuario ingrese HTML
$mensaje = htmlentities($mensaje);

// Grabamos el mensaje en la base.
$sql = "INSERT INTO foro (autor, titulo, mensaje, identificador, fecha, ult_respuesta) ";
$sql.= "VALUES ('$autor','$titulo','$mensaje','$ident',NOW(),NOW())";
$rs = mysql_query($sql, $con) or die("Error al grabar un mensaje: ".mysql_error);
$ult_id = mysql_insert_id($con);

/* si es un mensaje en respuesta a otro
actualizamos los datos */
if(!empty($ident))
{
$sql = "UPDATE foro SET respuestas=respuestas+1, ult_respuesta=NOW()";
$sql.= " WHERE id = '$ident'";
$rs = mysql_query($sql, $con);
Header("Location: foro.php?id=$ident#$ult_id");
exit();
}
Header("Location: index.php");
?>[/php]
En este script, luego de tomar las variables desde el formulario ( con el método POST ), primero verificamos que exista un nombre de autor y el título del mensaje, caso contrario le asignamos un valor por defecto. También utilizamos la función de PHP **htmlentities()** para convertir todos los caracteres especiales ( >, < , ", &, etc ) en sus respectivas entidades HTML ( &gt;, &lt;, &quote;, &amp;). Con esto evitamos que un usuario ingrese código HTML en nuestro Foro (con la respectiva vulnerabilidad que este implica).

A continuación, grabamos el mensaje en la base, y obtenemos, mediante la función **mysql_insert_id()**, el último id autoincremental que le corresponde a este registro. ¿Para qué? Simple. Si este mensaje que acabamos de grabar es el primero del tema, no necesitamos hacer nada, pero si es un mensaje en respuesta a otro (esto lo averiguamos preguntando por el valor de **$identificador**), entonces tenemos que actualizar ese primer mensaje, indicando que tiene una respuesta más, y cambiando la fecha y hora del último mensaje. De esa manera, nos aeguramos que tenemos bien ordenado el foro, con los temas con nuevos mensajes primero. Finalmente, dependiendo del caso, redirigimos al usuario al home del foro, o a la respuesta que acaba de ingresar.
**Miles de posibilidades**

Ya solo nos queda un último paso, y es el de crear la página que mostrara un tema y todas las respuestas que haya en él. Para ello, vemos como en el home del foro, llamamos a un script **foro.php** y le pasamos el id del tema que queremos ver. Luego, solo tenemos que obtener de la base el o los temas, en los que el id sea igual al que pasamos, o que el identificador (el campo que indica que ese mensaje es en respuesta a cierto tema) sea igual al identificador, los ordenamos por fecha y listo, foro al dente.

En este caso, el template que utilizaremos para mostrar cada uno de los mensajes, será una tabla con todos los datos necesarios: el autor del mensaje, el título, la fecha del mensaje, el mensaje en si mismo. Pero también agregaremos dos detalles. Primero, un link hacia el formulario que creamos antes, de modo que un usuario pueda citar un mensaje en particular, y segundo, un Anchor (o Ancla) para que al responder a un mensaje, se pueda acceder directamente al mismo por su id en la base de datos.

**_post.html_**[php]








**
< ?=$autor?>
**
Enviado el : < ?=$enviado?>











**
< ?=$titulo?>
**


[ [CITAR](respuesta.php?id=<?=$id?>&citar=1)
]






* * *

< ?=$mensaje?>










**[/php]
Ahora, veamos el código PHP que utilizaremos para "_parsear_" este template :

**_foro.php_**[php]< ?php
require('configuracion.php');
require('funciones.php');
$id = $_GET["id"];
if(empty($id)) Header("Location: index.php");

$sql = "SELECT id, autor, titulo, mensaje, ";
$sql.= "DATE_FORMAT(fecha, '%d/%m/%Y %H:%i:%s') as enviado FROM foro ";
$sql.= "WHERE id='$id' OR identificador='$id' ORDER BY fecha ASC";
$rs = mysql_query($sql, $con);
include('header.html');
if(mysql_num_rows($rs)>0)
{
include('titulos_post.html');
$template = implode("", file('post.html'));
while($row = mysql_fetch_assoc($rs))
{
$color=($color==""?"#5b69a6":"");
$row["color"] = $color;
//manipulamos el mensaje
$row["mensaje"] = nl2br($row["mensaje"]);
$row["mensaje"] = parsearTags($row["mensaje"]);
mostrarTemplate($template, $row);
}
}
include('footer.html');
?>[/php]

Como siempre, incluimos la conexion a la base de datos, el archivo de funciones y validamos de que exista la variable **$id**, ya que de lo contrario, nada podríamos hacer y nuestro foro fallaría en el Query. Hablando del Query, podemos ver como utilizamos la función de MySql **DATE_FORMAT()** para convertir el formato por defecto del tipo **datetime** ('AAAA-MM-DD hh:mm:ss') en algo que sea más común para nuestro idioma ('DD/MM/AAAA hh:mm:ss'). Si quieren más información sobre esta función, pueden visitar y consultar [el manual de mySQL](http://www.mysql.com/doc/en).

Lo más destacado en este script que podemos ver, son dos transformaciones que le hacemos al mensaje, antes de enviarlo al template. Como vemos, primero utilizamos la función de PHP  **nl2br()**, que convierte todos los saltos de linea, en tags **<br />**, de esa manera, los saltos que un usuario ingrese en el textarea, serán agregados correctamente al mostrar el mensaje. Luego, vemos como llamamos a la función **parsearTags()**. ¿Qué hace esta función? Veamos:

**_funciones.php_**[php]< ?php
function parsearTags($mensaje)
{
$mensaje = str_replace("[citar]", "
**


> 


> 
> * * *
> 
> **", $mensaje);
$mensaje = str_replace("[/citar]", "
**

> 
> * * *
> 
> **", $mensaje);
return $mensaje;
}
?>[/php]

Dentro de esta función, podemos agregar todas las modificaciones que queremos realizarle al mensaje, antes de mostrarlo en el Foro. En el ejemplo, vemos como hemos implementado el uso de un tag propio, **[citar]**. El mismo, dentro de la función, será reemplazado por el código HTML necesario para destacar el citado de un mensaje, todo esto gracias a la función **str_replace()** de PHP (más info en el manual). Este es el tag **[citar]** que se agrega automáticamente, y que notamos cuando respondíamos un mensaje.

Esta función, pueden personalizarla de la manera que deseen, agregando todos los tags que quieran, para ofrecerles a sus usuarios la libertad de darle formato a sus mensajes. Podrían, por ejemplo, agregar un nuevo tag, para poner palabras en negritas, o quizás alguna expresión regular que convierta automáticamente cualquier URL presente en el mensaje, en un link. Los límites son los de su imaginación.
**Misión Cumplida**

Cómo intenté demostrarles en este pequeño artículo, realizar nuestro primer foro es algo completamente sencillo, si sabemos utilizar mínimamente **MySql** y **PHP** (más bien, algunas funciones más que útiles del PHP). En estas pocas líneas aprendimos :

> 
> 
	
>   * **Crear una tabla en MySQL para que contenga los datos de nuestro foro.**
> 
	
>   * **A conectarnos a MySQL desde nuestro script PHP.**
> 
	
>   * **A utilizar un sistema de templates casero y simple.**
> 
	
>   * **La utilización de funciones de PHP como : extract(); eval(); implode(); file(); nl2br(); mysql_insert_id(); str_replace();**
> 
	
>   * **La utilización de la función DATE_FORMAT() de MySQL.**
> 
	
>   * **Cómo trabajar de manera segura con la directiva Register_Globals en OFF, tomando uno a uno los contenidos de las variables, desde sus respectivos arrays $_POST y $_GET.**
> 

Y varios conceptos más a la hora de programar nuestros scripts. Ahora, este sistema es muy básico, como simple. Así que de ahora en más, es campo fértil para que uds. mismos puedan agregarle todas las características y funcionalidades que deseen, personalizando el foro a su gusto. Como ideas, puedo mencionarles algunas :

	
>   * **Agregar más tags para que sus usuarios puedan dar formato a sus mensajes**
> 
	
>   * **Incorporarle un sistema de usuarios**
> 
	
>   * **Contadores de visualizaciones de un tema, para hacer un Ranking de temas más vistos.**
> 
	
>   * **La posibilidad de que los usuarios puedan utilizar firmas**
> 

Y seguro que a uds. mismos se les deben estar ocurriendo otras muchas buenas ideas para mejorar el Foro. Para aquellos usuarios que sean vagos y no quieran estar un rato con el Copy&Paste, les dejo el código completo del foro [para que lo descarguen](http://www.zonaphp.com/archivos/foro.zip). Por lo pronto, espero que hayan disfrutado este tutorial, y sigan programando simple, seguro, pero ante todo, bonito :D.
