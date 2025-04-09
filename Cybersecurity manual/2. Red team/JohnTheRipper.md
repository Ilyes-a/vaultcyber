# 🔐 John the Ripper Cheat Sheet 

##  Commandes les plus utiles

➡️ Cracker un fichier hash (MD5) avec une wordlist
```
john --wordlist=/path/to/wordlist.txt --format=raw-md5 hash.txt
```

➡️ Cracker un hash SHA256
```
john --wordlist=/path/to/wordlist.txt --format=raw-sha256 hash.txt
```

➡️ Cracker un hash bcrypt (plus lent)
```
john --wordlist=/path/to/wordlist.txt --format=bcrypt hash.txt
```
`
➡️ Cracker avec des règles d’attaque pour les mots de passe complexes
```
john --wordlist=/path/to/wordlist.txt --rules --format=raw-md5 hash.txt
```

➡️ Cracker un fichier `.zip` protégé par mot de passe
```
john --wordlist=/path/to/wordlist.txt --format=zip --rules zipfile.zip
```

➡️ Cracker un fichier `.pdf` protégé par mot de passe
```
john --wordlist=/path/to/wordlist.txt --format=pdf --rules file.pdf
```

➡️ Utiliser un fichier d'outils de brute-force avec des attaques par combinaison
```
john --wordlist=/path/to/wordlist.txt --rules --incremental --format=raw-md5 hash.txt
```

➡️ Cracker un hash NTLM pour les mots de passe Windows
```
john --wordlist=/path/to/wordlist.txt --format=nt hash.txt
```

➡️ Vérifier le statut de cracking (les progressions)
```
john --status
```

➡️ Sauvegarder l'état d'une session pour continuer plus tard
```
john --session=mysession --restore
```

➡️ Utiliser un fichier de hachages combinés (multi-hash)
```
john --wordlist=/path/to/wordlist.txt --format=raw-md5 hash1.txt hash2.txt
```

➡️ Appliquer un dictionnaire inversé pour un mot de passe compliqué (reverse brute-force)
```
john --wordlist=/path/to/wordlist.txt --rules --format=raw-md5 --reverse hash.txt
```


➡️ Cracker des hachages des comptes **shadow** sur Linux
```
john --wordlist=/path/to/wordlist.txt --format=shadow /etc/shadow
```

---

## 🧩 Options utiles (tableau)

|Option|Description courte|
|---|---|
|`--wordlist=<file>`|Utiliser une wordlist spécifique pour les attaques|
|`--format=<type>`|Format de hash (ex: `raw-md5`, `bcrypt`, `sha256`, `nt`)|
|`--rules`|Appliquer des règles d'attaque pour des mots de passe plus complexes|
|`--incremental`|Utiliser une attaque incrémentale pour tenter tous les caractères possibles|
|`--session=<name>`|Sauvegarder et restaurer une session de cracking|
|`--status`|Vérifier l'état actuel du cracking|
|`--restore`|Reprendre une session de cracking interrompue|
|`--show`|Afficher les mots de passe crackés jusqu'à présent|
|`--pot=<file>`|Spécifier un fichier de pot (pour stocker les résultats)|
|`--stdout`|Afficher les mots de passe générés en sortie|
|`--min-length`|Spécifier une longueur minimale pour les mots de passe à cracker|
|`--max-length`|Spécifier une longueur maximale pour les mots de passe à cracker|
|`--mask=<pattern>`|Utiliser un masque pour une attaque ciblée (ex: `?l?l?l?d` pour 3 lettres et 1 chiffre)|
|`--external`|Utiliser un module externe pour le cracking|
|`--wordlist=<path>`|Utiliser un chemin d’accès personnalisé pour la wordlist|

---

##  Techniques avancées

 **Brute Force avec Masque**  
Si tu sais que le mot de passe suit un certain format (ex: 3 lettres + 2 chiffres), tu peux utiliser un masque pour rendre l’attaque plus ciblée et rapide :
```
john --mask='?l?l?l?d?d' --format=raw-md5 hash.txt
```

Ici, `?l` représente une lettre minuscule et `?d` représente un chiffre.

 *Attaque par Rainbow Tables* 
John the Ripper peut aussi être configuré pour utiliser des Rainbow Tables. Pour cela, tu dois les télécharger et les spécifier dans la commande.

*Utilisation de règles avancées avec `--rules=Jumbo`*
Le mode Jumbo de John the Ripper permet d’utiliser des règles avancées de génération de mots de passe pour augmenter les chances de succès dans un temps raisonnable.