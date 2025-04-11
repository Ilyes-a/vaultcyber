#  Hashcat Cheat Sheet 

##  **Outils utiles pour Hashcat**

1. **Identifier le type de hash**  
	 OnlineHashCrack - Identifier le type de hash
    
2. **Cracker les hashes en ligne**  
     [CrackStation - Cracker les hashes MD5, SHA1, SHA256](https://crackstation.net/)  
     [HashKiller - Cracker les hashes en ligne](https://hashkiller.co.uk/)
    
3. **Liste des formats de hash supportés par Hashcat**  
    Liste pour voir tous les formats de hash compatibles avec Hashcat :  
     [Hashcat Wiki numéro type hash](https://hashcat.net/wiki/doku.php?id=example_hashes) (permet de trouver le paramètre -m de la commande)
    

---

##  Commandes Hashcat Générales

Une fois qu'on connait le type de hash et son id associé (grace à **[Hashcat Wiki numéro type hash](https://hashcat.net/wiki/doku.php?id=example_hashes)**), le paramètre `-m` pour spécifier le type de hash..

➡️ **Cracker un hash MD5**
```
hashcat -m 0 -a 0 -o cracked.txt hash.txt /path/to/wordlist.txt
```
- **`-m 0`** : Type de hash MD5.
- **`-a 0`** : Mode attaque basé sur une wordlist.
- **`-o cracked.txt`** : Sauvegarde le mot de passe trouvé dans `cracked.txt`.
    
➡️ **Cracker un hash SHA1**
```
hashcat -m 100 -a 0 -o cracked.txt hash.txt /path/to/wordlist.txt
```
- **`-m 100`** : Type de hash SHA1.
    
➡️ **Utiliser un masque pour une attaque ciblée**  
Si format pour le mot de passe connu (par exemple, 3 lettres suivies de 4 chiffres),
utiliser un masque pour accélérer le processus :
```
hashcat -m <hash_type> -a 3 -o cracked.txt hash.txt ?l?l?l?d?d?d?d
```

- **`-a 3`** : Mode masque (attaque par génération de mots de passe).
- **`?l`** : Lettre minuscule.
- **`?d`** : Chiffre.
    
➡️ **Brute force avec des règles spécifiques (Jumbo mode)**
```
hashcat -m <hash_type> -a 0 -o cracked.txt hash.txt /path/to/wordlist.txt --rules=Jumbo
```
- **`--rules=Jumbo`** : Applique les règles avancées de Hashcat pour générer des mots de passe plus complexes.
    
---

## 🧩 Options utiles pour Hashcat

|Option|Description courte|
|---|---|
|`-m <hash_type>`|Type de hash (ex: `0` pour MD5, `100` pour SHA1, etc.)|
|`-a <mode>`|Mode d'attaque (0 : wordlist, 3 : masque, etc.)|
|`-o <file>`|Sauvegarde les résultats dans un fichier spécifique|
|`-w <workload_profile>`|Définir le profil de charge de travail (`1` à `4`)|
|`--rules=<rule_file>`|Appliquer un fichier de règles pour personnaliser les attaques|
|`--status`|Afficher le statut actuel du cracking|
|`-t <threads>`|Définir le nombre de threads pour accélérer le processus|
|`--session=<session_name>`|Sauvegarder et restaurer une session de cracking|
|`--incremental`|Utiliser une attaque incrémentale (essayer toutes les combinaisons)|
|`--mask=<pattern>`|Utiliser un masque de caractères (ex: `?l?l?l?d` pour 3 lettres et 1 chiffre)|
|`--stdout`|Afficher les mots de passe générés sans les cracker|
|`--potfile-path=<file>`|Spécifier un fichier de stockage des mots de passe trouvés|
|`--force`|Forcer Hashcat à exécuter même si les conditions ne sont pas idéales|

---

## 🧩 Modes d'attaque pour l'option `-a` 

|Mode d'attaque|Description|Exemple|
|---|---|---|
|**`-a 0`**|**Brute force avec une wordlist** : Utilise une wordlist pour tester tous les mots.|`hashcat -m 0 -a 0 -o cracked.txt hash.txt /path/to/wordlist.txt`|
|**`-a 1`**|**Brute force combiné** : Combine plusieurs wordlists pour générer des combinaisons de mots de passe.|`hashcat -m 0 -a 1 -o cracked.txt hash.txt /path/to/wordlist1.txt /path/to/wordlist2.txt`|
|**`-a 3`**|**Attaque par masque (mask attack)** : Utilise un masque pour définir une structure spécifique des mots de passe (par exemple, lettres + chiffres).|`hashcat -m 0 -a 3 -o cracked.txt hash.txt ?l?l?l?d?d`|
|**`-a 6`**|**Attaque hybride (wordlist + mask)** : Combine une wordlist avec un masque pour générer des mots de passe plus complexes.|`hashcat -m 0 -a 6 -o cracked.txt hash.txt /path/to/wordlist.txt ?d?d`|
|**`-a 7`**|**Attaque hybride inversée (mask + wordlist)** : Inverse l'attaque hybride précédente (masque suivi d'une wordlist).|`hashcat -m 0 -a 7 -o cracked.txt hash.txt ?d?d /path/to/wordlist.txt`|
|**`-a 2`**|**Brute force par dictionnaire inversé (reverse dictionary attack)** : Essaie de cracker un hash en utilisant les mots de passe dans l'ordre inverse d'un fichier wordlist.|`hashcat -m 0 -a 2 -o cracked.txt hash.txt /path/to/wordlist.txt`|

---

