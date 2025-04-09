# 🔍 Nmap Cheat Sheet 

##  Commandes les plus utiles
➡️ Scan de base (ports TCP standards)
```
nmap <IP>
```

➡️ Scan furtif (SYN), **tous les ports**, rapide (⚡)
```
nmap -sS -p- -T4 <IP>
```

➡️ Scan UDP des 100 ports les + communs
```
nmap -sU --top-ports 100 -T4 <IP>
```

➡️ **Scan complet** : détection OS, version, traceroute, scripts
```
nmap -sS -sV -A -T4 <IP>
```

➡️ **Pas de ping, pas de DNS**, rapide et direct
```
nmap -Pn -n -sS -T4 <IP>
```

➡️ Scan en masse depuis un fichier, export en `.txt`
```
nmap -iL ips.txt -oN result.txt
```

➡️ Scan de vulnérabilités avec les scripts NSE
```
nmap --script vuln <IP>
```

➡️ Enumération HTTP ciblée sur ports web
```
nmap -p 80,443,8080 --script http-enum <IP>
```

➡️ Info sur OS via SMB (Windows)
```
nmap --script smb-os-discovery.nse -p 445 <IP>
```

➡️ Analyse des **chiffrements SSL/TLS**
```
nmap -sV --script ssl-enum-ciphers -p 443 <IP>
```


---

## 🧩 Tableau des options utiles

|Option|Description courte|
|---|---|
|`-sS`|SYN scan (furtif)|
|`-sT`|TCP connect (classique)|
|`-sU`|Scan UDP|
|`-p-`|Tous les ports (1-65535)|
|`-p 22,80`|Ports spécifiques|
|`-F`|Scan rapide (100 ports les + communs)|
|`-A`|OS + version + traceroute + scripts|
|`-O`|Détection OS uniquement|
|`-sV`|Version des services|
|`-T0` à `-T5`|Timing (T4 = rapide / T0 = furtif)|
|`-Pn`|Ignore le ping (host up forcé)|
|`-n`|Pas de résolution DNS|
|`-iL file.txt`|Input depuis un fichier|
|`-oN file.txt`|Output normal dans un fichier|
|`--open`|Affiche seulement les ports ouverts|
|`--script <name>`|Utilise des scripts NSE (vuln, http*, smb*, etc)|
|`--top-ports <nb>`|Scan les X ports les plus communs|

