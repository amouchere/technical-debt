---
title: "GIT : HTTPS vers SSH"
date: 2022-08-03
tags: ["Git"]
---

## Passer le remote origin de HTTPS à SSH


Vérifiez le protocole actuel 

```
➜  growlab-project git:(main) git remote -v
origin  https://github.com/amouchere/growlab-project.git (fetch)
origin  https://github.com/amouchere/growlab-project.git (push)

```


Changer le protocol et vérifier le changement

 
```
➜  growlab-project git:(main) git remote set-url origin git@github.com:amouchere/growlab-project.git
➜  growlab-project git:(main) git remote -v
origin  git@github.com:amouchere/growlab-project.git (fetch)
origin  git@github.com:amouchere/growlab-project.git (push)

```

