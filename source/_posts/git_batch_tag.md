---
title: "GIT : traitement sur tag en batch"
date: 2023-04-07
tags: ["Git"]
---

### Récupérer en local tous les tags sur le dépôt distant 

```
➜  git fetch --tags
```

### Lister les tag du dépôt, les trier et récupérer le dernier

```
➜  git tag --list | sort -V | tail -n1
```

### Suppression de tags selon une expression régulière
* Récupère tous les tags commençant par l'expression regulière (ici ^2023)
* Pour chacun d'entre eux, créer un nouveau tag nommé selon le retour de la commande `sed`. (ici remplacer toto par tutu dans les noms des tags)
* supprimer le tag d'origine en local puis en distant.

```
➜  git tag | grep '^2023' | while read tagname; do git tag  "$(echo "${tagname}" | sed "s/toto/tutu/")" "${tagname}"; git tag -d "${tagname}"; git push origin --delete "${tagname}";  done
```


### Pousser tous les tags du dépôt local  sur le dépôt distant 

```
➜  git push --tags
```

### Supprimer tous les tags du dépôt local 

```
➜  git tag -l | xargs git tag -d
```
