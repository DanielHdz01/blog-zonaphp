---
author: webstudio
comments: true
date: 2003-02-24 17:36:36+00:00
layout: post
slug: programacion-orientada-a-objetos-en-php
title: Programación Orientada a Objetos en PHP
wordpress_id: 11
categories:
- Prog. Orientada a Objetos
---

Este artículo escrito originalmente por **Luis Argerich** nos introducirá a la **Programación Orientada a Objetos** (POO o su versión inglesa: OOP) en PHP. En él veremos la forma de programar menos y mejor usando conceptos de **POO** junto con algunos trucos de PHP.<!-- more -->

**Programación Orientada a Objetos en PHP**

Algunos "puristas" afirmarán que PHP no es un verdadero lenguaje orienteado a objetos, lo que es cierto. PHP es un lenguaje híbrido dónde se pueden utilizar POO y programación estructurada tradicional. Pero para ciertos proyectos grandes, quizas deseemos/necesitemos(?) usar POO "pura" en PHP, declarando Clases y solo utilizando Objetos y Clases para el proyecto. Mientras emergen dia a dia proyectos más y más grandes, el uso de POO puede ayudar, el código POO es fácil de mantener, facíl de comprender y fácil de reusar. Y estos son los principios de la Ingeniería de Software. Aplicar estos conceptos en proyectos basados en la web, es la clave para el éxito de futurios sitios web.

Este artículo es una introducción a la **Programación Orientada a Objetos** en PHP. Intentaremos mostrarles como se puede programar menos y mejor utilizando algunos conceptos de **POO** y trucos de PHP. Buena Suerte !

Conceptos de programación orientada a objetos: aunque existen diferencias entre distintos autores, puedo decirles que un lenguaje de **POO** debe poseer:


  * **Tipos Abstractos de Datos y ocultamiento de la Información**
  * **Herencia**
  * **Polimorfismo**

El encapsulamiento en PHP se logra utilizando clases:

``` php
<?php
class Algo 
{
   // En PHP, las clases usualmente se nombran con la primera letra en mayúscula.
   var $x;

    function setX($v) 
    {
    // Los Métodos comienzan en minúscula y luego se usan mayúsculas para
    // separar palabras en el nombre del método.
    // Ejemplo : obtenerRaizCuadrada();
        $this->x = $v;
    }

    function getX() 
    {
        return $this->x;
    }
}
?>
```

Por supuesto, cada uno puede utilizar la nomenclatura que desee, pero utilizar una estandarizada es útil.
Los atributos de los objetos se definen en PHP utilizando la declaración "var" dentro de la clase, estos atributos no tienen tipo hasta que se les asigna un valor. Un atributo puee ser un entero, un arreglo, un arreglo asociativo o incluso otro objeto. Los métodos se definen como funciones dentro de la clase, para acceder a los atributos dentro de los métodos se debe utilizar **$this->nombre**, de otra manera, se utilizaria una variable local al método.

Para crear (instanciar) un nuevo objeto se utiliza el operador **new**:

``` php 
$obj = new Algo;
```

Luego, se pueden acceder a sus métodos asi:

``` php
$obj->setX(5);
$valor=$obj->getX();
```

El método setx le asigna el valor 5 al atributo x en el objeto obj (no a la clase), entonces getx devuelve ese valor: 5 en este caso.

En realidad también se puede acceder a los atributos del objeto de esta manera: $obj->x = 6; sin embargo esta no es una buena práctica de POO. Yo recomiendo acceder a los atributos definiendo métodos para asignarlos y devolverlos. Serás un buen programador orientado a objetos si considerás los atributos como innacesibles y solo utilizando métodos para acceder a ellos. Lamentablemente, PHP aún no ofrece una manera de definir un atributo como privado asi que una mala programación es posible.

La Herencia es sencilla de utilizar en PHP con la palabra reservada extend.

``` php
<?php
class Otra extends Algo
{
    var $y;
    function setY($v) 
    {
        $this->y = $v;
    }

    function getY() 
    {
        return $this->y;
    }
}
?>
```

Objetos de la clase "Otra" ahora poseen todos los atributos y métodos de la clase padre (Algo) más sus propios métodos y atributos. Se puede hacer:

``` php
$obj2=new Otra;
$obj2->setX(6);
$obj2->setY(7);
```

La Herencia múltiple no está soportada por PHP así que no se puede hacer que una clase extienda dos o más clases diferentes.

Se puede sobreescribir un método en la clase derivada tan solo redefiniéndolo, si redefiniéramos getx en "Otra" ya no podríamos acceder al metodo getx en "Algo". Si se declara un atributo en una clase derivada con el mismo nombre de un atributo de la clase base, el atributo derivado "esconde" al atributo de la clase base cuando se intenta acceder al mismo.

Se puedden definir Constructores en las clases. Los Constructores son métodos con el mismo nombre de la clase y que son llamados en el momento en que se instancia un objeto, por ejemplo:

``` php
<?php
class Algo
{
	var $x;
	
	function Algo($y)
	{
		$this->x=$y;
	}
	
	function setX($v)
	{
		$this->x=$v;          
	}
	
	function getX()
	{
		return $this->x;
	}
}
?>
```

Asi que ahora se puede crear al objeto asi :

``` php
$obj=new Algo(6);
```

Y el Constructor automáticamente asigna 6 al atributo x. Los Constructores y los métodos son funciones normales de PHP, por lo que se pueden usar argumentos por defecto.

``` php
function Algo($x="3",$y="5")
```

Entonces:

``` php
<?php
$obj=new Algo(); // x=3 e y=5
$obj=new Algo(8); // x=8 e y=5
$obj=new Algo(8,9); // x=8 e y=9
?>
```

Los argumentos por defecto son utilizandos de la misma manera que en C++, asi que no es posible pasarle un valor a Y dejando que X tome el valor por defecto, los argumentos son asignados de izquierda a derecha y cuando no se encuentran más argumentos, y si la función esperaba más de los que se enviaron, entonces los restantes toman sus valores por defecto.
Cuando un objeto de una clase derivada es instanciado, solo su Constructor es llamado, pero no el de la clase Padre, esto es una lástima porque el encadenamiento de constructores es una característica clásica de la POO. Si quieres llamar al constructor de la clase base, se tiene que hacer explicitamente en el constructor de la clase derivada. Esto funciona porque todos los metodos de la clase padre están disponibles en la clase derivada debido a la Herencia.

``` php
<?php
function Otra()
{
	$this->y=5;
	//llamada explicita al contructor de la clase padre.
	$this->Algo();
}
?>
```

Un mecanismo interesante en la POO es la utilización de Clases Abstractas, estas clases no son instanciables y su único propósito es el de definir una interfase para sus clases derivadas. Los diseñadores y analistas generalmente utilizan Clases Abstractas para forzar a los programadores a derivar clases de ciertas clases base, y de esa manera asegurarse que las nuevas clases tendrán ciertas funcionalidades deseadas. No existe una manera estandar de hacer esto en PHP, pero:

Si necesitas esta funcionaliad, define la clase base y coloca un "die();" en su constructor, de esta manera nos aseguramos que la clase no es instanciable, luego define sus métodos (interfase) colocando "die()" al final de cada uno, de esa manera, si un programador no sobreescribe el método entonces ocurre un error. Incluso puede ser necesario estar seguro que una clase es derivada de una clase base en particular, entonces es recomendable agregar un método en la clase base que la identifique (que retorne algun "identificador") y verificar esto cada vez que se recibe un objeto como argumento. Por supuesto esto no funciona si el diabólico programador sobreescribe el método en la clase derivada, pero generalmente el problema es tratar con programadores holgazanes, no con programadores diabólicos! Por supuesto, es mejor mantener la clase base fuera del alcance de los programadores, tan solo presentarles la interfase y hacerlos trabajar.

No hay destructores en PHP.
La "Sobrecarga" ( que es diferente a la "Sobreescritura" ) no es soportada por PHP. En POO uno "sobrecarga" un método cuando se definen dos o tres métodos con el mismo nombre, pero con diferentes números o tipos de parámetros (dependiendo del lenguaje). No existen tipos de datos en PHP, así que sobrecargar por tipos de datos no funciona, sin embargo, sobrecargar por cantidad de parámetros tampoco existe.

Es muy útil a veces en POO sobrecargar los Constructores para poder instanciar el objeto de diferentes maneras (pasándole diferentes números de parámetros). Un truco para hacer esto en PHP es:

``` php
<?php
class MiClase
{
	function MiClase()
	{
		$nombre="MiClase".func_num_args();
		$this->$nombre();
		// Notar que $this->nombre(); generalmente estaría mal
		// pero aqui $nombre es un string con el nombre del
		// método que se va a llamar
	}
	
	function MiClase1($x)
	{
		//código;
	}
	function MiClase2($x,$y)
	{
		//código;
	}
}
?>
```

Con este "trabajo extra" en la clase, el uso de la misma se vuelve transparente al usuario ( de la clase, no del sitio ) :

``` php
<?php
$obj1=new MiClase('1'); // llamará a MiClase1
$obj2=new MiClase('1','2'); // llamará a MiClase2
?>
```

Muchas veces esto es muy útil.
**Polimorfismo**

Polimorfismo se define a la habilidad de un objeto de determinar que método invocar para un objeto pasado como argumento en tiempo de ejecución. Por ejemplo, si tenemos una clase "Figura" que define un método para dibujar y dos clases derivadas Círculo y Rectángulo dónde se sobreescriben ese método, se puede tener un método que espere un argumento "x" y luego hacer $x->dibujar(). Si tuvieramos polimorfismo, el método "dibujar" llamado dependería del tipo de objeto que se le pasa a la función. El Polimorfismo es muy sencillo y hasta natural en lenguajes interpretados como PHP. Asi que  afortunadamente PHP soporta Polimorfismo.

``` php
<?php
function dibujar($x)
{
	// Supongamos que este es un método de una clase "Pizarra"
	$x->dibujar();
}

$obj=new Círculo(3,187);
$obj2=new Rectángulo(4,5);

$pizarra->dibujar($obj);    // llamará al método dibujar de Círculo.
$pizarra->dibujar($obj2);   // llamará al método dibujar de Rectángulo.
?>
```

**Técnicas avanzadas de POO en PHP**

Luego de aprender los conceptos básicos de la POO, les puedo mostrar algunas técnicas más avanzadas:

**Serialización**

PHP no soporta Objetos Persistentes. En POO los Objetos Persistentes son aquellos que mantienen su estado y funcionalidad a través de múltiples invocaciones de la aplicación, esto es, la habilidad de "grabar" el objeto a un archivo o base de datos y luego "cargarlo" nuevamente. Este mecanismo es conocido como Serialización. PHP tiene una función de serialización que puede ser utilizada con objetos, y devuelve una cadena que representa a ese objeto. Sin embargo la serialización guarda los atributos pero no los métodos.

En PHP4, si uno serializa un objeto hacia una cadena $s, luego se destruye el objeto y luego se vuelve a des-serializar el objeto hacia $obj, aún se puede tener acceso a los métodos! Yo en realidad no recomiendo esto porque _(a)_ La documentación no garantiza este funcionamiento y en futuras versiones de PHP puede dejar de existir. _(b) _Esto puede llevar a crear "ilusiones" de grabar el objeto serializado en una cadena y salir del script. En futuras ejecuciones del script, no podrás des-serializar la cadena hacia un objeto y esperar que los métodos estén alli, porque la cadena que representa al objeto no guarda los métodos del mismo. Resumiendo, la serialización en PHP es MUY útil para guardar los atributos de un objeto, pero solo eso. (También se pueden serializar arreglos simples y asociativos para guardarlos en disco).


``` php ejemplo
<?php
$obj=new ClaseFoo();
$cadena=serialize($obj);
// Grabamos $cadena a disco

//...algunas miles de instrucciones después....

//Cargamos $cadena del disco
$obj2 = unserialize($cadena);
?>
```

Obtendremos los atributos pero no los métodos (de acuerdo a la documentación). Esto nos lleva a que sea $obj2->x la única manera de acceder a los atributos (ya que no tenemos métodos para hacerlo!) así que no intenten hacer esto en sus casas.

Hay algunas maneras de solucionar este problema, pero se las dejo para que las invetiguen, ya que son muy "chanchas" para este lindo artículo.

Serialización Completa es una característica que apreciaría mucho en futuras versiones de PHP.
**Utilizando Clases para trabajar con datos almacenados**

Una de las mejores cosas de la POO y PHP es que se pueden definir sencillamente clases para manipular ciertas cosas y luego llamar a las clases apropiadas cuando sea necesiario. Supongamos que tenemos un formulario HTML dónde el usuario elige un producto, enviando su ID de producto, nosotros tenemos esa información en una base de datos y queremos mostrar por pantalla su precio, etc, etc. Tenemos productos de diferentes tipos, y la misma acción puede tener diferentes significados para diferentes tipos de productos. Por ejemplo, "mostrar" un sonido puede significar reproducirlo, mientras que para otro tipo de producto puede significar mostrar una imagen guardada en la  base de datos. Entonces podemos utilizar POO y PHP para programar menos y mejor:

Definimos una clase Producto, y los métodos que va a tener (por ejemplo "mostrar"), luego definir las clases para distintos tipos de prouctos que extenderán a la clase Producto (clase ProdSonido, clase ProdVisible, etc...) sobreescribiendo los métodos que definimos en Producto en cada una de estas nuevas clases para que hagan lo que necesitamos. Deberíamos nombrar las clases de acuerdo con la columna "tipo" que guardamos en la base de datos para cada producto (una tabla típica de productos deberia tener id, tipo, precio, descripción, etc). Luego, podemos recuperar el tipo de producto desde la  base de datos e instanciar un objeto de esa clase haciendo :

``` php
<?php

$obj=new $tipo();
$obj->accion();

?>
```

Esta es una característica muy útil de PHP, podremos luego llamar al método "mostrar" de $obj o cualquier otro método sin importar el tipo de objeto que tengamos. Con este tipo de técnica, no es necesario tocar el script que procesa todo, cuando se agregue un nuevo tipo de producto, tan solo agregamos la clase que lo maneje. Esta es una técnica muy poderosa, solo definimos los métodos que los objetos deban tener, sin importar el tipo que sea, los implementamos de diferentes maneras en diferentes clases y los usamos para cualquier tipo de objeto en el script principal. Sin ifs, sin 2 programadores en el mismo archivo, felicidad eterna. ¿No están de acuerdo que programar de esta manera es simple, el mantenimiento es más barato y la reusabilidad se vuelve un hecho ?

Si les toca dirigir un grupo de programadores se vuelve muy sencillo dividir las tareas, cada uno sería responsable por un tipo de objeto y la clase que lo maneja. La internacionalización (mismo sitio, diferentes idiomas) puede lograrse utilizando esta técnica, aplicando la clase que sea neceasaria de acuerdo al campo "idioma" que el usuario elija.

**Copiando y Clonando**

Cuando creamos un objeto $obj, se puede copiar el mismo haciendo $obj2 = $obj, el nuevo objeto es ahora una copia ( no una referencia ) de $obj así que tiene el mismo estado que tenía $obj en el momento en que la asignación se realizó. Algunas veces no queremos esto, simplemente queremos crear un nuevo objeto de la misma clase que $obj, llamado al constructor del nuevo objeto como si hubiésemos utilizado la sentencia new. Esto puede lograrse en PHP utilizando serialización y una clase base de la que las demás extienden.
**Entrando en terreno peligroso**

Cuando serializamos un objeto, obtenemos una cadena con cierto formato, puedes investigarlo si eres curioso, una de las cosas que esta cadena contiene es el nombre de la clase (interesante!) y podemos extraerlo haciendo :

``` php
<?php

$cadena = serialize($obj);
$vec = explode(':',$cadena);
$nombre = str_replace("\"",'',$vec[2]);

?>```

Asi que supongamos que creamos una clase Universo y forzamos que todas las demás clases extiendan a Universo, podemos definir un método clonar en Universo asi :

``` php
<?php
class Universo 
{
	function clonar()
	{
		$cadena=serialize($this);
		$vec=explode(':',$cadena);
		$nombre=str_replace("\"",'',$vec[2]);
		$ret = new $nombre;
		return $ret;
	}
}

// Entonces:
$obj=new Algo();
//Algo extiende a Universo !!
$otro = $obj->clonar();
?>
```

lo que logramos de esta manera es un nuevo objeto de clase Algo creado de la misma manera que utilizando new, el contructor es llamado, etc. No estoy muy seguro que esto sea útil para todos, pero la Clase Universo que conoce el nombre de la clase derivada es un interesante concepto para experimentar. El único límite es la imaginación.

**Nota:** Para todos los ejemplo, utilicé PHP4. Algunas de los conceptos explicados en este artículo pueden no funcionar en PHP3.
