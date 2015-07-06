title: Determinar rápidamente si es el último día del mes
date: 2013-02-06 23:36:59
tags:
category: Quick Tips
---
Seguramente en algún momento han tenido que escribir un _cron job_ que realize alguna tarea distinta solo el último día del mes. Con este pequeño script, no puede ser más sencillo.

``` php
<?php
if (gmdate('t') == gmdate('d')) {
    echo 'Hurra! Es el último día del mes!';
}
?>
```

Esto funciona maravillosamente, ya que `gmtdate('d')` retorna el día actual, y `gmtdate('t')` retorna el último día del mes actual. Si son iguales, voilá! 
