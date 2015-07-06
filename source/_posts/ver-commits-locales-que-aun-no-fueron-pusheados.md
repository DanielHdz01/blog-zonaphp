title: Ver commits locales que aún no fueron pusheados
date: 2013-12-17 00:24:06
tags:
category: Quick Tips
---
No pocas veces pasa que estamos trabajando en algo, realizamos ciertos _commits_ en nuestro repositorio local, y luego de que algo nos distrajo momentáneamente, ya no recordamos qué era eso que habíamos _commiteado_, pero aún no _pusheado_ al servidor. Si pedimos un `git status` entonces esto no nos da _demasiada_ información: 
```
# On branch origin/master
# Your branch is ahead of 'origin/master' by 1 commit.
#   (use "git push" to publish your local commits)
```

> DIOS! En qué estaba trajando?!

Afortundamente, gracias a `git log` podemos sacarnos la duda, indicando que queremos ver sólo la diferencia entre nuestro `HEAD` y el branch remoto `origin/master`

```
git log origin/master..HEAD

commit 782b5cc03b3e0961c5fa3ec88e3f8a89665b494d
Author: Pablo Rigazzi <pablo.rigazzi@gmail.com>
Date:   Sep 17 00:29:32 2013 +0200
    Unfinished version of User Class
```

Problema resuelto! 