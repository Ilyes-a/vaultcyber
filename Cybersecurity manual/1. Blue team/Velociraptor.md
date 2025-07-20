# 🛡️ Velociraptor DFIR Cheat Sheet

## 1. Présentation
Velociraptor est un framework DFIR open-source, multi-plateforme, basé sur une architecture client-serveur. Il utilise un langage de requête appelé **VQL** (Velociraptor Query Language) pour collecter et analyser des artefacts numériques à distance.

---

## 2. Installation & Déploiement

### Serveur (Linux)
```bash
chmod +x velociraptor-linux-amd64
./velociraptor config generate > server.config.yaml
./velociraptor --config server.config.yaml frontend
```

### Client
```bash
./velociraptor config client --config server.config.yaml > client.config.yaml
./velociraptor --config client.config.yaml client -v
```

### Mode local tout-en-un (lab)
```bash
velociraptor gui
```

---

## 3. Interface Web
- Adresse par défaut : `https://localhost:8889`
- Login initial : `thmadmin / tryhackme`
- Composants :
  - **Shell** : exécution de commandes (PowerShell, Bash, VQL)
  - **Collected** : résultats collectés
  - **VFS** : accès au système de fichiers distant
  - **Interrogate** : info système (hostname, OS, user...)

---

## 4. VQL - Velociraptor Query Language

### Syntaxe de base
```sql
SELECT <colonnes>
FROM <plugin>(...)
WHERE <filtre>
```

### Notebooks
- Format Jupyter-like (Markdown ou VQL)
- Taper `?` dans l'éditeur pour suggestions

### Exemple
```sql
SELECT * FROM info()
```

---

## 5. VFS - Virtual File System
### Types d'accessors :
- `file` : API standard OS
- `ntfs` : lecture raw NTFS (ADS, fichiers cachés)
- `registry` : accès registre Windows
- `artifacts` : fichiers collectés

### Actions disponibles
- Rafraîchissement récursif
- Téléchargement de fichiers/dossiers

---

## 6. Plugins Forensiques VQL

### NTFS
```sql
SELECT *
FROM parse_mft(filename="C:/$MFT", accessor="ntfs")
WHERE NOT IsDir
```

### PE parsing
```sql
SELECT FileName, parse_pe(file="C:/" + FullPath) AS PE
FROM parse_mft(...)
WHERE FullPath =~ "Windows/System32/spool/drivers"
```

### Users
```sql
SELECT Name, Description, Mtime AS LastLogin
FROM Artifact.Windows.Sys.Users()
```

### Powershell
```sql
execve(program="powershell", args=["-EncodedCommand", "<...>"])
```

---

## 7. Chasse & Artefacts

### Exemple : PrintNightmare
```sql
SELECT "C:/" + FullPath AS Full_Path, FileName AS File_Name,
       parse_pe(file="C:/" + FullPath) AS PE
FROM parse_mft(filename="C:/$MFT", accessor="ntfs")
WHERE NOT IsDir AND FullPath =~ "Windows/System32/spool/drivers" AND PE
```

- Artefact : `Windows.Detection.PrintNightmare`
- DLL : `nightmare.dll`
- PDB : `C:\Users\caleb\source\repos\nightmare\x64\Release\nightmare.pdb`

---

## 8. Déploiement Avancé

- Compatible SCCM, GPO, Docker, MSI
- Système de labels clients
- TLS avec nonce (auth mutuelle)
- Stockage local ou cloud (EFS, NFS)
- Build de collectors personnalisés

---

## 9. Commandes Utiles

### Lister les plugins disponibles
```bash
velociraptor artifacts list
```

### Exécuter un VQL en CLI
```bash
velociraptor query "SELECT * FROM info()"
```

### Upload fichier spécifique
```sql
SELECT upload(path="C:/path/to/file", accessor="ntfs") FROM scope()
```

---

## 10. Ressources

- Docs : https://docs.velociraptor.app
- GitHub : https://github.com/Velocidex/velociraptor
- Référence VQL : https://docs.velociraptor.app/docs/vql_reference/
- Exemples : https://docs.velociraptor.app/exchange/