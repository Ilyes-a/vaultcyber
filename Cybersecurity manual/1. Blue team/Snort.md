#  *Snort Cheat Sheet - IDS/IPS*

## 📜 **Commandes Clés**


Commande Lancer snort avec un fichier de config et le mode full avec le log qui s'écrit dans le directory actif :

```
snort -c local.rules -A full -l . 
```

et sur un fichier pcap :

```
snort -c local.rules -A full -l . -r fichier.pcap
```

Run snort en IPS mode, sur interface eth0 et eth1 en mode console:

```
sudo snort -c /etc/snort/snort.conf -q -Q --daq afpacket -i eth0:eth1 -A console
```

# Détails

### **Version & Configuration**

|Commande|Description|
|---|---|
|`snort -V`|Affiche la version|
|`snort -c /etc/snort/snort.conf -T`|Vérifie la configuration|
|`snort -q`|Mode silencieux (pas d'accueil)|

### **Mode Verbose & Log**

|Commande|Description|
|---|---|
|`snort -v`|Mode détaillé, affiche les paquets|
|`snort -d`|Affiche les données du paquet|
|`snort -X`|Affiche en hexadécimal|
|`snort -i eth0`|Sélectionne l'interface réseau|
|`snort -dev -l /var/log/snort`|Enregistre dans `/var/log/snort`|
|`snort -r snort.log`|Lit les logs générés|

### **Alertes & Modes IDS/IPS**

|Commande|Description|
|---|---|
|`snort -c /etc/snort/snort.conf -A console`|Alertes sur le terminal|
|`snort -c /etc/snort/snort.conf -A fast`|Alertes rapides (timestamp + IPs + ports)|
|`snort -c /etc/snort/snort.conf -A full`|Alertes détaillées|
|`snort -c /etc/snort/snort.conf -A none`|Désactive les alertes|

### **Modes Avancés**

|Commande|Description|
|---|---|
|`snort -c /etc/snort/snort.conf -D`|Démarre en arrière-plan (background)|
|`snort -r logname.log -n 10`|Affiche les 10 premiers paquets du log|
|`snort -r logname.log icmp`|Filtre les paquets ICMP|

## 🧩 **Paramètres Rapides**

- **`-c`** : Fichier de configuration
- **`-T`** : Teste la configuration
- **`-N`** : Désactive la journalisation
- **`-A`** : Mode d'alerte (full, fast, console, etc.)
- **`-D`** : Mode arrière-plan
- **`-v`** : Affichage détaillé
- **`-i`** : Interface réseau


---


## 🛠️ **Investiguer les PCAP avec Snort**
### **Paramètres pour PCAP**

|Paramètre|Description|
|---|---|
|`-r` / `--pcap-single=`|Lit un seul fichier pcap|
|`--pcap-list=""`|Lit plusieurs fichiers pcap (séparés par un espace)|
|`--pcap-show`|Affiche le nom du pcap durant le traitement|

### **Exemple : Investiguer un seul PCAP**

```bash
sudo snort -c /etc/snort/snort.conf -q -r icmp-test.pcap -A console -n 10
```

Cet exemple analyse le fichier `icmp-test.pcap` et affiche les alertes des 10 premiers paquets.

### **Exemple : Investiguer plusieurs PCAPs**

```bash
sudo snort -c /etc/snort/snort.conf -q --pcap-list="icmp-test.pcap http2.pcap" -A console -n 10
```

Cet exemple analyse plusieurs fichiers pcap (`icmp-test.pcap` et `http2.pcap`) et affiche les alertes des 10 premiers paquets de chaque fichier.

### **Exemple : Montrer les noms des PCAPs durant l'analyse**

```bash
sudo snort -c /etc/snort/snort.conf -q --pcap-list="icmp-test.pcap http2.pcap" -A console --pcap-show
```

Cela affiche le nom de chaque fichier pcap analysé dans les alertes.

---

# Snort Rules 

![[Pasted image 20250408195605.png]]

- **alert** : Génère une alerte et logue le paquet
- **log** : Logue le paquet
- **drop** : Bloque et logue le paquet
- **reject** : Bloque, logue et termine la session du paquet
    

### Protocoles
- **IP**, **TCP**, **UDP**, **ICMP**
    
### *Filtrage IP & Ports

- **Filtrer une IP** : `alert icmp 192.168.1.56 any <> any any (msg: "ICMP Packet From"; sid: 100001; rev:1;)`
    
- **Filtrer un sous-réseau** : `alert icmp 192.168.1.0/24 any <> any any (msg: "ICMP Packet Found"; sid: 100001; rev:1;)`
    
- **Filtrer plusieurs sous-réseaux** : `alert icmp [192.168.1.0/24, 10.1.1.0/24] any <> any any (msg: "ICMP Packet Found"; sid: 100001; rev:1;)`
    
- **Exclure une IP** : `alert icmp !192.168.1.0/24 any <> any any (msg: "ICMP Packet Found"; sid: 100001; rev:1;)`
    
- **Filtrer un port spécifique** : `alert tcp any any <> any 21 (msg: "FTP Port 21"; sid: 100001; rev:1;)`
    
- **Exclure un port spécifique** : `alert tcp any any <> any !21 (msg: "Non-FTP Traffic"; sid: 100001; rev:1;)`
    
- **Plage de ports** :
    
    - `alert tcp any any <> any 1:1024 (msg: "Low Ports"; sid: 100001; rev:1;)`
        
    - `alert tcp any any <> any :1024 (msg: "System Ports"; sid: 100001; rev:1;)`
        
    - `alert tcp any any <> any 1025: (msg: "High Ports"; sid: 100001; rev:1;)`
        

### *Direction

- **Source vers destination** : `->`
- **Bidirectionnel** : `<>`

### **Options de Règles**
- **msg** : Message d'alerte
- **sid** : ID unique de la règle (ex : `sid: 100001`)
- **rev** : Version de la règle (ex : `rev:1`)
- **reference** : Référence (CVE, etc.)
    
### **Options de Détection de Payload**

- **content** : Contenu spécifique (ASCII ou HEX)
    - **ASCII** : `alert tcp any any <> any 80 (msg: "GET Request"; content:"GET"; sid: 100001; rev:1;)`
    - **HEX** : `alert tcp any any <> any 80 (msg: "GET Request"; content:"|47 45 54|"; sid: 100001; rev:1;)`
- **nocase** : Ignore la casse (`nocase`)
- **fast_pattern** : Optimisation de la recherche de contenu
    

### **Options de Détection Non-Payload**
- **id** : Filtre sur le champ ID du paquet
    - `alert tcp any any <> any any (msg: "ID TEST"; id:123456; sid: 100001; rev:1;)`
- **flags** : Filtre sur les flags TCP (SYN, ACK, etc.)
    - `alert tcp any any <> any any (msg: "SYN Packet"; flags:S; sid: 100001; rev:1;)`
- **dsize** : Taille du payload
    - `alert ip any any <> any any (msg: "Packet Size Test"; dsize:100<>300; sid: 100001; rev:1;)`
- **sameip** : Source et destination avec la même IP
    - `alert ip any any <> any any (msg: "Same IP Test"; sameip; sid: 100001; rev:1;)`
        

### Fichier de Règles Locales
- **Emplacement** : `/etc/snort/rules/local.rules`
- **Modification** : `sudo gedit /etc/snort/rules/local.rules`





# Points to Remember

### [[Snort Cheatsheet - TryHackMe.pdf]]
## **Main** Components of Snort

- **Packet Decoder -** Packet collector component of Snort. It collects and prepares the packets for pre-processing. 
- **Pre-processors -** A component that arranges and modifies the packets for the detection engine.
- **Detection Engine -** The primary component that process, dissect and analyse the packets by applying the rules. 
- Logging and Alerting - Log and alert generation component.
- Outputs and Plugins - Output integration modules (i.e. alerts to syslog/mysql) and additional plugin (rule management detection plugins) support is done with this component. 

### **There are three types of rules available for snort**

- Community Rules - Free ruleset under the GPLv2. Publicly accessible, no need for registration.
- Registered Rules - Free ruleset (requires registration). This ruleset contains subscriber rules with 30 days delay.
- Subscriber Rules (Paid) - Paid ruleset (requires subscription). This ruleset is the main ruleset and is updated twice a week (Tuesdays and Thursdays).

- ==snort.conf==: _Main configuration file._
- ==local.rules==: _User-generated rules file._

![[Pasted image 20240729232308.png]]

<span style="color:rgb(221, 85, 85)">Data Acquisition Modules (DAQ)</span> are specific libraries used for packet I/O, bringing flexibility to process packets. It is possible to select DAQ type and mode for different purposes.

There are ==six DAQ modules== available in Snort;

- **Pcap:** Default mode, known as Sniffer mode.
- **Afpacket:** Inline mode, known as IPS mode.
- **Ipq:** Inline mode on Linux by using Netfilter. It replaces the snort_inline patch.  
- **Nfq:** Inline mode on Linux.
- **Ipfw:** Inline on OpenBSD and FreeBSD by using divert sockets, with the pf and ipfw firewalls.  
    
- **Dump:** Testing mode of inline and normalisation.

The most popular modes are the default (pcap) and inline/IPS (Afpacket).

![[Pasted image 20240729232521.png]]
