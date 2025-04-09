# 🦍 FFUF Cheat Sheet 

##  Commandes les plus utiles

➡️ Fuzz de répertoires (wordlist sur les paths)
```
ffuf -u http://target/FUZZ -w wordlist.txt
```
`
➡️ Fuzz avec extension spécifique (ex : `.php`)
```
ffuf -u http://target/FUZZ -w wordlist.txt -e .php
```

➡️ Fuzz d'un paramètre GET
```
ffuf -u http://target/page.php?file=FUZZ -w wordlist.txt
```

➡️ Fuzz d’un champ POST
```
ffuf -u http://target/login -X POST -d "user=admin&pass=FUZZ" -w wordlist.txt
```

➡️ Fuzz avec header personnalisé (auth, token…)
```
ffuf -u http://target/FUZZ -w wordlist.txt -H "Authorization: Bearer FUZZ"
```

➡️ Fuzz sur un sous-domaine (vhost)
```
ffuf -u http://FUZZ.target.com -w subdomains.txt -H "Host: FUZZ.target.com"
```
`
➡️ Filtrer par code HTTP (ex : ne garder que 200)
```
ffuf -u http://target/FUZZ -w wordlist.txt -fc 404
```

➡️ Filtrer par taille de réponse (ex : ignorer les 12 bytes)
```
ffuf -u http://target/FUZZ -w wordlist.txt -fs 12
```

➡️ Fuzz multi-paramètre (ex : injection + endpoint)
```
ffuf -u http://target/FUZZ?param=FUZ2Z -w endpoints.txt:FUZZ -w payloads.txt:FUZ2Z
```

➡️ Sauvegarder les résultats en JSON

```
ffuf -u http://target/FUZZ -w wordlist.txt -o result.json -of json
```

---

## 🧩 Options utiles (tableau)

|Option|Description courte|
|---|---|
|`-u`|URL cible, `FUZZ` est le mot clé remplacé|
|`-w`|Wordlist à utiliser|
|`-e`|Extensions à ajouter (.php, .txt, etc)|
|`-X`|Méthode HTTP (`GET`, `POST`, etc.)|
|`-d`|Données POST à envoyer|
|`-H`|Ajouter un header personnalisé|
|`-fc`|Filter code HTTP à **ignorer** (ex: `-fc 403`)|
|`-mc`|Match code HTTP à **garder**|
|`-fs`|Filtrer par taille de réponse (ignore size)|
|`-fw`|Filtrer par nb de mots|
|`-fl`|Filtrer par nb de lignes|
|`-o` / `-of`|Output et format (json, html, csv…)|
|`-t`|Threads (par défaut : 40)|
|`-timeout`|Timeout en secondes|
|`-recursion`|Explore les sous-répertoires trouvés|
|`-c`|Sauvegarde et réutilise les cookies|
