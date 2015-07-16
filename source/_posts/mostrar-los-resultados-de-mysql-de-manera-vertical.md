title: Mostrar los resultados de MySQL de manera vertical
date: 2014-02-11 01:20:17
category: Quick Tips
---
Cuando tenemos que acceder remotamente a un servidor y realizar algunas consultas a MySQL, algunas veces esta información es difícil de leer. Esto se debe al formato por defecto que MySQL utiliza en sus resultados a través de la consola: 

``` sql
mysql> select * from wp_users WHERE ID=2;
+----+------------+------------------------------------+---------------+-------
------------------+------------------------------+---------------------+-------
---------------+-------------+--------------+
| ID | user_login | user_pass                          | user_nicename | 
user_email              | user_url                     | user_registered     | 
user_activation_key  | user_status | display_name |
+----+------------+------------------------------------+---------------+-------
------------------+------------------------------+---------------------+-------
---------------+-------------+--------------+
|  2 | webstudio  | $P3B/Wr1SUfT7g3SgLZ5vy4AS6.tLrwCr1 | webstudio     | direcc
ion.de@gmail.com | http://www.web-studio.com.ar | 2006-05-03 22:27:07 | 
EuotpywqoDBeD1fElw0b |           0 | Pablo        |
+----+------------+------------------------------------+---------------+-------
------------------+------------------------------+---------------------+-------
---------------+-------------+--------------+
1 row in set (0.06 sec)

mysql> 
```

Pero si el mismo comando, lo ejecutamos finalizandolo con `\G` en vez del `;` habitual, los resultados se vuelven mucho más legibles: 

``` sql
mysql> select * from wp_users WHERE ID=2\G
*************************** 1. row ***************************
                 ID: 2
         user_login: webstudio
          user_pass: $P3B/Wr1SUfT7g3SgLZ5vy4AS6.tLrwCr1
      user_nicename: webstudio
         user_email: direccion.de@gmail.com
           user_url: http://www.web-studio.com.ar
    user_registered: 2006-05-03 22:27:07
user_activation_key: EuotpywqoDBeD1fElw0b
        user_status: 0
       display_name: Pablo
1 row in set (0.06 sec)

mysql> 
```

Espero que el Tip les sea de utilidad.



