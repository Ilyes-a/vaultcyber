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

## 🧱 Chaînage d’analyse (cas réels)

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

## 🔁 Boucles utiles (bonus)

```bash
for i in *.log; do echo "$i"; grep -i "error" "$i" | wc -l; done
# Affiche le nombre d’erreurs par fichier log dans un dossier
```

---

## 🛠️ Tips

- Utiliser `xargs` pour traiter chaque ligne comme argument d’une commande
- `2>/dev/null` permet d’ignorer les erreurs (utile avec `find` ou `grep`)
- `head`, `tail`, `less`, `more` pour lire de gros fichiers progressivement
