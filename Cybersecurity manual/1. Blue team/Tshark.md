
#  TShark 

## Sommaire
- [[#1. Généralités]]
- [[#2. Capture de trafic]]
- [[#3. Lecture et écriture de fichiers PCAP]]
- [[#4. Filtres de capture (`-f`)]]
- [[#5. Filtres d'affichage (`-Y`)]]
- [[#6. Extraction de champs (`-T fields`)]]
- [[#7. Statistiques (`-z`)]]
- [[#8. Suivi de flux et export d’objets]]
- [[#9. Recherche de credentials]]
- [[#10. Cas d’usage typiques]]

---

## 1. Généralités

```bash
tshark -h           # Aide
tshark -v           # Version
tshark -D           # Lister interfaces réseau
```

---

## 2. Capture de trafic

```bash
tshark                       # Capture avec interface par défaut
tshark -i eth0              # Capture sur interface eth0
tshark -i 1 -c 100          # Capture 100 paquets
tshark -w out.pcap          # Écrit dans un fichier
```

### Conditions de capture :

```bash
tshark -a duration:10                # Arrêt après 10s
tshark -a filesize:1024              # Arrêt à 1MB
tshark -b duration:5                 # Nouveau fichier toutes les 5s
tshark -b filesize:1024 -b files:3   # 3 fichiers max de 1MB en boucle
```

---

## 3. Lecture et écriture de fichiers PCAP

```bash
tshark -r capture.pcap                     # Lire un fichier
tshark -r capture.pcap -c 5                # Lire les 5 premiers paquets
tshark -r capture.pcap -V                  # Vue détaillée (verbose)
tshark -r capture.pcap -x                  # Vue hex + ASCII
tshark -r input.pcap -c 1 -w out.pcap      # Écriture filtrée
```

---

## 4. Filtres de capture (`-f`)

⚠️ S’appliquent **avant** la capture. Syntaxe BPF (comme tcpdump).

```bash
tshark -f "host 10.0.0.1"
tshark -f "net 192.168.1.0/24"
tshark -f "port 80"
tshark -f "src port 53"
tshark -f "dst host 1.1.1.1"
tshark -f "tcp"
```

---

## 5. Filtres d'affichage (`-Y`)

⚠️ S’appliquent **après** la capture. Même syntaxe que Wireshark.

### IP :
```bash
tshark -Y "ip.addr == 10.0.0.1"
tshark -Y "ip.src == 10.0.0.1"
tshark -Y "ip.dst == 10.0.0.1"
```

### TCP / HTTP / DNS :
```bash
tshark -Y "tcp.port == 80"
tshark -Y "http"
tshark -Y "http.response.code == 200"
tshark -Y "dns.qry.type == 1"
```

### Opérateurs avancés :
```bash
tshark -Y 'http.server contains "Apache"'
tshark -Y 'http.request.method matches "(GET|POST)"'
```

---

## 6. Extraction de champs (`-T fields`)

```bash
tshark -r file.pcap -T fields \
  -e ip.src -e ip.dst -e http.host -E header=y -c 5
```

Combine avec `awk`, `uniq`, `sort`, `nl` pour traiter les résultats.

---

## 7. Statistiques (`-z`)

```bash
tshark -r file.pcap -z io,phs -q           # Hiérarchie protocoles
tshark -r file.pcap -z endpoints,ip -q     # Endpoints IP
tshark -r file.pcap -z conv,ip -q          # Conversations IP
tshark -r file.pcap -z plen,tree -q        # Distribution taille
tshark -r file.pcap -z expert -q           # Infos expertes (erreurs)
```

### Protocoles spécifiques :
```bash
tshark -r file.pcap -z dns,tree -q
tshark -r file.pcap -z http,tree -q
tshark -r file.pcap -z ip_hosts,tree -q
tshark -r file.pcap -z ip_srcdst,tree -q
tshark -r file.pcap -z dests,tree -q
```

---

## 8. Suivi de flux et export d’objets

### Suivre un flux TCP :
```bash
tshark -r file.pcap -z follow,tcp,ascii,0 -q
```

### Exporter objets HTTP, SMB, TFTP :
```bash
tshark -r file.pcap --export-objects http,/tmp/out -q
```

---

## 9. Recherche de credentials

```bash
tshark -r credentials.pcap -z credentials -q
```

---

## 10. Cas d’usage typiques

### Extraire tous les hostnames DHCP :
```bash
tshark -r file.pcap -T fields -e dhcp.option.hostname | \
awk NF | sort | uniq -c | sort -nr
```

### Extraire les requêtes DNS :
```bash
tshark -r file.pcap -T fields -e dns.qry.name | \
awk NF | sort | uniq -c | sort -nr
```

### Lister les User-Agent HTTP :
```bash
tshark -r file.pcap -T fields -e http.user_agent | \
awk NF | sort | uniq -c | sort -nr
```