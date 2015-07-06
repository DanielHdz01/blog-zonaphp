title: Parsear archivos CSV con fgetcsv
date: 2011-03-06 22:26:16
tags:
category: 
    - Quick Tips
---
Resulta que hace poco, debido a un pequeño proceso que debíamos correr para importar ciertos valores, encontré dentro de la librería PHP la función `fgetcsv()` que me vino al pelo, justo cuando estaba por ponerme a implementar mi propia solución.

Funciona exactamente igual que `fgets()` solo que realiza un rápido parseo de la línea recientemente leída antes de devolverla a PHP. Su modo de uso es realmente simple. Si contáramos con un archivo separado por comas de este tipo: 

    1, Pablo, Web Developer, Rotterdam
    2, Tom, UX Designer, Amsterdam
    3, Roberto, Team Lead, Rotterdam
    4, Francisco, Papa, Ciudad de El Vaticano

Entonces para leerlo y procesarlo solo necesaríamos el siguiente código: 

``` php
<?php
$counter = 1;
if (($handle = fopen('fgetcsv.csv', 'r')) !== false)
{
    while (($datos = fgetcsv($handle, 1000))) {
        echo sprintf(
            "Se encontraron %d campos dentro de la fila %d\n",
            count($datos),
            $counter++
        );

        foreach ($datos as $dato) {
            echo $dato . "\n";
        }
    }
    fclose($handle);
}
```
