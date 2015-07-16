title: Generando y descargando archivos enormes con PHP
date: 2014-01-03 00:59:32
category: Quick Tips
---
Si bien hace un tiempo escribí sobre como {% post_link forzar-la-descarga-de-un-archivo-con-php %}, lo cierto es que esto solamente funciona con archivos relativamente pequeños, de no más de 2 Megabytes de tamaño. Archivos más grandes (alrededor de cientos de Megabytes) se verían truncados o vacíos, simplemente porque PHP posee un espacio limitado para hacer _output buffering_. 

Entonces, si quisiéramos extender el ejemplo anterior, pero teniendo en cuenta tamaños de archivo mucho más grandes, deberíamos realizar algo así: 

``` php
$filename = 'archivo-xml-muy-grande.xml';

header('Content-type: text/xml');
header('Content-length: ' . filesize($filename));
header('Content-Disposition: attachment; filename="'.$filename.'"');

ob_end_flush();
readfile($filename);
exit;
```