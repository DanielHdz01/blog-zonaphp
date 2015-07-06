title: Volver al branch anterior de git en un suspiro
date: 2013-07-07 00:00:01
tags:
category: Quick Tips
---
Si hubiera sabido esto, me hubiera ahorrado al menos algunos meses de tipeo.

Resulta que si uno está trabajando entre dos branches distintos de git, en vez de utilizar el nombre completo del branch...
```
git checkout feature/make-something-better
git checkout master
git checkout feature/make-something-better
```
Podríamos haber tipeado simplemente...
```
git checkout feature/make-something-better
git checkout master
git checkout -
```

De seguir realizando _checkouts_ entre estos dos branchs, solamente deberíamos utilizar el caracter `-` para alternar entre esos dos.