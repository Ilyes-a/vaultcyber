# 🧠 Volatility 3 Cheat Sheet

## 📌 Objectif
Volatility 3 est un framework d’analyse de la mémoire RAM, permettant d’extraire des artéfacts numériques **volatils** pour l’investigation forensique. Il fonctionne **sans dépendance au système analysé**, et offre une vue complète de l’état système au moment de la capture.

---

## 🔧 Installation (Linux recommandé)
```bash
git clone https://github.com/volatilityfoundation/volatility3.git
cd volatility3
python3 vol.py -h  # tester l'installation
```

### Dépendances :
- Python ≥ 3.5.3
- `pefile` : `pip install pefile`
- (Optionnels mais recommandés) :
  - `yara-python` : détection de malwares par signature
  - `capstone` : désassemblage

---

## 📂 Types de fichiers supportés
| Origine            | Extension         |
|--------------------|-------------------|
| Dump .raw (Redline, FTK, DumpIt…) | `.raw` |
| VMware             | `.vmem`           |
| Hyper-V            | `.bin`            |
| Parallels          | `.mem`            |
| VirtualBox (incomplet) | `.sav`        |

---

## 🖥️ Analyse de base

### Obtenir des infos sur l'image
```bash
python3 vol.py -f memory.raw windows.info
```

### Lister les plugins disponibles
```bash
python3 vol.py -h
```

---

## 🧩 Plugins principaux (Volatility3)

### 📋 Liste de processus
```bash
python3 vol.py -f memory.raw windows.pslist
python3 vol.py -f memory.raw windows.pstree
python3 vol.py -f memory.raw windows.psscan  # détecte rootkits
```

### 🌐 Connexions réseau
```bash
python3 vol.py -f memory.raw windows.netstat
```

### 📦 DLLs chargées par processus
```bash
python3 vol.py -f memory.raw windows.dlllist
```

---

## 🕵️ Détection de malwares

### 🔬 Code injecté (shellcode, fileless)
```bash
python3 vol.py -f memory.raw windows.malfind
```

### 📜 Recherche par règle YARA
```bash
python3 vol.py -f memory.raw windows.yarascan --yara-rules=myrules.yar
```

---

## 🛠️ Analyse avancée

### 🪝 SSDT Hooking (System Service Descriptor Table)
```bash
python3 vol.py -f memory.raw windows.ssdt
```

### 🧬 Modules noyau
```bash
python3 vol.py -f memory.raw windows.modules
python3 vol.py -f memory.raw windows.driverscan
```

---

## 🔎 Plugins complémentaires utiles

| Plugin          | Description                                  |
|----------------|----------------------------------------------|
| `modscan`       | Recherche de modules noyau par signature     |
| `moddump`       | Dump des modules                             |
| `handles`       | Objets ouverts par processus (mutex, files…) |
| `callbacks`     | Fonctions callback enregistrées              |
| `driverirp`     | IRP Hooks dans les drivers                   |
| `idt`           | Table des interruptions                      |
| `apihooks`      | API Hooking                                  |

---

## 🧪 Exemple complet
```bash
python3 vol.py -f dump.raw windows.pslist
python3 vol.py -f dump.raw windows.malfind
python3 vol.py -f dump.raw windows.yarascan --yara-rules=./rules.yar
```

---

## 📚 Ressources
- Repo officiel : https://github.com/volatilityfoundation/volatility3
- Symboles Linux/macOS : https://github.com/volatilityfoundation/volatility3#symbol-tables
---

## 📥 Extraction ciblée de mémoire d’un processus

### Dump mémoire d’un PID spécifique
```bash
python3 vol.py -f memory.raw -o ./dumped windows.memmap.Memmap --pid <PID_suspect> --dump
```

> Cela extrait l’espace mémoire associé au processus spécifié.

### Analyse des dumps extraits avec strings
```bash
strings ./dumped/*.dmp | grep -i "user-agent"
```

> Permet de repérer des artefacts réseau, commandes HTTP, ou indicateurs liés à du malware ou de l’exfiltration.