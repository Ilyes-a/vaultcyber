
### **1. Elasticsearch**
→ **<span style="color:rgb(240, 240, 117)">Base de données</span> NoSQL qui **stocke et analyse les logs** au format JSON.  
🧠 Sert à rechercher, corréler, filtrer les événements via API REST.

---
### **2. Logstash**

→ **<span style="color:rgb(240, 240, 117)">Pipeline de traitement</span> qui **ingère**, **filtre**, puis **envoie** les données (ex : vers Elasticsearch).  
⚙️ Fonctionne avec des blocs : `input`, `filter`, `output`.

---
### **3. Beats**
→ **<span style="color:rgb(240, 240, 117)">Agents légers</span> installés sur les machines pour **collecter et envoyer les données** (ex : logs Windows, paquets réseau) vers Logstash ou Elasticsearch.  
📦 Chaque Beat a un rôle précis (Winlogbeat, Packetbeat...).

---
### **4. Kibana**
→ I<span style="color:rgb(240, 240, 117)">nterface web</span> de visualisation** connectée à Elasticsearch.  
📊 Sert à créer des dashboards, faire des recherches et des graphiques en temps réel.

# Kibana Search Query Cheat Sheet

## 1. Free Text Search
Cherche une valeur dans tous les champs analysés.
```
login
failed login
mimikatz
```
## 2. Recherche par champ spécifique
```
event.action:"logon"
user.name:"administrator"
host.name:"PC-001"
source.ip:"192.168.1.10"
file.path.keyword:"C:\Windows\System32\cmd.exe"
```
## 3. Requêtes combinées (AND / OR / NOT)
```
event.action:"logon" AND user.name:"admin"
event.action:"logon" OR event.action:"logoff"
user.name:"admin" AND NOT source.ip:"127.0.0.1"
```
## 4. Opérateurs de comparaison
```
bytes > 10000
status_code >= 400 AND status_code < 500
@timestamp >= "2024-05-01T00:00:00" AND @timestamp < "2024-05-02T00:00:00"
```
## 5. Recherche par wildcard
```
user.name:adm*
file.name:*.exe
file.path:*\Temp\*
```
## 6. Plages de valeurs (Range queries)
```
bytes:[1000 TO 100000]
@timestamp:[now-1d TO now]
source.port:[0 TO 1024]
```
## 7. Champs existants / manquants
```
_exists_:user.name
NOT _exists_:destination.ip
```
## 8. Recherches complexes (exemples)
Connexion RDP par Administrateur avec adresse IP publique :
```
event.action:"logon" AND user.name:"Administrator" AND source.ip:!10.* AND source.ip:!192.168.* AND source.ip:!172.16.*
```
Exécution de fichier suspect :
```
process.name:"powershell.exe" AND process.command_line:*Invoke-WebRequest* AND file.path:*\Temp\*
```

Recherche d’événements sur un jour spécifique :
```
@timestamp:[2024-05-20T00:00:00 TO 2024-05-20T23:59:59] AND user.name:"admin"
```
