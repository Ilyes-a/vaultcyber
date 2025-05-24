
# Windows Event Log & Sysmon
## 1. Emplacement & Format des Logs

- Format : `.evtx` (propriétaire, binaire)
- Chemin : `C:\Windows\System32\winevt\Logs\`

## 2. Types de Journaux Windows

| Type                      | Description |
|---------------------------|-------------|
| System Logs               | Événements du noyau Windows, pilotes, pannes |
| Security Logs             | Connexions, déconnexions, droits, audits |
| Application Logs          | Logs d’applications installées |
| Directory Service         | Événements Active Directory |
| File Replication Service  | Réplication GPO et scripts |
| DNS Events                | Activité des serveurs DNS |
| Custom Logs               | Journaux définis par l’application elle-même |

## 3. Types d'événements

| Type        | Description |
|-------------|-------------|
| Information | État ou activité normale |
| Warning     | Fonctionnement hors norme |
| Error       | Échec d’une action |
| Critical    | Problème majeur du système |
| Audit       | Succès/échec d’audit de sécurité |

## 4. Accès aux logs

| Méthode        | Description |
|----------------|-------------|
| Event Viewer   | GUI MMC (`eventvwr.msc`) |
| wevtutil.exe   | CLI pour export, query, clear logs |
| Get-WinEvent   | Cmdlet PowerShell avancée |


## 6. wevtutil.exe (CLI)

### Commandes utiles :

| Commande            | Description |
|---------------------|-------------|
| `wevtutil el`       | Lister tous les logs |
| `wevtutil gl <log>` | Afficher les propriétés du log |
| `wevtutil qe <log>` | Lire les événements |
| `wevtutil epl`      | Exporter un log |
| `wevtutil cl`       | Effacer un log |
| `wevtutil im` / `um`| Installer / supprimer un manifest |

### Options réseau :
```bash
/r:<remote> /u:<domain\user> /p:<password> /a:Negotiate|Kerberos|NTLM
```

## 7. Get-WinEvent (PowerShell)

### Log listing
```bash
Get-WinEvent -ListLog *
Get-WinEvent -ListProvider *
```

### Filtres avancés

Par EventID :
```bash
Get-WinEvent -LogName Security -FilterXPath '*/System/EventID=4624'
```

Par Provider :
```bash
Get-WinEvent -LogName Application -FilterXPath '*/System/Provider[@Name="WLMS"]'
```

Par Donnée XML :
```bash
Get-WinEvent -LogName Security -FilterXPath '*/EventData/Data[@Name="TargetUserName"]="SYSTEM"'
```

Par XPath combiné :
```bash
Get-WinEvent -LogName Application -FilterXPath '*/System/EventID=4720 and */EventData/Data[@Name="TargetUserName"]="Sam"'
```

Par Hashtable :
```bash
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4624; StartTime='01/01/2024'}
```

## 8. Événements importants à surveiller

| Event ID | Source   | Description |
|----------|----------|-------------|
| 4624     | Security | Logon réussi |
| 4625     | Security | Logon échoué |
| 4648     | Security | Logon avec des identifiants explicites |
| 4688     | Security | Création de processus |
| 1102     | Security | Journal des événements effacé |
| 7045     | System   | Nouveau service installé |

## 9. Sysmon (System Monitor)

### Installation :
```bash
Sysmon.exe -accepteula -i path\to\config.xml
```

- Logs : `Applications and Services Logs > Microsoft > Windows > Sysmon > Operational`
- Config recommandée : https://github.com/SwiftOnSecurity/sysmon-config

### Principaux EventIDs Sysmon :

| ID    | Événement            | Utilité                            |
| ----- | -------------------- | ---------------------------------- |
| 1     | ProcessCreate        | Détection de nouveaux processus    |
| 3     | NetworkConnect       | Connexions réseau (ports suspects) |
| 7     | ImageLoad            | DLL injection                      |
| 8     | CreateRemoteThread   | Injection dans d'autres processus  |
| 10    | ProcessAccess        | Accès à lsass.exe (Mimikatz)       |
| 11    | FileCreate           | Fichiers suspects déposés          |
| 12-14 | RegistryEvent        | Persistence via registre           |
| 15    | FileCreateStreamHash | Alternate Data Streams (ADS)       |
| 22    | DnsQuery             | Requêtes DNS suspectes             |

## 10. Bonnes pratiques Sysmon

- Exclure > Inclure : Réduit le bruit sans perdre de signaux importants.
- Adapter au contexte : connaître l’environnement pour adapter les règles.
- Mise à jour régulière des règles : intégrer les dernières techniques ATT&CK.
- Utiliser SwiftOnSecurity / ION-Storm comme base puis affiner.

## 11. Techniques de chasse recommandées

| Technique               | Méthode                                         |
|------------------------|--------------------------------------------------|
| Port C2 (4444, 8080)   | EventID 3 + DestinationPort                      |
| Persistence            | EventID 11 (Startup) / 13 (Registry)            |
| Mimikatz / LSASS       | EventID 10 + filtre sur TargetImage             |
| Process Hollowing      | EventID 8 + SourceImage                         |
| ADS                    | EventID 15 + .hta, .bat, Temp                   |
| HTA abuse              | mshta.exe comme parent + .hta dans IE cache     |
| Remote Access (RAT)    | Port 1034 / 1604 / 8080 + exclusion OneDrive     |
