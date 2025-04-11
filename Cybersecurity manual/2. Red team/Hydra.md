#  Hydra

##  Commandes les plus utiles
➡️ Brute force sur un login HTTP (formulaire classique)
```
hydra -l admin -P /path/to/wordlist.txt http://target.com/login.php
```

➡️ Brute force sur un service SSH
```
hydra -l root -P /path/to/wordlist.txt ssh://target.com
```

➡️ Brute force sur un service FTP
```
hydra -l user -P /path/to/wordlist.txt ftp://target.com
```

➡️ Brute force sur un service HTTP (basic auth)
```
hydra -L users.txt -P /path/to/wordlist.txt http-get://target.com
```

➡️ Brute force sur un service RDP (Remote Desktop Protocol)
```
hydra -l admin -P /path/to/wordlist.txt rdp://target.com
```

➡️ Brute force avec proxy (socks5, etc.)
```
hydra -l admin -P /path/to/wordlist.txt -socks5 proxy_ip:port http://target.com
```

➡️ Brute force en mode parallélisme (plus rapide)
```
hydra -l admin -P /path/to/wordlist.txt -t 64 ssh://target.com
```

➡️ Vérifier les services en ligne sans brute force (test de connectivité)
```
hydra -t 4 -s 22 -vV target.com ssh
```

➡️ Brute force sur un service telnet
```
hydra -l root -P /path/to/wordlist.txt telnet://target.com
```

➡️ Sauvegarder les résultats dans un fichier de log
```
hydra -l admin -P /path/to/wordlist.txt ssh://target.com -o result.log
```

---

## 🧩 Options utiles (tableau)

|Option|Description courte|
|---|---|
|`-l`|Spécifie le login utilisateur|
|`-L`|Spécifie un fichier contenant une liste d’utilisateurs|
|`-P`|Spécifie un fichier contenant une liste de mots de passe|
|`-p`|Spécifie un mot de passe unique|
|`-t`|Nombre de threads (par défaut : 16)|
|`-s`|Port à spécifier|
|`-vV`|Mode verbeux pour voir l'évolution du bruteforce|
|`-o`|Sauvegarde les résultats dans un fichier|
|`-I`|Utilisation d’un fichier .csv pour les utilisateurs|
|`-S`|IP ou DNS du serveur cible|
|`-M`|Mode de brute force sur plusieurs cibles|
|`-T`|Test de vitesse (timing entre chaque requête)|
|`-f`|Arrêter dès que la première combinaison valide est trouvée|
