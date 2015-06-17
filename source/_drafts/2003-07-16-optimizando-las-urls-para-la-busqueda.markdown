---
author: Nok
comments: true
date: 2003-07-16 02:52:19+00:00
layout: post
slug: optimizando-las-urls-para-la-busqueda
title: Optimizando las URLs para la búsqueda
wordpress_id: 9
categories:
- PHP Avanzado
---

Cuando tenemos un sitio con material que queremos compartir y que este disponible, nos interesa que sea indexado por los motores de búsqueda. En este artículo se discuten varias maneras con las cuales se puede lograr que nuestras URLs sean fácilmente indexables.<!-- more -->

**Introducción**

A menudo cuando realizamos nuestros sitios o aplicaciones web, nos encontramos con que nuestras urls son de la manera:

**http://misitio.com/articulo.php?id=10&pagina;=2**

Esto es totalmente normal y no debería ser ningún problema, pero resulta que este tipo de url no es indexable (agregada a la base de datos) por los motores de búsqueda de la web, por lo que se nos presenta el inconveniente de que si en nuestro sitio se escriben artículos o noticias las cuales queremos que sean "conocidas", esta clase de urls no nos ayuda demasiado. Por esto, habrán notado que muchos sitios, trabajan con urls del tipo:

**http://otrositio.com/articulo/10/2**

En si, es exactamente lo mismo, es decir el 10 indica el artículo y el 2 el número de página. La gran diferencia es que esta clase de urls son totalmente indexables por los motores de búsqueda, lo que ayuda mucho a la difusión de nuestros artículos y/o noticias.
Para lograr esto, y así facilitar la indexación de nuestras páginas,  Apache nos provee de herramientas con las cuales se pueden utilizar varios métodos, en este artículo analizaremos 3 de ellos, estos son:




	
  * Motor de re-escritura de Apache

	
  * Configuración de Apache mediante Files o Location

	
  * Utilizando el error 404 de documento no encontrado.


Presentados los contendientes, veamos cuales son sus características.
**Conociendo el mod_rewrite de Apache 1.3.x**

El primer método que veremos será utilizando el este es usando el Motor de Re-escritura de URLs de Apache (mod_rewrite), que es uno de los métodos más sencillos de aplicar. Antes de utilizar esta poderosa herramienta, primero conoceremos algunos de sus parámetros de configuración para sacarle más provecho.
Por lo general este modulo viene cargado en Apache, pero sino, lo pueden activar desde el httpd.conf de sus apaches descomentando la línea:

**LoadModule rewrite_module modules/mod_rewrite.so**

Una vez que tenemos instalado este modulo, veamos algunas de las directivas de configuración con las que cuenta:

**RewriteEngine**_on | off_
Con esta directiva lo que hacemos es activar o desactivar el motor de re-escritura. Sus únicas posibilidades son on (activo) u off (desactivo)

**Expresiones regulares**
El mod_rewrite soporta expresiones regulares las cuales responde a la siguiente especificación:

Texto:



.

    Cualquier carácter

[caracteres]

    Clase de caracteres: Algún carácter dentro de _caracteres_

[^caracteres]

    Clase de caracteres: Ningún carácter dentro de _caracteres_

texto1|texto2

    Alternativa: texto1 or texto2



Cuantificadores:



?

    0 o 1 ocurrencia del  texto precedente

*

    0 o más ocurrencias del texto precedente.

+

    1 o más ocurrencias de el texto precedente.



Grupos:



(texto)

    Grupo de texto. Sirve para agrupar texto o para utilizarlo como referencia luego.



Inicio y Fin:



^

    Principio de línea.

$

    Fin de línea.



Caracteres de escape (no ser considerados especiales):



\car

    Escapa el carácter _car_. Por ejemplo, "\." se considera el punto en sí, no cualquier carácter. De mismo modo \n representa el salto de línea, etc.



Además, existen las referencias a expresiones entre paréntesis previas (back-references), y las referencias a variables de entorno. Para las primeras se utiliza $N, donde N es el numero (en orden) de la expresión entre paréntesis en la expresión regular previa, es decir,
si tenemos: Hola (.*). Que tal $1.
$1 hace referencia a la expresión matcheada en el primer grupo de paréntesis.
En cuanto a las segundas (variables de entorno) se las referencia usando %{NOMBRE_VAR}. Por ejemplo: %{REQUEST_URI}

**RewriteCond** _CadenaEvaluada Patrón_
Con esta directiva podemos realizar reglas condicionales, es decir, chequear de antemano cual es la URI para saber si se le aplica o no una regla de re-escritura (RewriteRule). La _CadenaEvaluada_ es por lo general una referencia a una variable del entorno, por ejemplo la REQUEST_URI, que será comparada contra el _Patrón_ que es una expresión regular.

De esta manera la CadenaEvaluada será comparada con la expresión regular en _Patrón_ y si coincide (matchea) será ejecutada la regla de escritura siguiente.

Por ejemplo:
`# La regla se ejecutará si el REQUEST_URI empieza con /espanol/
RewriteCond %{REQUEST_URI} /espanol/.*
RewriteRule ....re-escribimos de alguna manera...`

**RewriteRule** _Patrón Re-escritura_
Esta es la directiva con la cual se realiza la re-escritura de la URL, el patrón responde a la especificación antes vista y la re-escritura puede ser un texto con referencias previas (back-references). Es decir, todo lo que concuerde con el _patrón_ será re-escrito según _Re-escritura_.

Por ejemplo:
`#Todo lo que empiece con español lo re-escribimos como index.es.html
RewriteRule /espanol/.* /index.es.html`

Ahora mezclando todo:

`# Si el REQUEST_URI empieza con /es/ o /en/ o /ru/
RewriteCond %{REQUEST_URI} /(es|en|ru)/.*`

`#Re-escribo la url por _index.es.html_ o  _index.es.html_ o _index.es.html_
#Según corresponda.
RewriteRule /(.*)/.* /index.$1.html`
**Utilizando el mod_rewrite**

Con el mod_rewrite instalado y conociendo lo básico de expresiones regulares, lo único que nos queda es usar el .htaccess para indicarle como queremos que haga su trabajo.

Para nuestro ejemplo supondremos que tenemos un sistema el cual tiene varios artículos y cada artículo tiene varias páginas, por lo que en nuestra url estará compuesta de 2 variables, **id** y **pagina**  que nos indican el artículo y el numero de página respectivamente.
Es decir:

**artculo.php?id=XXX&pagina;=YYY**

Donde XXX e YYY son los valores de las variables que son pasadas por la URL mediante el método GET. Lo que buscamos es transformar nuestras direcciones de manera que nos sirva para la indexación, de esta manera:

**articulo/XXX/YYY**

En nuestro ejemplo lo que necesitamos hacer es simplemente traducir el valor del id y la página a la dirección real. Para esto, utilizaremos referencias previas. Veamos nuestro .htaccess para hacer esto

**_.htaccess_**
`#Activamos el mod_rewrite
RewriteEngine on
#Le indicamos como re-escribir la URL
#En este caso lo que hacemos es indicarle que las expresiones entre paréntesis corresponden al id y página respectivamente.
RewriteRule /articulo/(.+)/(.+) /articulo.php?id=$1&pagina;=$2`

De esta manera, la url será re-escrita como a nosotros nos conviene, así de fácil.

Esta es una versión bastante básica, aunque funcional. Queda en el lector investigar más sobre el uso del **mod_rewrite** y así poder sacarle toda las funcionalidades que esta poderosa herramienta presenta.

Cabe resaltar que en este caso el .htaccess debe esta ubicado en un directorio superior al cual sé esta haciendo referencia con la url, dado que de otra manera no funciona. Es decir, si deseamos que nuestra url sea:

   **http://misitio.com/articulo/10/2**

Nuestro .htaccess deberá encontrarse en un nivel superior al **DOCUMENT_ROOT**.
**Capturando la URL**

Para los próximos métodos es necesario simular la re-escritura por lo que vamos a presentar un script php el cual nos servirá para tal fin.

Para esto nos ayudaremos con la variable de entorno **REQUEST_URI** que se encuentra dentro del array asociativo $_SERVER (php >= 4.1.x) y que nos indica cual fue el "documento" solicitado por el browser. Es decir, para nuestro ejemplo seria:

**http://misitio.com/articulo.php?id=XXX&pagina;=YYY**
REQUEST_URI = **/articulo.php?id=XXX&pagina;=YYY**

**http://misitio.com/ articulo.php/XXX/YYY**
REQUEST_URI = **/articulo/XXX/YYY**

Aclarado eso pongamos manos a la obra, lo que haremos será crear un script que sera el encargado de analizar la url y realizar el pedido de la información realmente.

**_capturar.php_**[php]< ?php
/*  Especificamos cual es el script de nuestra aplicación. El que se encargará de hacer el trabajo. */
$base = 'articulo.php';
$_uri = $_SERVER['REQUEST_URI'];

/* Desarmamos la  URI para luego analizarla, debería ser así /articulo/XXX/YYY */
$url = explode('/', $_uri);

/* Verificamos que este bien el artículo solicitado, es decir respete el formato articulo.php/XXX/YYY */
if ($url[1] == 'articulo' and isset($url[2]) and isset($url[3])) {

   /* Una vez que obtuvimos los datos se los pasamos a nuestro script */
   /* para hacer eso, lo que hacemos es sobre escribir las entradas del array $_GET para que tome los valores de ahi */
   $_GET['id'] = $url[2];
   $_GET['pagina'] = $url[3];

   /* incluimos la aplicación, que tomará estas variables */
   include_once($base);
}
else {
   /* Si esta mal la URI lo redirijimos al home del sitio */
   header('Location: http://misitio.com/');
}
?>[/php]

Con este script podremos adaptar nuestra aplicación casi sin problemas, y sin tocar nada de código. Por el lado de php ya estaríamos casi listos, ahora veamos como configurar nuestro apache.
**Configurando Apache con Files o Location**

Otro de los métodos para realizar esto, es configurar nuestro Apache mediante el uso de **Location**, esto debe ser dentro del **httpd.conf**, pero como muchas veces no se puede tocar este archivo debido a que estamos en un servidor de hosting en el cual no tenemos acceso a este archivo, existe la posibilidad de usar **Files** desde el archivo **.htaccess**.

En ambos casos lo que se debe indicar es lo siguiente:

**_httpd.conf_**
`
#Forzamos el tipo del archivo a ser php
ForceType application/x-httpd-php

`

**_.htaccess_**
`
#Forzamos el tipo del archivo a ser php
ForceType application/x-httpd-php
`

De esta manera Apache forzara la ejecución de un archivo llamado "_articulo_" (sin .php) para cualquier requerimiento que empiece con "articulo". Es decir que nuestro archivo se deberá llamar "articulo" en lugar de "capturar.php". Esto no genera ningún tipo de falla de seguridad ya que este archivo será interpretado como código php y no será posible la visualización del código.

En este caso no será necesario hacerle modificación alguna a nuestro script, ya que automáticamente tomará la variable REQUEST_URI y realizará su trabajo.

**Nota**: Es posible que la opción Files no funcione en entornos Windows.
**Usando el Error 404**

Por último, existe un método por el cual se puede utilizar una página personalizada de **error 404** para realizar esto mismo. El truco consiste en solicitar una url no existente interceptar ese pedido y luego decidir si es valida o no.

**Nota**: Si bien el método funciona tiene algunos puntos en contra como es el hecho de que genera logs duplicados, es decir, siempre se registra el error de no encontrado por más que se sobre escriba el encabezamiento. Queda a criterio del lector su utilización o no.

Para realizar esto debemos indicar en nuestro ya amigo .htaccess cual es la página de error 404 (para mayor información pueden consultar el artículo ...).

_**.htaccess**_
`ErrorDocument 404 capturar.php`

Ahora lo único que resta es analizar si es o no una página de error, para eso modificamos levemente nuestro script.

**_capturar.php_**[php]< ?php
/*  Especificamos cual es el script de nuestra aplicación. El que se encargará de hacer el trabajo. */
$base = 'articulo.php';
$_uri = $_SERVER['REQUEST_URI'];

/* Desarmamos la  URI para luego analizarla, debería ser así /articulo/XXX/YYY */
$url = explode('/', $_uri);

/* Verificamos que este bien el artículo solicitado, es decir respete el formato articulo.php/XXX/YYY */
   if ($url[1] == 'articulo' and isset($url[2]) and isset($url[3])) {
   /* como es una url valida sobreescribimos el encabezado de error 404 */
   header("Status: 200 OK");
   /*  El resto sigue igual */
   // ...
}
   else {
   /* Como esta mal le indicamos que no existe la página */
   include_once("error404.html");
}
?>[/php]
**Conclusión**

Bueno, hemos visto como de varias maneras es posible convertir nuestras URL en indexables, solo resta decir que hay que experimentar mucho y tratar de sacarle el mayor jugo posible a esta técnica altamente útil y poderosa, para aumentar el trafico de nuestro sitio, mejorando la indexación en los motores de búsqueda y haciendo lucir mucho más profesional a nuestro website. Como siempre los comentarios son bienvenidos.

[Juan Pablo Winiarczyk](http://www.jpw.com.ar)

Para leer más:

Info sobre mod_rewrite



	
  * [http://httpd.apache.org/docs/mod/mod_rewrite.html](http://httpd.apache.org/docs/mod/mod_rewrite.html)

	
  * [http://httpd.apache.org/docs/misc/rewriteguide.html](http://httpd.apache.org/docs/misc/rewriteguide.html)


