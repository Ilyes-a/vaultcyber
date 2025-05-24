# Sysmon 

Sysmon (System Monitor) est un service Windows de la suite Sysinternals permettant de loguer en détail les événements système dans les journaux Windows Event Log, utile pour la détection de comportements malveillants (APT, malware, persistence...).

---

## ⚙️ Installation
1. Télécharger depuis : https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon
2. Installer avec une config XML :
```bash
Sysmon64.exe -accepteula -i sysmonconfig.xml
```
3. Mise à jour de la config :
```bash
Sysmon64.exe -c sysmonconfig.xml
```
4. Désinstallation :
```bash
Sysmon64.exe -u
```

---

## 🧩 Événements Sysmon Utiles

| Event ID | Description | Détails |
|----------|-------------|---------|
| 1        | Process Create | Nom, chemin, hash, ligne de commande |
| 2        | File creation time changed | (timestamp tampering) |
| 3        | Network connection | IP source/dest, port, process |
| 4        | Sysmon service state change | Start/stop du service |
| 5        | Process terminated | PID, exit code |
| 6        | Driver loaded | Nom, signature |
| 7        | Image loaded | DLL chargées |
| 8        | CreateRemoteThread | Injection de code |
| 10       | ProcessAccess | Tentative d'accès à un autre process |
| 11       | FileCreate | Nouveau fichier créé |
| 12-13    | Registry Key Create/Delete | Surveiller la persistence |
| 22       | DNS query | Requêtes DNS |
| 23       | FileDelete | Suppression de fichiers |
| 25       | Process Tampering | Tentatives de falsification de process |

---

## 🛡️ Exemples de Détection

- **Mimikatz** → Event ID 10 : accès mémoire à LSASS
- **Tunneling DNS** → Event ID 22 : DNS anormal
- **Persistence** → Event ID 12/13 : clés de registre suspectes (Run, Services)
- **C2 Connection** → Event ID 3 + ports inhabituels
---
##  Bonnes pratiques
- Utiliser la config recommandée : https://github.com/SwiftOnSecurity/sysmon-config

---
## 🔗 Ressources
- [Sysinternals Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [Config de SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config)
- [Sigma Rules](https://github.com/SigmaHQ/sigma)
