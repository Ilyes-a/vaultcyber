#  **Zeek 

## **Commandes de base Zeek**

### 1. **Vérifier la version de Zeek**
```bash
zeek -v
```

### 2. **Lancer Zeek en tant que service**
```bash
zeekctl status   # Vérifier le statut
zeekctl start    # Démarrer Zeek
zeekctl stop     # Arrêter Zeek
```

### 3. **Traitement d'un fichier pcap**
```bash
zeek -C -r <file.pcap> <script.zeek> # Traiter un fichier pcap avec un script
```

### 4. **Vérification des logs générés après traitement pcap**
```bash
ls -l  # Voir les logs générés
```

### 5. **Vérifier l'état de Zeek**
```bash
zeekctl status
```

### 6. **Exporter les logs en format CSV (utile pour l'analyse)**
```bash
zeek-cut -d, -c ts,uid,id.orig_h,id.resp_h <log_file> > output.csv
```

---

## 🧩 **Commandes avancées et outils**

### 1. **Utiliser `zeek-cut` pour extraire des colonnes spécifiques**
```bash
cat <log_file> | zeek-cut <field_names>
```

### 2. **Rechercher des mots-clés dans les logs Zeek**
```bash
cat <log_file> | grep 'keywords'
```

### 3. **Appliquer des filtres et trier les résultats**
```bash
cat <log_file> | sort | uniq   # Trier et éliminer les doublons
cat <log_file> | sort -n      # Trier numériquement
cat <log_file> | uniq -c     # Compter les occurrences
```

### 4. **Combiner les commandes `grep` et `awk` pour une recherche avancée**
```bash
grep "suspicious" <log_file> | awk '{print $1, $5, $6}'  # Extraire des colonnes spécifiques
```

### 5. **Manipuler les logs avec `awk` et `sed` pour une analyse avancée**
```bash
awk '$5 > 100 {print $0}' <log_file>  # Afficher les lignes où la 5e colonne est supérieure à 100
sed 's/\s\+/ /g' <log_file>          # Remplacer les multiples espaces par un espace unique
```

### 6. **Utiliser `zeek-cut` pour exporter des champs dans un format CSV**
```bash
cat <log_file> | zeek-cut -d, id.orig_h,id.resp_h,duration > filtered_logs.csv
```

---

## 📂 **Catégories de Logs Zeek**

### 1. **Logs de réseau** (protocoles, connexions, etc.)
- Exemple de logs : `conn.log`, `dns.log`, `http.log`, `ftp.log`, `ssh.log`, etc.

### 2. **Logs de fichiers** (analyse des fichiers transférés)
- Exemple de logs : `files.log`, `pe.log`, `x509.log`

### 3. **Logs de contrôle réseau** (contrôle et gestion du réseau)
- Exemple de logs : `netcontrol.log`, `openflow.log`

### 4. **Logs de détection** (anomalies, signatures, etc.)
- Exemple de logs : `notice.log`, `signatures.log`, `intel.log`

### 5. **Logs d'observations réseau** (flux et services)
- Exemple de logs : `known_hosts.log`, `software.log`

### 6. **Logs divers** (alertes externes, erreurs)
- Exemple de logs : `weird.log`, `barnyard2.log`

### 7. **Logs de diagnostic** (statistiques, état de Zeek)
- Exemple de logs : `broker.log`, `stats.log`, `stderr.log`
  
---

## 🛠 **Utilisation des Frameworks Zeek**

### 1. **Frameworks principaux**
- **Logging Framework** : Pour tous les cas de logs.
- **File Analysis Framework** : Pour les fichiers transférés et leur hashage (MD5, SHA1, SHA256).
- **Signature Framework** : Pour la détection d'événements via signatures.
- **GeoLocation Framework** : Pour l'ajout de données géographiques aux IPs.

### 2. **Exemple de script pour analyser les fichiers transférés**
```zeek
@load /opt/zeek/share/zeek/policy/frameworks/files/hash-all-files.zeek
```

### 3. **Exemple de script pour détecter un brute-force FTP**
ftpbrute.sig :
```zeek
signature ftp-brute {
    ip-proto == tcp
    payload /.*530.*Login.*incorrect.*/
    event "FTP Brute-force Attempt"
}
```

### 4. **Activer le GeoLocation Framework pour ajouter des informations géographiques aux IPs**
```zeek
@load /opt/zeek/share/zeek/policy/frameworks/geoip/geoip.zeek
```

---

## 📄 **Script Zeek**

### 1. **Écrire un script simple pour afficher des connexions**
```zeek
event new_connection(c: connection) {
    print fmt("New Connection: %s -> %s", c$id$orig_h, c$id$resp_h);
}
```

### 2. **Script pour une alerte de signature spécifique**
```zeek
event signature_match(state: signature_state, msg: string, data: string) {
    if (state$sig_id == "ftp-admin") {
        print "Signature hit! --> #FTP-Admin";
    }
}
```

### 3. **Exécuter un script avec Zeek**
```bash
zeek -C -r <file.pcap> <script_name>.zeek
```

### 4. **Écrire un script pour détecter les connexions HTTP suspectes**
```zeek
event http_request(c: connection, method: string, host: string, uri: string) {
    if (uri == "/admin" || uri == "/config") {
        print fmt("Suspicious HTTP request: %s -> %s", c$id$orig_h, uri);
    }
}
```

---

## **Trucs et astuces pour analyser les logs**

### 1. **Filtrage avancé avec `awk` et `sed`**
```bash
awk 'NR == 11 {print $0}' <log_file>   # Imprimer la ligne 11
sed -n '10,15p' <log_file>             # Imprimer les lignes 10 à 15
```

### 2. **Analyser les connexions avec `grep`**
```bash
grep -rin "suspicious_pattern" <log_file> | column -t
```

### 3. **Utiliser `zeek-cut` pour filtrer les logs avec plusieurs colonnes**
```bash
cat <log_file> | zeek-cut ts,uid,id.orig_h,id.resp_h | grep "SuspiciousPattern"
```

---

## **Exemple de détection avec des signatures Zeek**

### 1. **Signature de mot de passe en clair HTTP**
fichier.sig
```zeek
signature http-password {
    ip-proto == tcp
    dst-port == 80
    payload /.*password.*/
    event "Cleartext Password Found!"
}
```

### 2. **Signature de tentative de connexion FTP admin**
```zeek
signature ftp-admin {
    ip-proto == tcp
    ftp /.*USER.*admin.*/
    event "FTP Admin Login Attempt!"
}
```

### 3. **Signature de brute-force FTP**
```zeek
signature ftp-brute {
    ip-proto == tcp
    payload /.*530.*Login.*incorrect.*/
    event "FTP Brute-force Attempt"
}
```

---

## **Utilisation des Paquets avec `zkg`**

### 1. **Installer un paquet**
```bash
zkg install <package_name>
```

### 2. **Lister les paquets installés**
```bash
zkg list
```

### 3. **Mettre à jour un paquet**
```bash
zkg upgrade <package_name>
```

---

## **Analyser les événements d'intelligence**

### 1. **Ajouter un fichier d'intelligence personnalisé**
```bash
redef Intel::read_files += { "/opt/zeek/intel/zeek_intel.txt" };
```

### 2. **Exemple d'alertes d'intelligence**
```bash
cat intel.log | zeek-cut uid id.orig_h id.resp_h seen.indicator matched
```

