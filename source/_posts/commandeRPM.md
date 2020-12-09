---
title: RPM commandes 
date: 2020-12-09 20:56:52
tags:  ["Linux", "RPM"]
---


## Installation d'un RPM 

```
rpm -ivh /packagename.rpm
```

* i pour installation
* vh pour le mode verbose 


## Requêter son RPM


```
sudo rpm -qa
# peut être un peu verbeux ..

sudo rpm -qa | grep -i packagename
```


## Supprimer un package RPM

```
rpm -e <package name>
```