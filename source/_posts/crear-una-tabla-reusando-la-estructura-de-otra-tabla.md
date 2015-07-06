title: Crear una tabla reusando la estructura de otra tabla
date: 2013-07-26 00:52:16
category: Quick Tips
---
A pesar de los años, el lenguaje SQL y nuestro viejo y querido motor MySQL nos siguen sorprendiendo. Prueba de ello lo que descubrí de casualidad y no dudé en _Twittear_.

<blockquote class="twitter-tweet" lang="en"><p lang="es" dir="ltr">Quieren crear una tabla en mySQL con la misma estructura de otra? Nada más simple : CREATE TABLE nueva_tabla LIKE vieja_tabla;</p>&mdash; Pablo Rigazzi (@prigazzi) <a href="https://twitter.com/prigazzi/status/360876055491383296">July 26, 2013</a></blockquote> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Esto creará una tabla nueva, pero sin los datos de la tabla anterior. Si queremos además, copiar los datos entre las tablas, podemos realizar luego: 

``` sql
INSERT INTO nueva_tabla SELECT * FROM vieja_tabla;
```