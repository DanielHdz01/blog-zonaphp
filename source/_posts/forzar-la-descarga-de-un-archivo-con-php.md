title: Forzar la descarga de un archivo con PHP
date: 2013-03-06 23:45:51
tags:
category: Quick Tips
---
Ya conocemos la historia. Gracias al poder de PHP generamos contenido que debería ser descargable por el usuario, pero el navegador en vez de ofrecer la descarga de un archivo, muestra el contenido dentro de la ventana donde estamos trabajando. 

Por suerte, podemos indicarle muy fácilmente que queremos que este contenido sea tratado como un archivo, y que se ofrezca como una descarga. Si necesitáramos descargar el contenido como xml, entonces deberíamos enviar los headers correctos: 

``` php
header('Content-type: text/xml');
header('Content-Disposition: attachment; filename="NombreDelArchivo.xml"');
```

Y listo. Esto también funciona con otros tipos de contenido, como `application/pdf` (para archivos PDF), o `application/msword` (para archivos Word).