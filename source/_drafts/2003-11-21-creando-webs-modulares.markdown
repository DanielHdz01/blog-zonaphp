---
author: webstudio
comments: true
date: 2003-11-21 17:57:30+00:00
layout: post
slug: creando-webs-modulares
title: Creando Webs Modulares
wordpress_id: 7
categories:
- Principiantes
---

Después de embeber código PHP entre el HTML por un tiempo, cualquier principiante que esté en planes de dejar de serlo, ve cierto patrón que se repite y piensa... ¿No habrá otra manera mejor, más ordenada de hacer esto?. Pues si, si la hay. Y en este artículo aprenderemos una de estas maneras.<!-- more -->

**Lo habitual**
Cuando aprendemos nuestros primeros pasos con PHP, cualquier tutorial o manual que leemos, nos enseña y vanagloria las bondades de poder incluir el código PHP directamente entre el HTML. Entonces, es muy habitual ver ejemplos del tipo:

[php]


< ?php
   echo "Este texto se hace con PHP";
?>

[/php]
Y como la mente y el alma de un principiante de cualquier tema, está preparada para aprender todo lo que pueda, en el menor tiempo que pueda, toma este tipo de ejemplos como "verdades universales" y comenzamos a ver código PHP embebido entre el HTML por TODOS lados. Pero no hay que preocuparse, esto no es necesariamente malo, y todos lo hicimos en menor o mayor medida al comenzar. Es un paso necesario que hay que aprender a superar. :D

Luego de un tiempo, y después de haber leido un poco más, aprendemos que todas las partes comunes de código que son repetitivas, pueden residir en archivos separados y ser "Incluidas" en nuestro código con tan solo el uso de una función:

[php]


< ? include('includes/header.php'); ?>



 


   
< ? include('includes/menu.php'); ?>

   
Aqui va el contenido del sitio web propiamente dicho. Recordar que es terriblemente MALO y PERJUDICIAL para la salud de su sitio, utilizar archivos externos con extensión ".inc"

 
< ? include('includes/footer.php'); ?>


[/php]

Entonces, ese es el momento en que **include()** se convierte en nuestro mejor amigo y descubrimos, fascinados, como podemos lograr una verdadera mejora en el mantenimiento de nuestros sitios, al tener que modificar 1 solo archivo y que este cambio, se vea reflejado en TODAS las páginas que incluyan ese archivo. Hemos, señoras y señores, alcanzado el Nirvana.

Pero, lamento decepcionarlos, ningún estado de felicidad suprema es permanente. Luego de un tiempo, y a medida que seguimos utilizando este método, de repente nos vamos dando cuenta que algo simplemente no cuadra. Si bien el anterior método del include() nos salvó en su debido momento, nos vamos dando cuenta que para crear una nueva página, es necesario repetir muchas veces los include() que llaman a las partes comunes de nuestro sitio. Esto sin contar que si, por arte de magia o capricho de un jefe/cliente, la cabecera de nuestro sitio pasa de ser header.php a cabecera.php, deberiamos modificar uno a uno todos los archivos que hagan un llamado a "header.php" y hacer el reemplazo.

Es en este punto, donde cientos de programadores, todos los años se hacen la misma pregunta: ¿No habrá una manera mejor de hacer esto?
**Una Mejor Manera**

Y si. Siempre existe una mejor manera de hacer las cosas. Y este caso no es la excepción.

Después de mucho pensar, y de mirar constantemente el código, una y mil veces, uno termina siempre preguntándose : ¿Y si hiciéramos las cosas al revés? ¿Qué pasaría si en vez de incluir todas las partes comunes de nuestro sitio (Header/Footer/etc), solamente incluyéramos el contenido?

Pero que buena pregunta! Veamos que sucedería.

La primera cuestión que nos llega a la mente, es que sería necesario indicarle a nuestro archivo.php, qué página queremos cargar. Veamos como podemos hacer esto :

[php]< ?php
// Leemos la variable que indica que página queremos cargar.
if(empty($_GET['modulo']))
 $pagina='home.php';
else
 $pagina=$_GET['modulo'];
include('includes/header.php');
include('modulos/'.$pagina);
include('includes/footer.php');
?>[/php]

De esta manera, si llamáramos a este archivo **index.php**, para cargar algún "modulo" de nuestro sitio web, tan sólo deberíamos indicarlo en el URL, de esta manera : **http://www.nuestrositio.com/index.php?modulo=foro.php**.

Pero de este método se desprenden varias cuestiones, a saber:



	
  * Si un usuario "distraido" pusiera en la variable **modulo** un nombre de archivo inexistente, aparecería un mensaje de error (si PHP está configurado para mostrar errores, lo que es lo más común) indicando que el archivo no existe. Así que es necesario primero, identificar si el archivo que el usuario intenta acceder, existe.

	
  * Cuestiones de Diseño : Es muy posible, que no todos los "módulos" utilicen el mismo "diseño base" del sitio, o sea, que no siempre sea necesario mostrar la misma estructura, o el mismo sistema de navegación. Sería necesario de alguna manera, indicarle a nuestro sitio que "diseño base" utilizará cada módulo.

	
  * ÿltimo pero no menos importante, la Seguridad. Dejar al descubierto una variable que indica que archivo incluir, es una de las fallas de seguridad más serias que posee un sitio web. ¿Se imaginan que sucedería si un usuario, no ya tan "distraido" sino un poco más malicioso, llamara a nuestro sitio web de esta manera : **http://www.nuestrositio.com/index.php?modulo=../../../../../etc/passwd **?
Sirenas de Emergencia por todos lados !!! De esto se desprende que hay que encontrar una manera de controlar que la página que sea pedida, esté dentro de las opciones permitidas.

Vistas todas las desventajas de este primer acercamiento, veremos de que manera las podemos solucionar con un poco de ingenio y mucho PHP.
**Modularizando nuestro sitio**

Teniendo las consideraciones de seguridad y diseño en cuenta, vamos a desarrollar un sistema que nos permita modularizar nuestro sitio, manteniendo cierta flexibilidad para poder indicar que Diseño ( o Layout, como lo llamaremos en el código ) utilizarán nuestros módulos. Además, mantendremos un listado de todas las páginas que pueden ser invocadas, de manera que evitaremos que cualquier archivo de los que el Webserver tenga acceso de lectura, pueden terminar "sin querer" en el navegador de nuestro visitante.

Este listado podriamos guardarlo de diferentes maneras : archivos de texto con algún formato estandar o propio, en una Base de Datos, en un archivo XML (como es costumbre en algunas aplicaciones más complejas) o con tipos de datos propios del PHP (en este caso, arreglos). "_Yo escogo este último porque me place, vosotros podéis escoger el tipo que queráis_" (los fanáticos de Cha-Cha-Cha estarán esbozando una sonrisa). Y justifico mi decisión:



	
  * No tengo personalmente nada en contra de los archivos de texto plano, pero parsear los contenidos de uno, con el tipo de formato que sea, incluye cierta lógica de programación extra innecesaria, ya que la idea es hacer las cosas lo más sencillas posibles y no al revés.

	
  * Una base de datos definitivamente haría más sencillas las cosas, pero limitaría la utilidad de este método a aquellos servidores que no posean bases de datos. Se que los servidores que no poseen bases de datos hoy por hoy son escasos, pero prefiero no basarme en esa solución para hacer la aplicación lo más compatible posible.

	
  * Muchos ven en el formato XML al "santo grial" de las soluciones para almacenar datos o utilizarlos como sistemas de configuración. Y posiblemente para aplicaciones de escritorio esto no sea tan errado, ya que el archivo XML se parsea una vez y luego se mantienen sus datos en memoria durante toda la "vida" del programa. Pero en un ambiente web, sería necesario no solo que el PHP tenga activada alguna de las opciones para trabajar con XML, sino que se tendría que parsear el archivo XML ( con el gasto de recursos que esto conlleva ) una vez POR CADA pedido de página que se le hiciera el servidor. Definitivamente, NO. Aparte, es un tutorial para principiantes, asi que vamos a hacer las cosas sencillas.

Así es que para nuestro sistema de configuración, vamos a utilizar los viejos y queridos arreglos asociativos de PHP. El siguiente es un ejemplo básico para un sitio web realmente pequeño, pero ya veremos de extenderlo a medida que nuestro ejemplo "evolucione".

**_conf.php_**[php]< ?php
/*
* Archivo de configuración para nuestra aplicación modularizada.
* Definimos valores por defecto y datos para cada uno de nuestros módulos.
*/
define('MODULO_DEFECTO', 'home');
define('LAYOUT_DEFECTO', 'layout_simple.php');
define('MODULO_PATH', realpath('./modulos/'));
define('LAYOUT_PATH', realpath('./layouts/'));

$conf['home'] = array(
       'archivo' => 'home.php',
       'layout' => LAYOUT_DEFECTO );
$conf['articulo'] = array(
       'archivo' => 'art.php' );
?>[/php]

Aqui vemos, como en la primera parte de nuestro archivo, definimos algunas constantes que nos van a servir: MODULO_DEFECTO, indicando cuál de los módulos cargar si no se indicó ninguno, LAYOUT_DEFECTO que indica que "diseño" se utilizará por defecto en los módulos, MODULO_PATH y LAYOUT_PATH, indicando directorios en los cuales vamos a almacenar nuestros módulos y layouts. En el ejemplo, quedarán en dos directorios separados, pero podrían ser el mismo sin problema.

Aqui vemos, que tenemos dos "módulos" en nuestra aplicación; **"home"** y **"articulo"**. Estos nombres de índices, son los que le vamos a pasar a nuestra variable **modulo**, indicando a que sección de nuestro sitio queremos acceder.

Como vemos, cada "sección" contiene ( o deberia indicar ) dos directivas: **"archivo"** y **"layout"**. El primer valor es el nombre del archivo que efectivamente vamos a incluir, asociado con el nombre del indice del arreglo. Esto soluciona en gran parte el problema de seguridad que nombramos anteriormente, ya que no se indica directamente el nombre del archivo a incluir, sino que se indica a través de un nombre ficticio, un alias.

El segundo valor, layout, es el nombre del archivo que contiene el diseño "base" de la aplicación. Aqui tenemos dos opciones, indicar en el archivo de configuración el nombre del archivo de layout, o expresar, mediante la constante definida antes, que cierto módulo utiliza el layout por defecto ( esto ayuda a la claridad luego cuando se quiera revisar el archivo de configuración y se aconseja ). Como podemos ver en el módulo "articulo", en este caso no se indica ningun archivo de layout. Esto nosotros lo tomaremos como que al no indicarse, se desea utilizar el layout por defecto (Esta es una opción realmente buena para los programadores holgazanes como yo, que no quieren escribir grandes archivos de configuración).
**Programando las bases**

Ya tenemos listo nuestro archivo de configuración, que servirá como columna vertebral de nuestra nueva aplicación modularizable. Ahora veremos como lo utilizamos. Pero primero, veamos un poco como organizaremos nuestro árbol de directorios en nuestra nueva aplicación, para tener todo bien ordenado.



	
  * **includes/**

	
  * **layouts/**

	
  * **modulos/**

	
  * index.php

Así, vemos como tendremos un directorio para nuestros includes, uno para los módulos que componen nuestra aplicación y otro para los distintos layouts. Recordemos que ahora, nuestro archivo **index.php** será el único punto de entrada para nuestro sitio. Esto representa una ventaja ya que podemos colocar alli todas las tareas repetitivas (inicialización de variables, conexión a una base de datos, lectura de otros archivos de funciones o configuración, etc.). Otra de las ventajas es que ante 1 cambio que afectaría a todo el sitio web, solo hay que realizarlo en 1 solo archivo, solucionando alguno de los problemas que la metodología de varios archivos incluyendo partes comunes acarreaba.

Nuestro archivo index.php comienza de esta manera :

**_index.php_**[php]< ?php
// Primero incluimos el archivo de configuración
include('conf.php');

/** Verificamos que se haya escogido un modulo, sino
* tomamos el valor por defecto de la configuración.
* También debemos verificar que el valor que nos
* pasaron, corresponde a un modulo que existe.
*/
if (!empty($_GET['mod']))
   $modulo = $_GET['mod'];
else
   $modulo = MODULO_DEFECTO;

/** También debemos verificar que el valor que nos
* pasaron, corresponde a un modulo que existe, caso
* contrario, cargamos el modulo por defecto
*/
if (empty($conf[$modulo]))
       $modulo = MODULO_DEFECTO;

/** Ahora determinamos que archivo de Layout tendrá
* este módulo, si no tiene ninguno asignado, utilizamos
* el que viene por defecto
*/
if (empty($conf[$modulo]['layout']))
       $conf[$modulo]['layout'] = LAYOUT_DEFECTO;
?>[/php]
Como bien explican los comentarios, lo que hacemos primero es incluir el archivo de configuración, sin él no podríamos saber si un módulo está permitido o no, y que Layouts utilizar. Luego, validamos que la variable "**mod**" contenga algo. De estar vacía o con un nombre de algún módulo inexistente (segundo **if()**), entonces hacemos que cargue el módulo por defecto, que está indicado en la constante MODULO_DEFECTO. Lo mismo hacemos para el Layout a cargar, si no está indicado en el archivo de configuración, tomamos por defecto el valor contenido en LAYOUT_DEFECTO. Continuamos con nuestro index.php

**_index.php_**[php]< ?php
/** Aqui podemos colocar todos los comandos necesarios para
* realizar las tareas que se deben repetir en cada recarga
* del index.php - En el ejemplo, conexión a la base de datos.
*
* include('clases/class.DB.php');
* $db = new DB();
* $db->conectar();
*/

/** Finalmente, cargamos el archivo de Layout que a su vez, se
* encargará de incluir al módulo propiamente dicho. si el archivo
* no existiera, cargamos directamente el módulo. También es un
* buen lugar para incluir Headers y Footers comunes.
*/
$path_layout = LAYOUT_PATH.'/'.$conf[$modulo]['layout'];
$path_modulo = MODULO_PATH.'/'.$conf[$modulo]['archivo'];

if (file_exists($path_layout))
   include( $path_layout );
else
   if (file_exists( $path_modulo ))
       include( $path_modulo );
   else
       die('Error al cargar el módulo **'.$modulo.'**. No existe el archivo **'.$conf[$modulo]['archivo'].'**');
?>[/php]

Aqui vemos algo que comentábamos antes. Encerrado en comentarios, hay cierto código que podemos incluir en nuestro index.php que, al ser ahora el único punto de entrada a todas las páginas de nuestro sitio, será ejecutado para cada página. Ya no hay que repetirlo en todas las páginas PHP, tan solo se coloca en el **index.php**. Esto es bueno, porque mientras menos repetición de código haya, más sencillo de mantener es todo. Vemos alli como ejemplo, el código para incluir e instanciar algún objeto de conexión a base de datos, pero podría ser cualquier otro código que necesitemos ejecutar cada vez que se vea una página, como por ejemplo, algún Validador de Usuarios registrados o el código necesario para dar seguimiento a las sesiones de Usuario.

Finalmente, nuestro archivo verifica que el archivo de Layout que se quiere incluir, exista primero. De existir, entonces se incluye y ya veremos en un ejemplo, como nuestro archivo Layout debe incluir luego, al archivo del módulo. Si no existiera el archivo de Layout, se intenta incluir directamente el archivo del módulo que se pidió, sin Layout, y si éste archivo tampoco existiera ( ¿demasiadas cosas que salen mal, no? ) entonces se muestra un error en la pantalla, indicando el nombre del módulo y el archivo que no se pudo hallar.

Perfecto, ya tenemos todo listo para cargar los módulos que necesitemos. Ahora, veamos como será un módulo de ejemplo y su archivo de Layut.
**Creando Módulos**

Tenemos dos opciones a la hora de crear un archivo de layout. Son las siguientes:



	
  * Incluir Headers y Footers comunes a todas las páginas dentro del index.php, ANTES y DESPUES de incluir el archivo de Layout, en el que dejaremos solo el diseño propio de la página para que albergue al módulo. Esto es útil en el caso de que un archivo de Layout no se encuentre o no sea necesario, y al incluir el Módulo, ya esté cargado el Header y luego sea incluido el Footer.

	
  * Hacer que el archivo de Layout, aparte de incluir el archivo del Módulo, sea el encargado de incluir Headers y Footers, lo cuál puede ser útil si el diseño general de varias secciones del sitio cambia drásticamente, como distintas subsecciones con Headers y Footers diferentes, o en el caso de "Versiones para Imprimir" o la creación de versiones en PDF de ciertos artículos.

Yo voy a elegir en este caso la segunda opción. Haré que el archivo de Layout **'layout_simple.php'** (el Layout por defecto, según el archivo dFe configuración) sea el encargado de incluir los archivos que hacen de Header y ooter en la aplicación. Esto es preferible a la primera opción ya que si, por ejemplo, queremos que un módulo específico de nuestra aplicación, devuelva un archivo .GIF como resultado ( utilizando GD ), entonces de esta manera, podremos indicar para ESE módulo, un archivo de Layout que se encargue de enviar los Headers correctos y luego incluir el archivo del módulo. Pero no se preocupen por estas consideraciones, ahora, veamos un ejemplo de Layout, que guardaremos en el directorio **/layouts**:

**_layout_simple.php_**[php]



< ?php include('includes/header.html'); ?>



   


       

       

< ?
   if (file_exists( $path_modulo )) include( $path_modulo );
   else die('Error al cargar el módulo **'.$modulo.'. No
   existe el archivo **'.$conf[$modulo]['archivo'].'**');
?>
       

   

< ?php include('includes/footer.html'); ?>

[/php]
¿Ven? es un simple archivo HTML con la estructura básica de las páginas de nuestro sitio, incluyendo el Header, el Footer y el código necesario para llamar al módulo pedido por el usuario. Por favor noten el uso de la variable **$path_modulo**, para indicarle al Layout el nombre del módulo que queremos cargar, variable creada en el **index.php** y propagada al archivo de Layout por estar éste, incluido en el primero.

Entonces, ya tenemos nuestro index.php, tenemos el Layout que se encarga de incluir el módulo... ¿Qué nos falta? ¡Pues el módulo! ÿstos pueden ser tan complejos o tan simples como el usuario quiera. En el ejemplo siguiente, un módulo **"home"** bien simple, que se carga por defecto en nuestra aplicación :

**_home.php_**[php]

### Bienvenido al Home




Este es un ejemplo de un sitio modular, como vemos, las páginas que
componen los módulos, pueden ser tanto archivos.php como archivos.html,
todo dependiendo de si necesitamos interactividad o no con el Servidor.
La hora actual es : **< ?=date("H:m:s");?>**




Para ver el contenido de un artículo, por favor, seguir el
[siguiente link](?mod=articulo).

[/php]
De nuevo, un poco de HTML por aquí, un poco de PHP por allá. Cabe notar, eso si, la manera en la que hicimos un link hacia OTRA página de nuestro sitio: "**?mod=articulo**". Con esto, le indicamos al navegador, que queremos cargar el mismo archivo que tenemos actualmente, solo que con este nuevo "**querystring**". Si la página actual fuera algo como:

**http://localhost/modulares/index.php**

Indicando un link como recién, al hacerle click cambiaría a :

**http://localhost/modulares/index.php?mod=articulo**

O sea, queremos cargar OTRO módulo, esta vez llamado "**articulo**". Como ya todo el trabajo árduo está hecho, solo tenemos que crear un archivo llamado **art.php** (ya que así lo indica el archivo de configuración) en el directorio **/modulos/**.

**_art.php_**[php]

### Título del Artículo




Aqui tenemos el ejemplo de un artículo cargado en nuestra Web Modularizada.
En este ejemplo simple, el artículo o nota, está escrito en HTML directamente,
pero podría estar siendo sacado de la base de datos si quisiéramos, no tenemos
limitación al respecto.




[Versión para Imprimir](?mod=imp_art) |
[Volver al Home](?mod=home).

[/php]
Así vemos, como crear un nuevo módulo es tan sencillo como crear el archivo pertinente, dejarlo en el directorios de Módulos, y actualizar el archivo de configuración para que permita accederlo. ÿste módulo, al no indicarse que Layout utiliza, vemos como toma el Layout por Defecto, o sea, el mismo que el Home. Cómo último paso, al final del artículo, tenemos dos links, uno, que nos regresa al Home del Sitio y el otro, el que nos interesa ahora, que ofrece una "Versión para Imprimir" del artículo. Modifiquemos nuestro archivo de configuración y agreguemos un nuevo módulo, que nos permita ofrecer una versión "Imprimible" del Artículo. Luego de editarlo, quedaría asi :

**_conf.php_**[php]< ?php
/*
* Archivo de configuración para nuestra aplicación modularizada.
* Definimos valores por defecto y datos para cada uno de nuestros módulos.
*/
define('MODULO_DEFECTO', 'home');
define('LAYOUT_DEFECTO', 'layout_simple.php');
define('MODULO_PATH', realpath('./modulos/'));
define('LAYOUT_PATH', realpath('./layouts/'));

$conf['home'] = array(
       'archivo' => 'home.php',
       'layout' => LAYOUT_DEFECTO );
$conf['articulo'] = array(
       'archivo' => 'art.php' );
$conf['imp_art'] = array(
       'archivo' => $conf['articulo']['archivo'],
       'layout' => 'imprimir.php' );
?>[/php]
¿Llamamos al mismo módulo? Así es. Pero la sutil diferencia es que lo incluimos con un Layout distinto, más limpio, apto para salir por impresora. Aqui un ejemplo de este Layout, que utiliza Cascading Style Sheets :

**_imprimir.php_**[php]< ?
$uri = "http://".$_SERVER["SERVER_NAME"].$_SERVER["REQUEST_URI"];
?>







< ?
   if (file_exists( $path_modulo )) include( $path_modulo );
   else die('Error al cargar el módulo **'.$modulo.'. No existe el archivo **'.$conf[$modulo]['archivo'].'**');
?>
_Este artículo se puede encontrar en : [< ?=$uri?>](<?=$uri?>)_




[/php]
**Finalizando**

Ya finalizando con este artículo, logramos tener una aplicación web que posee la capacidad de cargar distintos módulos y diseños dependiendo de los parámetros recibidos. Esto se parece mucho a un [Patrón de Diseño](http://www.google.com.ar/search?q=Patrones+de+dise%C3%B1o) llamado [Front Controller](http://www.google.com/search?q=Front+Controller&hl=es&ie=UTF-8&lr=lang_es&sa=X&oi=lrtip8). Para los curiosos, ahi tienen dos links en los que Google les puede enseñar un par de cositas.

Hoy aprendimos como centralizar el funcionamiento de un sitio a través de un archivo de configuración. También como modularizar nuestro sitio web con la ventaja de poder aplicar distintos diseños a los diferentes módulos que lo componen. Y sobre todo, a hacerlo de una manera sencilla, eficiente y segura. ¿Qué más se puede pedir?

Bueno, un montón de cosas, estas son las que se me ocurren en este momento. Estoy seguro que a Uds. se les ocurrirán muchas más :



	
  * Podríamos incluir en el Layout por Defecto, un menú de opciones en la celda izquierda, de manera de facilitar la navegación del sitio.

	
  * Una modificación interesante, sería que al pedir un usuario, un módulo inexistente en el arch. de configuración, se cargara un módulo determinado ( y no el "home" ) indicando el error y mostrando una página "símil" ERROR 404.

	
  * Se podría agregar en el arch. de configuración, un dato más para cada módulo, que indique el título de la página. Y que el Layout se encargue de utilizar ese valor dentro del tag ****.

	
  * Recomendable sería que tanto los directorios de Layouts, Includes y Modulos, no estén disponibles en el mismo nivel que el Directorio DocumentRoot del WebServer, ya que de esa manera, por quien conozca el árbol de directorios, podría ejecutar los archivos llamándolos directamente. Para evitar esto, podemos tanto mover los directorios un nivel hacia arriba y modificar el archivo de configuración para que encuentre estos directorios ( y como vemos, no tendriamos que tocar nada en el código de la aplicación ) o bien utilizar algún método para proteger esos directorios por contraseña, como puede ser un archivo [**.htaccess**](http://httpd.apache.org/docs/howto/htaccess.html) de Apache.

	
  * Tener un solo punto de entrada en nuestro sitio web, implica el paso de varios parámetros a un solo archivo de nuestro sitio. Lo que hace que nuestras chances de ser indexados por un buscador tipo Google se vean reducidas. Pero para esto hay varias soluciones, y bien lo explica nuestro amigo Nok en [Optimizando las URLs para las búsquedas](http://www.zonaphp.com/optimizando-las-urls-para-la-busqueda/).

Para los poco pacientes ( y para el resto también ) que no quieran estar copiando y pegando el contenido de estos ejemplos en los archivos, [aqui les dejo](http://www.zonaphp.com/archivos/modulares.zip) un archivo ZIP con todos los archivos del artículo. Recuerden, no es código apto para producción, si quieren utilizarlo directamente en sus sitios Web, lo hacen bajo su propia responsabilidad. ¡No digan que no les avisé! ;)

Esto ha sido todo por este momento, espero que lo que hayan leido en este artículo les sea deutilidad en sus sitios web y les ayude a programar menos y más rápidamente. Nos vemos en el siguiente artículo !
