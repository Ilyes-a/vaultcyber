
# Windows Event Log & [#Sysmon](#Sysmon)

# Windows Event logs:
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

| Type        | Description                      |
| ----------- | -------------------------------- |
| Information | État ou activité normale         |
| Warning     | Fonctionnement hors norme        |
| Error       | Échec d’une action               |
| Critical    | Problème majeur du système       |
| Audit       | Succès/échec d’audit de sécurité |
## 4. Accès aux logs

| Méthode      | Description                        |
| ------------ | ---------------------------------- |
| Event Viewer | GUI MMC (`eventvwr.msc`)           |
| wevtutil.exe | CLI pour export, query, clear logs |
| Get-WinEvent | Cmdlet PowerShell avancée          |
## 6. wevtutil.exe (CLI)
### Commandes utiles :

| Commande             | Description                       |
| -------------------- | --------------------------------- |
| `wevtutil el`        | Lister tous les logs              |
| `wevtutil gl <log>`  | Afficher les propriétés du log    |
| `wevtutil qe <log>`  | Lire les événements               |
| `wevtutil epl`       | Exporter un log                   |
| `wevtutil cl`        | Effacer un log                    |
| `wevtutil im` / `um` | Installer / supprimer un manifest |
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
## 9. Techniques de chasse recommandées

| Technique            | Méthode                                      |
| -------------------- | -------------------------------------------- |
| Port C2 (4444, 8080) | EventID 3 + DestinationPort                  |
| Persistence          | EventID 11 (Startup) / 13 (Registry)         |
| Mimikatz / LSASS     | EventID 10 + filtre sur TargetImage          |
| Process Hollowing    | EventID 8 + SourceImage                      |
| ADS                  | EventID 15 + .hta, .bat, Temp                |
| HTA abuse            | mshta.exe comme parent + .hta dans IE cache  |
| Remote Access (RAT)  | Port 1034 / 1604 / 8080 + exclusion OneDrive |
## 8. Événements importants à surveiller
|                              |                                                                                                 |
| ---------------------------- | ----------------------------------------------------------------------------------------------- |
| **Event ID**                 | **What it means**                                                                               |
| 4624                         | Successful account log on                                                                       |
| 4625                         | Failed account log on                                                                           |
| 4634                         | An account logged off                                                                           |
| 4648                         | A logon attempt was made with explicit credentials                                              |
| 4719                         | System audit policy was changed.                                                                |
| 4964                         | A special group has been assigned to a new log on                                               |
| 1102                         | Audit log was cleared. This can relate to a potential attack                                    |
| 4720                         | A user account was created                                                                      |
| 4722                         | A user account was enabled                                                                      |
| 4723                         | An attempt was made to change the password of an account                                        |
| 4725                         | A user account was disabled                                                                     |
| 4728                         | A user was added to a privileged global group                                                   |
| 4732                         | A user was added to a privileged local group                                                    |
| 4756                         | A user was added to a privileged universal group                                                |
| 4738                         | A user account was changed                                                                      |
| 4740                         | A user account was locked out                                                                   |
| 4767                         | A user account was unlocked                                                                     |
| 4735                         | A privileged local group was modified                                                           |
| 4737                         | A privileged global group was modified                                                          |
| 4755                         | A privileged universal group was modified                                                       |
| 4772                         | A Kerberos authentication ticket request failed                                                 |
| 4777                         | The domain controller failed to validate the credentials of an account.                         |
| 4782                         | Password hash an account was accessed                                                           |
| 4616                         | System time was changed                                                                         |
| 4657                         | A registry value was changed                                                                    |
| 4697                         | An attempt was made to install a service                                                        |
| 4698, 4699, 4700, 4701, 4702 | Events related to Windows scheduled tasks being created, modified, deleted, enabled or disabled |
| 4946                         | A rule was added to the Windows Firewall exception list                                         |
| 4947                         | A rule was modified in the Windows Firewall exception list                                      |
| 4950                         | A setting was changed in Windows Firewall                                                       |
| 4954                         | Group Policy settings for Windows Firewall has changed                                          |
| 5025                         | The Windows Firewall service has been stopped                                                   |
| 5031                         | Windows Firewall blocked an application from accepting incoming traffic                         |
| 5152, 5153                   | A network packet was blocked by Windows Filtering Platform                                      |
| 5155                         | Windows Filtering Platform blocked an application or service from listening on a port           |
| 5157                         | Windows Filtering Platform blocked a connection                                                 |
| 5447                         | A Windows Filtering Platform filter was changed                                                 |

# Sysmon 

Sysmon (System Monitor) est un service Windows de la suite Sysinternals permettant de loguer en détail les événements système dans les journaux Windows Event Log, utile pour la détection de comportements malveillants (APT, malware, persistence...).
## 🧩 Événements Sysmon Utiles

| Event ID | Description                                                                    |
| -------- | ------------------------------------------------------------------------------ |
| 1        | Création de processus                                                          |
| 2        | Modification de l'horodatage de création d'un fichier                          |
| 3        | Connexion réseau (TCP/UDP)                                                     |
| 4        | Changement d'état du service Sysmon                                            |
| 5        | Terminaison de processus                                                       |
| 6        | Chargement de pilote (driver)                                                  |
| 7        | Chargement d'image (DLL)                                                       |
| 8        | Création de thread distant (CreateRemoteThread)                                |
| 9        | Lecture brute sur disque (RawAccessRead)                                       |
| 10       | Accès à un autre processus (ProcessAccess)                                     |
| 11       | Création de fichier                                                            |
| 12       | Création ou suppression de clé de registre                                     |
| 13       | Modification de valeur de registre                                             |
| 14       | Renommage de clé ou de valeur de registre                                      |
| 15       | Création de flux de fichier avec hachage (FileCreateStreamHash)                |
| 16       | Modification de la configuration de Sysmon                                     |
| 17       | Création de canal nommé (Named Pipe)                                           |
| 18       | Connexion à un canal nommé                                                     |
| 19       | Activité WMI : création de filtre d'événement (WmiEventFilter)                 |
| 20       | Activité WMI : création de consommateur d'événement (WmiEventConsumer)         |
| 21       | Activité WMI : liaison entre filtre et consommateur (WmiEventConsumerToFilter) |
| 22       | Requête DNS effectuée par un processus                                         |
| 23       | Suppression de fichier avec archivage (FileDelete)                             |
| 24       | Changement du contenu du presse-papiers (ClipboardChange)                      |
| 25       | Altération de processus (Process Tampering)                                    |
| 26       | Suppression de fichier détectée (sans archivage)                               |
| 27       | Blocage de création de fichier exécutable                                      |
| 28       | Blocage de tentative de destruction sécurisée de fichier (shredding)           |
| 4720     | New user created                                                               |
| 4103     | Powershell logging enabled                                                     |
|          |                                                                                |

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

|29|Détection de création de fichier exécutable|
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
