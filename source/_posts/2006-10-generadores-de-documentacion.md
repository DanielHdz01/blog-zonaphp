title: Generadores de Documentación
author: webstudio
categories:
  - Principiantes
date: 2006-10-30 22:20:59
tags:
---
Por motivos laborales, me vi en la necesidad de **documentar grandes cantidades de código** últimamente, un proyecto muy grande con muchos objetos ubicados en distintos directorios. Además, sumado al hecho de que es muy probable que se agreguen más programadores, la necesidad de tener una documentación del código ya no era por el mero hecho de la **mala memoria** propia, sino que se tornaba realmente necesario para que puedan **ponerse al día rápidamente**.<!-- more -->

Así que buscando en **Google**, caí rápidamente en dos proyectos, creados en PHP, para generar documentación en base al código. Uno llamado [PHPDocumentor](http://www.phpdoc.org/) y el otro [PHPDoc](http://www.phpdoc.de/) (el que diga que los programadores no tienen imaginación a la hora de poner nombres, que sepa que se equivoca). El primero, parece que se erigió como el standard en la generación de documentación, mientras que el segundo, era una opción interesante a la hora de encontrar un reemplazo. Lo que no esperaba era encontrarme con un leve problema: **_Ninguno de los dos funcionó en mi máquina de desarrollo, ni bien instalados_**. Muy extraño, porque recuerdo **haberlos utilizado en el pasado**, pero esta vez, no hubo caso. Les edité el código fuente, cambié extensiones y constantes. Nada.

<img src="{% asset_path doxygen.png %}" style="margin: 10px; float: right;">Así es como caí en las **dos soluciones** que estoy utilizando actualmente y con las cuales, tengo resuelto el tema de la documentación en el mediano plazo. El primero de ellos, es un proyecto llamado [**Doxygen**](http://www.stack.nl/~dimitri/doxygen/), muy conocido en el ámbito de **Java y C++**, pero que me sorprendió gratamente al ver que también **funcionaba con PHP**. Mucha gente lo utiliza (por ejemplo, la gente de [KDE](http://www.kde.org)) y ya veo por qué: crea la documentación separada en lista y jerarquía de clases, por archivos, módulos o estructuras de datos. Además ofrece la posibilidad de generar archivos HTML,  [CHM](http://en.wikipedia.org/wiki/Microsoft_Compressed_HTML_Help), [RTF](http://en.wikipedia.org/wiki/Rich_Text_Format), [PDF](http://en.wikipedia.org/wiki/Portable_Document_Format), [LaTeX](http://en.wikipedia.org/wiki/LaTeX), [PostScript](http://en.wikipedia.org/wiki/PostScript) o [man pages](http://en.wikipedia.org/wiki/Unix_manual).

### PHPXref.com

Esto parecería ser suficiente, si no fuera porque encontré otro generador, llamado [**PHPxRef**](http://phpxref.sourceforge.net/), con una característica muy interesante: linkea toda llamada de un método, función, variable o constante, hacia el **lugar del código donde está definido** (aunque sea en otro archivo). Complementa a la perfección a Doxygen y como éste, son aplicaciones que se instalan en la PC (si usan windows, aunque también están disponibles para linux) y pueden utilizarse sin problemas, y sin tener PHP instalado. Si quieren ver un ejemplo de PHPxRef, pueden acceder a [PHPxRef.com](http://www.phpxref.com), donde hay una lista de documentación generada de algunas aplicaciones Open Source conocidas.

### ¿Pero como se documenta el código?

Esto merecería ser parte de un artículo aparte, lo cuál esto no pretende ser, pero básicamente, un standar en la documentación de código, es utilizar la sintáxis **JavaDoc** o **PHPDoc** (básicamente, lo mismo, pero con otro nombre). Si tuviéramos una clase definida en un archivo, la sintáxis PHPDoc sería:

```php
/**
 * MiClase
 * @uses OtraClase, Logger
 * @package Framework
 * @version $id$
 * @author Pablo Rigazzi
 *
 * Description: Esta es una clase de ejemplo, solo para asegurarnos que todos entiendan como va la cosa.
 */
class MiClase extends OtraClase {
}
```

Así, como verán, el comentario en vez de comenzar como usualmente lo hace, con **/***. lo hace con un doble asterisco: **/****. Esta sutil diferencia, permite que un programa pueda leer los comentarios cercanos a las declaraciones de clases o métodos, y agregar el contenido a la documentación. Así como ven palabras clave especiales (**@uses, @author, @version, @package**, etc), hay muchas otras para indicar, por ejemplo, los tipos de parámetros que recibe una función (**@param**) o el valor que la misma devuelve (**@return**). Para los más curiosos, les dejo una explicación sobre [Como documentar código](http://www.lab.dit.upm.es/~lprg/material/apuntes/doc/doc.htm).
