
# Brim queries
Brim est une interface graphique sur Kali Linux permettant d'explorer, filtrer et analyser rapidement des fichiers PCAP et logs Zeek grâce à une base de données ZNG rapide et indexée.
---

##  Requêtes de Base

### 📊 Vue d’ensemble des logs
```zeek
count() by _path
```
 Donne un aperçu global des types de logs présents dans le fichier.

### 🪟 Activité réseau Windows (SMB, RPC)
```zeek
_path=="smb_mapping" or _path=="smb_files" or _path=="dce_rpc"
```
Met en évidence les échanges Windows (partages réseau, appels RPC).

### 🌐 Connexions réseau uniques
```zeek
_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h | sort | uniq
```
 Liste toutes les connexions distinctes pour détecter des anomalies.

### 📡 Requêtes DNS & Méthodes HTTP
```zeek
_path=="dns" | count() by query | sort -r
_path=="http" | count() by method | sort -r
```
Identifie les requêtes DNS et méthodes HTTP inhabituelles.

### 📁 Activité sur les fichiers
```zeek
filename != null
```
Détecte les transferts ou fuites de fichiers suspects.

### 🧮 Statistiques par sous-réseau IP
```zeek
_path=="conn" | put classnet := network_of(id.resp_h) | count() by classnet | sort -r
```
 Repère les communications en dehors du périmètre prévu.

### 🚨 Alertes Suricata / Detections
```zeek
event_type=="alert" or _path=="notice" or _path=="signatures"
```
 Récupère les alertes de sécurité générées par Suricata ou Zeek.

---

## 🎯 Requêtes Personnalisées et Cas d’usage

### 🔍 Recherche libre
```zeek
10.0.0.1
```
 Permet de retrouver rapidement une IP, domaine ou valeur spécifique.

###  Recherche avec opérateurs logiques
```zeek
192 and NTP
```
 Combine plusieurs termes pour affiner la recherche.

### Filtrer par adresse source
```zeek
id.orig_h == 192.168.121.40
```
 Isole les connexions provenant d’un hôte donné.

### Filtrer par type de log
```zeek
_path == "conn"
```
 Ne conserve que les logs de connexion réseau.

### 📊 Compter par champ
```zeek
count() by _path
```
 Donne un résumé chiffré par type de log.

###  Trier les résultats
```zeek
count() by _path | sort -r
```
 Affiche les types de logs les plus fréquents en premier.

###  Extraire des champs spécifiques
```zeek
_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h
```
 Ne garde que les champs pertinents pour l’analyse.

###  Hôtes qui communiquent le plus
```zeek
_path=="conn" | cut id.orig_h, id.resp_h | sort | uniq -c | sort -r
```
 Met en lumière les machines les plus actives.

###  Ports les plus utilisés
```zeek
_path=="conn" | cut id.resp_p, service | sort | uniq -c | sort -r count
```
 Identifie les services réseau potentiellement exposés.

###  Connexions longues
```zeek
_path=="conn" | cut id.orig_h, id.resp_p, id.resp_h, duration | sort -r duration
```
 Détecte des sessions inhabituelles ou persistantes.

###  Volume de données transférées
```zeek
_path=="conn" | put total_bytes := orig_bytes + resp_bytes | sort -r total_bytes | cut uid, id, orig_bytes, resp_bytes, total_bytes
```
 Analyse les plus gros transferts réseau.

###  Noms de domaine/DHCP suspects
```zeek
_path=="dhcp" | cut host_name, domain
```
 Scrute les noms d’hôtes et domaines pour détecter les anomalies.

### IPs suspectes par sous-réseau
```zeek
_path=="conn" | put classnet := network_of(id.resp_h) | count() by classnet | sort -r
```
 Classe les IP par sous-réseau pour repérer les hôtes douteux.

###  Activité SMB / RPC
```zeek
_path=="dce_rpc" OR _path=="smb_mapping" OR _path=="smb_files"
```
 Utile pour détecter des tentatives de déplacement latéral.

###  Détection C2 / Malware
```zeek
event_type=="alert" | count() by alert.severity, alert.category | sort count
```
 Donne un aperçu des menaces détectées (C2, exploit…).

###  Mappage MITRE ATT&CK
```zeek
event_type=="alert" | cut alert.category, alert.metadata.mitre_technique_name, alert.metadata.mitre_technique_id, alert.metadata.mitre_tactic_name | sort | uniq -c
```
 Relie les détections aux techniques MITRE pour enrichir l’analyse.

---
