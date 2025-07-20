# Linux Command Line Utilities Cheat Sheet

Une collection de commandes shell combinées avec `|`, souvent utilisées pour l'analyse, le filtrage et la transformation de données en ligne de commande.

---

## 🔍 Rechercher & Filtrer

```bash
cat /etc/passwd | grep bash           # Affiche uniquement les lignes contenant 'bash'
ps aux | grep apache                  # Filtre les processus liés à Apache
cat fichier.log | grep -i error       # Recherche 'error' sans tenir compte de la casse
```

---

## 📊 Compter les résultats

```bash
ls -l | wc -l                         # Compte le nombre de fichiers (y compris l'en-tête)
cat auth.log | grep 'Failed' | wc -l # Compte le nombre d'échecs de connexion
```

---

## 🧹 Supprimer les doublons

```bash
cat noms.txt | sort | uniq           # Trie puis élimine les doublons
cat noms.txt | sort | uniq -c        # Affiche les doublons avec leur nombre d’occurrences
```

---

## 📈 Statistiques rapides

```bash
du -sh * | sort -h                   # Affiche la taille des dossiers et trie
ps aux | awk '{print $1}' | sort | uniq -c | sort -nr | head
# Classe les utilisateurs par nombre de processus
```

---

## 🔠 Extraction de champs (colonnes)

```bash
cat /etc/passwd | cut -d: -f1        # Affiche seulement les noms d’utilisateurs
ps aux | awk '{print $1, $11}'       # Affiche l’utilisateur et le nom du processus
```

---

## 🎯 Filtrage avancé avec `awk`

```bash
df -h | awk '$5+0 > 50 {print $0}'   # Affiche les partitions avec +50% d'utilisation
netstat -antp | awk '$6=="LISTEN"'  # Affiche les connexions à l’écoute
```

---

##  Chaînage d’analyse (cas réels)

```bash
cat /var/log/auth.log | grep 'sudo' | awk '{print $1, $2, $3, $9}' | sort | uniq -c | sort -nr
# Compte les utilisations de sudo par utilisateur dans les logs
```

```bash
find / -type f 2>/dev/null | grep -i '.log$' | xargs grep -i 'error' | less
# Recherche toutes les erreurs dans tous les fichiers .log du système
```

```bash
ps aux | grep -v grep | grep sshd | awk '{print $2}' | xargs kill -9
# Kill tous les processus SSH en cours sauf grep lui-même
```

---

##  Boucles utiles (bonus)

```bash
for i in *.log; do echo "$i"; grep -i "error" "$i" | wc -l; done
# Affiche le nombre d’erreurs par fichier log dans un dossier
```

---
## 🔎 strings

extrait toutes les chaînes ASCII imprimables d’un fichier binaire, utile pour révéler des artefacts cachés (URLs, user-agent, commandes, chemins…).  
```
strings sample.dmp | grep -i "user-agent"

strings <fichier>
strings <fichier> | more
strings <fichier> > sortie.txt
```

**À noter** :  
`!This program cannot be run in DOS mode.` → indique que le fichier est un exécutable PE (stub DOS par défaut).


> Permet d’identifier des fonctions API, URLs, messages, etc.

---
### Identifier le type de fichier
```bash
file <nom_fichier>
```
> Exemple : `PE32 executable (GUI) Intel 80386, for MS Windows`
---

## Hasher un échantillon
```bash
md5sum <fichier>
sha1sum <fichier>
sha256sum <fichier>
```
---

###  Analyse PE Header avec `pecheck`
```bash
pecheck <fichier>
```

- Affiche : sections, entropie, imports, hash, flags suspicieux, etc.
- Sections typiques :
  - `.text` : Code exécutable (instructions CPU)
  - `.data` : Données globales
  - `.rsrc` : Ressources embarquées (icônes, images, etc.)
  - `.rdata` : Données en lecture seule

> Entropie > 7.0 → fichier potentiellement packé.

###  pe-tree – Analyse interactive des fichiers PE
```bash
pe-tree <fichier>
```
Ouvre une interface graphique interactive (TreeView) pour explorer la structure PE : sections, imports/exports, entropie, headers, etc.




## 🛠️ Tips

- Utiliser `xargs` pour traiter chaque ligne comme argument d’une commande
- `2>/dev/null` permet d’ignorer les erreurs (utile avec `find` ou `grep`)
- `head`, `tail`, `less`, `more` pour lire de gros fichiers progressivement
