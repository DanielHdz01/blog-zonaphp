---
author: chubu
comments: true
date: 2011-08-20 07:14:35+00:00
layout: post
published: false
slug: No Content Found
title: 'Memcached: acelerando y escalando nuestras aplicaciones web'
wordpress_id: 54
categories:
- Editorial
---

Usualmente, el primer problema que enfrentamos cuando nuestras aplicaciones web comienzan a crecer, es su performance. Muchas veces esta performance está relacionada con la interacción con una base de datos, otras con la construcción del HTML que será enviado al servidor. Cuando ya hemos realizado todas las optimizaciones posibles en nuestro uso de los recursos y todavía tenemos la soga al cuello es cuando aparece [Memcached](http://memcached.org/) para rescatarnos.
<!-- more -->
Memcached es un **"sistema distribuido de cacheo de objetos en memoria de alta performance"**. Básicamente, mediante su uso, nuestras aplicaciones web pueden acceder a un **espacio de memoria compartido** entre todos los threads (o hilos de ejecución, son los procesos del servidor que atienden cada request o petición web) donde pueden almacenar y acceder información y de este modo ahorrar recursos. De más está aclarar que **el tiempo de acceso a memoria es infinitamente inferior al tiempo de acceso a disco**, poniendo esta alternativa bastantes escalones por encima que el usual almacenamiento de datos o HTML procesado en archivos.



### Performance y Escalabilidad


Vemos que Memcached puede cubrir, de una manera sencilla, un hueco histórico en el desarrollo web PHP que es la falta de un espacio de memoria compartido que otros lenguajes si proporcionan, ayudandonos a **mejorar la preformance de nuestras aplicaciones web**. Pero sus posibilidades no se terminan ahi: las aplicaciones son varias: podemos usarlo para **unificar el acceso a datos de sesión de los distintos servidores web detrás de un balanceador de carga**, de modo tal que si **a lo largo de nuestra sesión** nuestras peticiones son atendidos por **distintos servidores**, estos accederán a **un único almacenamiento de sesiones**, solucionando otro problema histórico del desarrollo web en PHP: la **escalabilidad** ya que nos permite aplicar, de un modo muy simple, un modelo de **escalamiento horizontal**, pero esto es tema para otro artículo.

Otro buen ejemplo del uso de Memcached es el de **almacenar estructuras de datos ya procesadas**, o HTML de listados o páginas muy visitadas, o cualquier pieza de información que requiera de una consulta a un origen de datos y un posterior proceso, **operaciones que insumen muchos recursos** y memoria de proceso, y tenerlo disponible en Memcached ahorrando acceso a disco, a base de datos y tiempo de proceso, de modo tal que esta información es regenerada sólo cuándo la lógica de la aplicación así lo demande (por ejemplo cuando los datos sean modificados), o simplemente regenerandola a intervalos de tiempo preestablecidos, logrando **tiempos de respuesta infinitamente menores a los normales**.



### Instalando Memcached


Bueno, pasemos a lo concreto. Primero debemos instalar Memcached en nuestro servidor.


#### Windows


Si son usuarios Windows deben tener en cuenta que **no existe una versión oficial de Memcached para Windows** y que las versiones disponibles, ofrecidas por terceros, no son muy actuales (al momento de escribir este artículo Memcached va por su versión estable 1.4.7, y las versiones disponibles para Windows van desde la 1.2.4 a la 1.4.2). Aclarado el asunto, **deberán elegir entre algúna de las disponibles** y seguir las instrucciones detalladas



	
  * [memcached for Windows](http://splinedancer.com/memcached-win32/) (32 bits, [Memcached 1.2.4](http://splinedancer.com/memcached-win32/memcached-1.2.4-Win32-Preview-20080309_bin.zip))

	
  * [Jellycan Code - Memcached](http://code.jellycan.com/memcached/) (32 bits, [Memcached 1.2.5](http://code.jellycan.com/files/memcached-1.2.5-win32-bin.zip) y [Memcached 1.2.6](http://code.jellycan.com/files/memcached-1.2.6-win32-bin.zip))

	
  * [Uriel Katz Blog: Memcached 64-bit for Windows](http://www.urielkatz.com/archive/detail/memcached-64-bit-windows/) (64 bits, [Memcached 1.4.2](http://www.urielkatz.com/projects/memcached-win64/memcached-win64.zip))


A continuación, si queremos instalar **Memcached como un Servicio**, debemos seguir los siguientes pasos:



	
  * descomprimir el contenido del zip en nuestro lugar de preferencia (por ejemplo **c:\memcached**)

	
  * si estamos corriendo Windows Vista o 7, debemos hacer click derecho en el archvo **memcached.exe** y clickear la opción **Propiedades**. Se va a mostrar una ventana en la que tenemos que ir a la solapa de **Compatibilidad**, y ahi debemos buscar la parde de **Nivel de privilegios** y marcar la opción **Ejecutar este programa como administrador** y finalmente **Aceptar**.

	
  * ahora desde la linea de comandos debemos ejecutar:

    
    c:\memcached\memcached.exe -d install -m 64 -l 127.0.0.1 -p 11211




	
  * listo, Memcached ya está instalado como un servicio que va a atender en el puerto 11211 (puerto standard de Memcached) de la IP 127.0.0.1 con 64 Mb de memoria, ahora para iniciarlo ejecutamos:

    
    c:\memcached\memcached.exe -d start




	
  * y para frenarlo:

    
    c:\memcached\memcached.exe -d stop





Si no deseamos instalarlo como un servicio, **simplemente podemos ejecutarlo** de la siguiente manera:

    
    c:\memcached\memcached.exe -m 128 -l 192.168.10.101 -p 11222


Esto va a iniciar una instancia de Memcached atendiendo en el **puerto 11222** de la IP **192.168.10.101** con **128 Mb** de memoria para almacenamiento de datos. Para finalizar su ejecución simplemente presionamos **Contro+C** dentro de la ventana de linea de comandos.


#### Linux


Si son usuarios de Linux la instalación recomendada son los paquetes de cada distribución. Si utilizan una **distribución basada en Debian** es tan simple como ejecutar:

    
    sudo apt-get install memcached


Para **distribuciones basadas en RedHat**:

    
    sudo yum install memcached


El archivo de configuración usualmente está en **/etc/memcached.conf** y su formato es bien simple: son lineas con los parámetros que se le podrian pasar al programa por linea de comando, de modo que para configurar Memcached con **512 mb** de memoria y escuchando en el puerto **11888** de la IP **127.0.0.1** quedaría algo así:

    
    -d
    -m 512
    -p 11888
    -l 127.0.0.1
    -u nobody


El parámetro **-d** hace que el programa se "demonice" o, en otras palabras, que corra de fondo como un servicio. Y, como se imaginarán, el parámetro **-u nobody** le indica al programa que se debe ejecutar con los permisos del usuario nobody. Resta **reiniciar el servicio** para que tome los cambios de configuración:

    
    sudo /etc/init.d/memcached restart





### Instalando la extensión php_memcached


Bueno, a esta altura ya tenemos que tener **Memcached funcionando en nuestro sistema**, primer paso dado, pasemos al segundo: habilitar la extensión **php_memcache**.


#### Windows


Para esto los usuarios de Windows deberán, en caso que no la tengan ya instalada, **bajarla de la siguiente dirección**: [http://downloads.php.net/pierre/](http://downloads.php.net/pierre/) (**buscar la versión acorde a su servidor web**, ya sea este "Thread Safe", para Apache por ejemplo, o "Non Thread Safe", para IIS por ejemplo), **descomprimir** el contenido del archivo zip en una carpeta temporal y **copiar el archivo php_memcache.dll a la carpeta de extensiones** (usualmente **c:\php\ext**). Ahora debemos activar la extensión en el archivo **php.ini**, agregando la siguiente linea:

    
    extension=php_memcache.dll


Ahora debemos **reiniciar el servidor web** para que tome los cambios de configuración de PHP.


#### Linux


Los usuarios de **distribuciones basadas en Debian** deberán instalarlo de la siguiente manera:

    
    sudo apt-get install php5-memcache


Para **distribuciones basadas en RedHat** tenemos que ejecutar:

    
    sudo yum install php-pecl-memcache


En ambos casos **la configuración de PHP se hará automáticamente** y solo restará **reiniciar el servidor web** para que tome la nueva configuración (asumiendo que es Apache2, por poner un ejemplo):

    
    sudo /etc/init.d/apache2 restart


Listo, ya tenemos la extensión **php_memcache** funcionando, ahora a probar!



### Probando nuestra instalación


Creemos el siguiente script dentro de algúna carpeta pública de nuestro servidor web:
[php]connect("127.0.0.1", 11211);

// obtenemos la versión del servidor de Memcached
echo "Versión del servidor: " . $memcache->getVersion() . "  
\n";

// definimos un objeto standard
$objeto = new stdClass;

// y le agregamos algúnas propiedades
$objeto->descripcion = "un objeto";
$objeto->numero = 123456;
$objeto->un_array = array(1, 'dos', 3, 'cuatro');

// guardamos el objeto en Memcached
// bajo la llave "llave-del-objeto"
// y con una expiración de 30 segundos
$memcache->set("llave-del-objeto", $objeto, false, 30);

// traemos los datos guardados y los mostramos
echo "Datos guardados:  
\n";
var_dump($memcache->get("llave-del-objeto"));
?>[/php]
Si todo funcionó bien, esto debería devolver lo siguiente:
`Versión del servidor: 1.4.2  

Datos guardados:  

object(stdClass)#3 (3) {
  ["descripcion"]=>
  string(9) "un objeto"
  ["numero"]=>
  int(123456)
  ["un_array"]=>
  array(4) {
    [0]=>
    int(1)
    [1]=>
    string(3) "dos"
    [2]=>
    int(3)
    [3]=>
    string(6) "cuatro"
  }
}`



### La Clase Memcache


Como vimos en el código de la prueba, la extensión **php_memcache** nos provee una clase, llamada **Memcache**, que nos permite interactuar con el servidor de Memcache. Esta clase nos provee una serie de métodos, de los cuales vamos a repasar los más relevantes:



	
  * bool **Memcache::connect** ( string _$host_ [, int _$port_ [, int _$timeout_ ]] )
Esta función nos permite conectar con un servidor **$host**, en un puerto **$port**, dentro de un tiempo de espera determinado por el valor de **$timeout**, y nos devuelve un booleano indicando si pudo o no realizar la conexión.

	
  * bool **Memcache::set** ( string _$key_ , mixed _$var_ [, int _$flag_ [, int _$expire_ ]] )
Guarda un valor **$var** con una llave **$key**. El valor de **$expire** determina el tiempo de expiración en segundos (0 para que no expire nunca, aunque esto no es garantizado por Memcached, ya que este al quedarse sin memoria comienza a descartar los pares de datos más antiguos). El valor de **$flag** determina que opciones aplicar a la operación (la constante _**MEMCACHE_COMPRESSED**_ indica el uso de compresión por medio de zlib).

	
  * string **Memcache::get** ( string _$key_ [, int _&$flags_ ] )
array **Memcache::get** ( array _$keys_ [, array _&$flags_ ] )
Devuelve los datos previamente guardados, siempre que el valor con la llave indicada exista en el servidor. Se puede pasar tanto una sola llave (**$key**), o un array de llaves (**$keys**) para obtener un array de valores. En caso de pedir un array, resultado contendrá solamente los valores de las llaves encontradas. El parámetro **$flags** puede reicibr los mismos valores que apra **Memcache::set**.

	
  * bool Memcache::delete ( string $key )
Elimina la llave $key (y el valor asociado a ella).


Otros métodos útiles expuestos por esta clase son:

	
  * bool **Memcache::add** ( string _$key_ , mixed _$var_ [, int _$flag_ [, int _$expire_ ]] )
Similar a Memcache::set, pero este guarda el valor **sólo si la llave indicada aún no existe** en el servidor, en caso contrario devuelve FALSE.

	
  * bol **Memcache::replace** ( string _$key_ , mixed _$var_ [, int _$flag_ [, int _$expire_ ]] )
También similar a Memcache::set(), este método permite reemplazar el valor de una **llave ya existente**. Si se usa con una llave inexistente devuelve FALSE.

	
  * int **Memcache::increment** ( string _$key_ [, int _$value_ = 1 ] )
Incrementa el valor de una llave **ya existente** según **$value**. Si el valor de la llave indicada no es numérico y no se puede convertir a número, cambiará su valor a **$value**.

	
  * int **Memcache::decrement** ( string _$key_ [, int _$value_ = 1 ] )
Similar a **Memcache::increment** pero este decrementa el valor de la llave según $value. Este método, al igual que **Memcache::increment** no funcionan con valores guardados con compresión.

	
  * bool **Memcache::flush** ( void )
Marca todos los valores del servidor como expirados, por consecuente eliminandolos.





### Memcached para almacenar las sesiones


Finalmente nos queda cubrir el uso de **Memcached como almacenamiento de información de sesiones de PHP**. Si fuimos siguiendo las distintas configuraciones de este artículo, a este punto estamos en condiciones de usar Memcached como storage de sesiones con **sólo modificar algúnas lineas del archivo php.ini**:

    
    session.save_handler = memcache
    session.save_path = "tcp://127.0.0.1:11211"


Esto le indica a PHP que debe usar la extensión **php_memcache** como manejador de sesiones y que se debe conectar por TCP al **puerto 11211** de la IP **127.0.0.1** para almacenar ahi los datos de sesión.

Como se explicó en la introducción de este artículo, aplicar esta configuración en todos los servidores de un pool que esté detrás de un balanceo de carga nos haría ganar considerablemente en performance, ya que todos los servidores accederían a los mismos datos de sesión sin necesidad de implementar soluciones más "costosas" a nivel consumo de recursos, como pueden ser carpetas de archivos compartidas entre los servidores.

Existen varias otras opciones de configuración vinculadas al uso de **php_memcache** como manejador de datos de sesión, por es les recomiendo leer la [página del manual al respecto](http://www.php.net/manual/es/memcache.ini.php).



### Conclusión


Bueno, hemos cubierto desde **la teoría de los distintos escenarios** donde **Memcached** puede hacernos las cosas más faciles, pasando por **la instalación del servicio**, hasta el uso y **la API que la extensión php_memcache nos expone**. Queda experimentar con esto y encontrar los casos donde podamos aplicar estas técnicas y lograr mejores resultados de nuestras aplicaciones.

Espero sus comentarios y que esto les haya sido de utilidad y los ayude a desarrollar más y mejores aplicaciones web en PHP.
