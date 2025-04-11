#  Gobuster Cheat Sheet 

##  Commandes les plus utiles
➡️ Fuzz de répertoires (mode dir)
```
gobuster dir -u http://target.com/ -w wordlist.txt
```
`
➡️ Fuzz avec extensions (ex: `.php`, `.txt`)
```
gobuster dir -u http://target.com/ -w wordlist.txt -x php,txt
```

➡️ Fuzz en HTTPS
```
gobuster dir -u https://target.com/ -w wordlist.txt
```

➡️ Fuzz en ignorant les erreurs 404 (code de retour)
```
gobuster dir -u http://target.com/ -w wordlist.txt -b 404
```

➡️ Fuzz en mode vhost (sous-domaines virtuels)
```
gobuster vhost -u http://target.com -w subdomains.txt
```

➡️ Fuzz de sous-domaines (DNS)

```
gobuster dns -d target.com -w subdomains.txt
```

➡️ Fuzz avec header personnalisé (ex: Auth)
```
gobuster dir -u http://target.com/ -w wordlist.txt -H "Authorization: Bearer TOKEN"
```

➡️ Export des résultats dans un fichier
```
gobuster dir -u http://target.com/ -w wordlist.txt -o result.txt
```

➡️ Threads boostés pour aller plus vite
```
`gobuster dir -u http://target.com/ -w wordlist.txt -t 50`
```

---

## 🧩 Options utiles (tableau)

|Option|Description courte|
|---|---|
|`dir`|Mode directory brute-force|
|`dns`|Mode DNS (subdomain brute-force)|
|`vhost`|Mode Virtual Host brute-force|
|`-u`|URL cible|
|`-w`|Wordlist|
|`-x`|Extensions (ex: php, txt, html)|
|`-b`|Status codes à ignorer (ex: 404)|
|`-s`|Status codes à filtrer (match only)|
|`-t`|Nombre de threads (default: 10)|
|`-r`|Ne pas suivre les redirections|
|`-o`|Fichier de sortie|
|`-H`|Header personnalisé|
|`-c`|Cookie personnalisé|
|`-k`|Ignore les certifs SSL invalides (HTTPS)|
|`--delay`|Délai entre les requêtes (anti-WAF)|
|`--timeout`|Timeout d'une requête|