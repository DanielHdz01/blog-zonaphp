title: "¿Qué esperar de PHP5?"
author: webstudio
categories:
  - PHP Avanzado
date: 2003-04-30 04:08:38
tags:
---
Finalmente, aqui tenemos uno de los tutoriales sobre las nuevas características de PHP5, más detallados que hay. Todo lo que hay que saber, sobre esta nueva versión de nuestro lenguaje de programación preferido.<!-- more -->

### Introducción

Muy pocas veces se vió tanta espectativa ante el lanzamiento de una nueva versión del PHP. Y no es para menos, ya que las nuevas características que incluirá PHP5 serán cruciales para que los desarrolladores y los Ingenieros en Sistemas comiencen a tomar mucho más en serio al lenguaje, y que sea más aceptado para desarrollos medianos a grandes, ofreciendo la posibilidad de escribir mejor código y mucho más claro. En este artículo explicaré las mejoras más notables de la nueva versión de nuestro lenguaje favorito.

Las características que propician a la **Programación Orientada a Objetos** (POO) bajo PHP, han sido extensamente revisadas y mejoradas, más que nada oyendo las necesidades que los desarrolladores clamaban como fundamentales. Objetos por referencia, Clases Abstractas, Interfases, variables y funciones privadas y protegidas, son sólo algunas de ellas, que harán que nuestro lenguaje preferido, sea aún mucho más poderoso y flexible que antes.

Este tutorial pretende ser una introducción **lo más completa posible** a los principales cambios que nos encontraremos, a la hora de programar en PHP y como estos cambios pueden (o no) afectar a nuestras aplicaciones existentes. Se debe tener en cuenta que muchos de los ejemplos que veremos, deben ser catalogados como "**experimentales**" y es posible que algunos no sean incluidos del todo en el nuevo motor del PHP, el Zend Scripting Engine 2.0. Igualmente, trataremos de ir actualizando este documento hasta que PHP5 salga a la calle, de manera que siempre cuente con los últimos detalles. También, supongo ciertos conocimientos por parte del usuario, en cuánto a programación en general y POO en particular.

Si Uds. mismos quieren practicar y probar los ejemplos que veremos, necesitan un servidor [Apache](http://httpd.apache.org) corriendo e instalar alguno de los Snaps de la versión de desarrollo del PHP 5, que la pueden conseguir en [http://snaps.php.net](http://snaps.php.net). Hay versiones tanto para sistemas *nix o para Windows.

Ahora, que comience la fiesta.

### Nuevo Modelo de Objetos

Definitivamente, el principal cambio que más ha resonado desde que se conoce que se está desarrollando PHP5, sin dudas es el cambio en el manejo de los objetos. En la actualidad, en PHP4, los objetos son tratados igual que otros tipos de datos básicos, como los enteros o los arreglos. O sea, cuando los programadores realizan operaciones sobre los objetos, como asignación de variables o cuando son pasados como parámetros a funciones, todo el objeto es **copiado**. Lamentablemente, este comportamiento (que fue más que nada heredado del PHP3 hacia el PHP4 , limita en gran medida el Modelo de Objetos, llevando generalmente a prácticas **extras** (como el paso o el instanciamiento de objetos manualmente por referencia).

``` php 
<?php
class Marca
{
    var $nombre;
    
    function nombre($nombre)
    {
        $this->nombre = $nombre;
    }
}

function cambiaNombre($bebida, $nombre)
{
    $bebida->nombre($nombre);
}

// Evitamos una doble copia de objeto
$bebida = &new Marca;
$bebida->nombre('Coca-Cola');
var_dump($bebida->nombre);
cambiaNombre($bebida, 'Pepsi');
echo "{$bebida->nombre} la opción de la nueva Generación\n";

/* Esto imprime en pantalla :
     Coca-Cola la opción de la nueva Generación */
?>
```

Aqui vemos como el objeto que es "**operado**" dentro de la función `cambiaNombre()`, no es más que una copia del objeto al que realmente queremos cambiar. Este tipo de comportamiento es justamente el que no siempre es el deseado (de hecho, es muy raro que se necesite una copia del objeto).

El nuevo Modelo de Objetos propuesto por PHP5, que se ha visto un poco influenciado por Java, se diferencia en que al crear un objeto, **recibimos un apuntador hacia el objeto**, y no el objeto en si mismo. Cuando este apuntador es pasado como parámetro a funciones, asignado o copiado, SOLO el apuntador es el pasado, asignado o copiado. El objeto nunca es copiado o duplicado, evitando comportamientos indeseados o una mala utilización de los recursos, al tener varias copias de un mismo objeto en memoria todo el tiempo. Esto mismo, también se aplica a los objetos que son devueltos por una función, no teniendo que declarar implícitamente en la definición de la función que se devolverá un objeto por referencia. Esto es muy útil sobre todo en algunos **Patrones de Diseño** (o _Design Patterns_), como el Singleton, que están pensados para siempre devolver una misma instancia de un objeto:

``` php
<?php
function Singleton()
{
    static $instancia = null;

    if ($instancia === null)
    {
        $instancia = new Objeto();
    }

    return $instancia;
}

$obj = Singleton();
$obj->setMiembro("Un Valor");
$obj = Singletron();
echo $obj->getMiembro();
/* Imprime : Un Valor */
?>
```

La "vieja" sintaxis de pasar objetos por referencia, seguirá funcionando, aunque con objetos, no será necesaria. Es posible que el Zend Engine 2 también venga con una **directiva de configuración** (en el php.ini) que indique que el PHP se comporte de manera igual que PHP4, con el paso de objetos por valor, pero que lanze un error **E_NOTICE** cada vez que esto suceda, de manera que los programadores puedan migrar gradualmente sus aplicaciones hacia el nuevo comportamiento.

Pero esta nueva cualidad en el manejo de los objetos en PHP5, que en si es muy útil, viene acompañada de otras características, derivadas de esta primera, que nos ayudarán aún más a programar más rápido y mejor. A continuación, **enumeraremos algunas de las más importantes mejoras**, relacionadas con el nuevo Modelo de Objetos.

### Unificación de Constructores

Hasta ahora, para crear un **constructor** de una clase (o sea, una función que se ejecuta _cada vez_ que se instancia un nuevo objeto de esa clase), era necesario llamar a un método de la misma manera que la clase. Este método entonces, era **automáticamente** ejecutado por el ZE (Zend Engine). Para llamar al constructor de una super clase (algo muy común en POO), entonces era **necesario saber el nombre de la clase** a la cual nuestro objeto extiende :

``` php
<?php
class Foo
{
    function Foo()
    {
        print "Mucho gusto, soy el constructor de Foo.";
    }
}

class Bar extends Foo
{
    function Bar()
    {
        parent::Foo();
        print "Que tal? Yo soy el constructor de Bar.";
    }
}

class FooBar extends Bar
{
    function FooBar()
    {
        parent::Bar();
        print "Yo soy una tercera Clase para mostrar el ejemplo";
    }
}
?>
```

En el ejemplo, tenemos tres clases, FooBar que extiende a Bar, y ésta última que extiende a Foo. Ahora, por un momento supongamos que queremos **mover** a FooBar dentro del **árbol de jerarquía**, ahora extendiendo a Foo en vez de extender a Bar. Para ello, no solo sería necesario cambiar el "extends Bar" por un "extends Foo" sino que además deberíamos **cambiar el nombre del método** a llamar en el constructor.

En realidad, actualmente existe una "solución" a este problema. En base a mi experiencia anterior en otros proyectos en los que necesitabamos resolver este pequeño "inconveniente", es que llegamos a una solución de este estilo :

``` php 
<?php
class A
{
    function A($mensaje)
    {
        echo "Dentro de a: $mensaje";
    }

    function getPadre()
    {
        return get_parent_class(get_class($this));
    }

    function constructorPadre()
    {
        $args = func_get_args();
        if (($padre = $this->getPadre()) == NULL) return;
        call_user_func_array(array(&$this, $padre), $args);
    }
}
class B extends A
{
    function B($mensaje1, $mensaje2)
    {
        $this->constructorPadre($mensaje1);
        echo "Dentro de b: $mensaje2";
    }
}
$B = new B("Mensaje para A", "Mensaje para B");
?>
```

Lo que hacemos entonces, es crear un método, dentro de una super clase que **nuestros objetos extenderán**, que una vez invocado, lo que hace es:

  * Recibir los parámetros que le mandemos
    
  * Averiguar el nombre de la SuperClase
    
  * Si existe una SuperClase, entonces llamar a su constructor, pasándole los parámetros recibidos.

De esta manera, **ya no importa** conocer el nombre de la Super Clase, es el método mismo el que la averigua y la adapta en caso de que nuestro objeto sea movido dentro de la jerarquía de clases, siempre y cuando cuente con estos métodos disponibles. 

Afortunadamente, en PHP5, ya no tendremos que buscar soluciones caseras para este problema, dado que sea ha unificado el nombre que deben tener los métodos constructores. El mismo es "**__construct**" (con doble guión bajo al comienzo).

``` php 
<?php
class Foo
{
    var $variable;
    function __construct()
    {
        $this->variable = 'Utilizando objetos con el nuevo constructor __construct';
    }
}
$f = new Foo();
?>
```

Así, el problema anterior queda completamente resuelto, **con un nombre estándar** para nuestros constructores, ya no hay problemas de tener que cambiar nuestro código, en el caso de que nuestras clases sean cambiadas de lugar. Por compatibilidad con el código existente, el Zend Engine 2 (ZE2), **primero buscará un método llamado '__construct'** para ejecutarlo como constructor; de no encontrarlo, entonces buscará un método que **se llame igual que la clase** (simulando el comportamiento de PHP4).

### Destructores

Los **destructores**, en la Programación Orientada a Objetos, son una herramienta generalmente muy utilizada. Cuando la última **referencia** a un objeto es destruida, antes de que el objeto sea liberado de la memoria, se llama a uno de los métodos del objeto (el método destructor) que realizará su tarea y luego el objeto **morirá** (triste, no?). Estos métodos, pueden **grabar algún mensaje** en algún archivo de Log, **liberar archivos o recursos** hacia bases de datos, **destruir otros objetos**, etc.

De momento, esta característica **no existía** en los objetos, pero se podía simular utilizando la función `register_shutdown_function()` que era ejecutada cuando todo el script finalizaba. Ahora, en PHP5, tenemos la posibilidad de definir un método destructor explícitamente. Éste método, debe ser llamado **__destruct()**:

``` php
<?php
class Objeto
{
    var $variable;
    
    public function __construct()
    {
        echo 'Utilizando objetos con el nuevo constructor __construct';
    }
    
    public function __destruct()
    {
        echo 'Destruyendo un objeto con el nuevo método __destruct. Bye Bye!';
    }
}
$o = new Objeto();

/**
* Esto produciría :
* Utilizando objetos con el nuevo constructor __construct
* Destruyendo un objeto con el nuevo método __destruct. Bye Bye!
*/
?>
```

Al igual que los contructores, **los destructores de las Super Clases no son llamados implícitamente**, así que para ejecutar estos destructores se debe llamar explícitamente a `parent::__destruct()` en el cuerpo del destructor. Esta nueva característica no debería traer problemas de compatibilidad, salvo en aquellas clases que tuvieran un método llamado __destruct.

### Clonado de Objetos

Muchas veces, al hacer una **copia** de un objeto, no nos interesa tener una copia exacta del mismo, sino que lo que buscamos es un objeto del **mismo tipo** que el copiado, pero con algunos recursos o características propias del nuevo objeto, y no "copiadas" del primero. Con el nuevo modelo de objetos de PHP5, al hacer un `$objeto2 = $objeto1` ya no estamos copiando un objeto, sino creando una nueva referencia al mismo, por lo que es necesario un mecanismo para copiar objetos, sin tener que llamar al operador **new**. Aquí es dónde entra en juego, el método `__clone()`.

``` php
<?php
class Clonable
{
    var $variable;
    function __construct()
    {
        $this->variable = 'Variable Clonada';
    }
}

$objeto1 = new Clonable();
$objeto2 = $objeto1->__clone();
?>
```

Por defecto, el Zend Engine 2 busca un método **__clone()** definido por el usuario, si no lo encuentra, entonces llama a un método **__clone()** por defecto, que crea un objeto copiando todos los atributos del original. Pero como **__construct()** y **__destruct()**, el método **__clone()** puede ser sobreescrito para hacer lo que el usuario desee, y de esta manera, se puede controlar mejor el clonado de objetos:

``` php
<?php
class Clonable
{
    static $id = 0;
    
    function __construct()
    {
        $this->id = self::$id++;
    }
    
    function __clone()
    {
        /*
         * Noten el uso de $that para referirnos
         * a las variables del objeto que estamos
         * intentando clonar, y $this para referirnos
         * a las variables del "clon".
         */
        $this->nombre = $that->name;
        $this->direccion = 'Buenos Aires';
        $this->id = self::$id++;
    }
}
// Seteamos un nuevo objeto
$obj = new Clonable();
$obj->nombre = 'Pablo';
$obj->address = 'Rotterdam';

print $obj->id . "\n";

// Y lo clonamos utilizando nuestra
// propia versión de __clone()
$obj2 = $obj->__clone();

print $obj2->id . "\n";
print $obj2->nombre . "\n";
print $obj2->direccion . "\n";
?>
```

Así podemos ver como obtenemos un nuevo objeto, pero **no una copia exacta** del primero, sino que pudimos **controlar** que partes del objeto clonado permanecían iguales, y que parten cambiaban automáticamente (el id, en el ejemplo) o manualmente (la dirección). Nuevamente, la compatibilidad de PHP5 en cuanto a código ya existente, depende de que ya existiera objetos con un método **__clone();** que pueden no ejecutarse como el programador ideó originalmente.

### Otros métodos nuevos

Hasta ahora vimos como PHP5 incorpora constructores unificados, destructores y el nuevo método __clone(), pero estas no son todas las sorpresas que el nuevo modelo de objetos nos tiene deparadas. Existen **otros métodos**, muy útiles a la hora de trabajar con objetos, que nos ayudarán en nuestra programación diaria y en adaptarnos más a los estándares de programación: 

* `__call()`
* la dupla `__set()` / `__get()`.

Muchas veces me sucedió que por no leer bien la API de un objeto, terminaba llamando a métodos que en realidad **no existían** o que tenian un nombre diferente. Ante esto, el script producía un "**Fatal Error** - método _Tal_ no está definido en el Objeto _Juancito_" y terminaba la ejecución. Bastante útil a la hora de "debuggear" nuestra aplicación, pero molesto si el error era generado en el servidor de producción (Si, sé que en un servidor de producción deben desactivarse todos los errores y utilizar otros métodos para generar errores, pero eso será tema de OTRO tutorial.).

Por suerte, la buena gente que programa PHP, incorporó el nuevo método **__call()**, que es automáticamente ejecutado en caso de ser llamado un método que **no está definido** en nuestra clase :

``` php
<?php
class Objeto
{
    /*
    * El prototipo es algo así :
    * mixed __call(string $metodo, array $atributos);
    */
    function __call($metodo, $atributos)
    {
        echo ("EL método \"$metodo\" fue llamado y no existe  
");
        // Revisamos los parámetros
        var_dump($atributos);
    }
}
$obj = new Objeto();
$obj->probando(1, 2, "3");
?>
```

Útil, ¿no creen? Bueno, este método puede ser **más útil de lo que parece**. Y los que programan mucho Orientado a Objetos, ya se deben estar imaginando porqué. Lo que hace muy interesante a este método es que permite simular la **sobrecarga de métodos por tipo de operador**, algo que no era posible anteriormente con PHP (si se podía simular la sobrecarga de métodos por cantidad de parámetros).

``` php
<?php
class Objeto
{
    function __call($metodo, $atributos)
    {
        if (is_integer($atributos[0])) {
            $tipo = "entero";
        } else if (is_object($atributos[0])) {
            $tipo = "objeto";
        }

        $funcion = "$metodo_$tipo";
        return $this->$funcion($atributos[0]);
    }
    
    function sobrecarga_entero($entero)
    {
        echo "El parámetro era un entero";
        echo "Entero : $entero  
";
    }
    
    function sobrecarga_objeto($objeto)
    {
        echo "El parámetro era un objeto";
        var_dump($objeto);
    }
}
$obj = new Objeto();
$obj->sobrecarga(3);
$obj->sobrecarga(new Objeto());
?>
```

Otra de las características más interesantes que ofrece PHP5, es la de poder utilizar los métodos **__set()** y **__get()**, que si se encuentran definidos en una clase, serán llamados siempre que se intente **acceder o modificar** los contenidos de algún miembro del objeto (salvo que ya nos encontremos dentro de alguno de los dos métodos, para evitar recursiones infinitas). Esto puede ser útil si queremos, por ejemplo, realizar un debug o un log de todos los valores que son asignados a un miembro en particular, o si queremos asegurarnos que a un miembro SOLO pueda asignársele valores de 1 tipo en particular.

``` php
<?php
class Restringido 
{
    var $a = array(); // Solo permitiremos que $a sea un array
    var $error = ""; // Logearemos todos los contenidos de $error
    
    function __get($var)
    {
        print "Devolviendo el valor de '$var'";
        return $this->$var;
    }
    
    function __set($var, $valor)
    {
        if ($var == "a") { // Esto sería más "lindo" con un Switch
            if (!is_array($valor)) {
                print "Solo podemos aceptar arreglos";
                return;
            } else {
                $this->a = $valor;
            }
        } else if($var=="error") {
            $this->error = $valor;
            // Suponemos que $this->log es un objeto
            // para Logear a un archivo
            $this->log->agregarLog(time(), $valor);
        }
    }
}
$r = new Restringido();
$r->a = "Hola";
$r->a = array(1, 2, 3, 4, 5);
$r->error = "Este es el mensaje de error";
var_dump($r->a);
?>
```

### Desreferenciamiento de Objetos

Detrás de un nombre tan largo y poco entendible, se esconde una característica **muy interesante**. Supongamos que necesitamos acceder a un _Objeto X_ que se encuentra instanciado y almacenado dentro de un _Objeto Y_. Actualmente, si queremos accederlo de la "**manera correcta**" en PHP (esto es, solo a través de métodos y no accediendo directamente a los miembros internos de un objeto), tendríamos que hacer más o menos lo siguiente :

``` php
<?php
$temp = $objY->getObjX();
echo $temp->metodo();
?>
```

Encontramos la necesidad de utilizar si o si una **variable temporal** a la cual asignar una referencia al objeto que queremos acceder. Gracias al nuevo Modelo de Objetos y con el **desreferenciamiento** de objetos nativo de PHP5, podríamos haberlo resuelto, de una manera un poco más "elegante":

``` php
<?php
echo $objY->getObjX()->metodo();
?>
```

¿Ven como es mucho más sencillo de esta manera? Quizás con un ejemplo de tan solo 2 objetos la diferencia no es tan notable, pero ya cuando tenemos un nivel de 3 objetos o aún más, la ventaja es mucho más evidente :

``` php
<?php
// En vez de hacer algo como esto
$temp = $objZ->getObjY();
$temp = $temp->getObjX();
echo $temp->metodo();

// Podremos hacer algo como esto
echo $objZ->getObjY()
          ->getObjX()
          ->metodo();
?>
```

Este tipo de accesos, incluso podrían extenderse a algo de la forma `$objZ->getObjY()->miembro->metodo()`, en caso de que uno de los apuntadores a un objeto esté almacenado directamente en un miembro y no sea devuelto por una función. Hay que notar, eso si, que la extrema utilización de este recurso, puede ser perjudicial en términos **legibilidad** de nuestros programas, al no saber qué objetos estamos accediendo.

No existe ningun tipo de incompatibilidad de esta característica con versiones anteriores de código, ya que al no existir esta capacidad en versiones anteriores de PHP, no es posible que exista código actualmente que se vea impactado por algún cambio.

### ¿Es todo?

Como podemos ver, PHP5 nos ofrece un nuevo abanico de posibilidades a la hora de Diseñar y Programar Orientados a Objetos, una mejora que seguramente ayudará a que el lenguaje sea más tomado en cuenta a la hora de realizar Proyectos más y más grandes. De todas maneras, PHP no se aleja de su modelo procedural de programación, continuando como el lenguaje rápido y flexible que todos conocemos hasta ahora.

Afortunadamente estas no son todas las novedades que tendremos con PHP5, pero si son todas las que entran en este primer artículo. Quedan en el tintero aspectos tan interesantes como las Interfases, los tipos de variables y funciones Privados, Protegidos  y Públicos, los Namespaces, la implementación de Excepciones para el control de error y muchos temas más, que serán comentados y explicados lo mejor posible.

Hasta entonces, les deseo felices noches de programación extrema y grandes cantidades de cafeína para todos uds.
