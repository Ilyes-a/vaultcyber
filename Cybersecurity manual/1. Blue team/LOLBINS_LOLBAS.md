
### **Exemples de LOLBins sous Windows**
(Living Off the Land Binaries) OU Living off the Land binaries And scripts
MITRE : Utiliser des programmes **légitimes** déjà présents sur le système pour Éviter la détection

Recherche en ligne : https://lolbas-project.github.io/

| Binaire                       | Usage légitime                         | Usage détourné (LOLBins)                                                     |     |
| ----------------------------- | -------------------------------------- | ---------------------------------------------------------------------------- | --- |
| `powershell.exe`              | Automatiser des tâches                 | Télécharger et exécuter du code malveillant (e.g., `Invoke-WebRequest`)      |     |
| `cmd.exe`                     | Interface ligne de commande            | Lancer des scripts, exécuter des commandes de persistance                    |     |
| `certutil.exe`                | Gérer les certificats                  | Télécharger un fichier distant (`certutil -urlcache -split -f`)              |     |
| `regsvr32.exe`                | Enregistrer des DLLs                   | Charger une DLL malveillante distante via un script SCT                      |     |
| `mshta.exe`                   | Exécuter du code HTML/JS               | Lancer du code malveillant via un fichier `.hta`                             |     |
| `rundll32.exe`                | Exécuter des fonctions dans une DLL    | Utilisé pour lancer du code arbitraire contenu dans une DLL                  |     |
| `wmic.exe`                    | Interface WMI                          | Exécution de commandes à distance ou récupération d’infos système            |     |
| `bitsadmin.exe`               | Gérer les téléchargements BITS         | Télécharger et exécuter du contenu distant                                   |     |
| `msiexec.exe`                 | Installer des paquets `.msi`           | Télécharger et exécuter des MSI malveillants depuis une URL distante         |     |
| `installutil.exe`             | Installer des assemblies .NET          | Charger et exécuter une DLL malveillante                                     |     |
| `scriptrunner.exe`            | Exécution de scripts via SCCM          | Exécution de code arbitraire via script                                      |     |
| `at.exe` / `schtasks.exe`     | Planification de tâches                | Persistance ou exécution différée d’une commande                             |     |
| `forfiles.exe`                | Exécuter une commande sur des fichiers | Exécution de payloads ou persistance via tâche planifiée                     |     |
| `hh.exe`                      | Afficher un fichier d’aide (.chm)      | Peut exécuter du HTML contenant du JavaScript ou des objets ActiveX          |     |
| `msbuild.exe`                 | Compiler et exécuter du code C#        | Exécution de payloads dans des fichiers `.proj` (inline C# malveillant)      |     |
| `wscript.exe` / `cscript.exe` | Exécution de scripts VBScript/JS       | Lancer des scripts malveillants en `.vbs` ou `.js`                           |     |
| `mavinject.exe`               | Injection de DLL dans un processus     | Injection de code dans des processus Windows                                 |     |
| `dxcap.exe`                   | Diagnostique DirectX                   | Utilisé pour le sideloading de DLLs                                          |     |
| `reg.exe`                     | Gérer le registre Windows              | Ajouter des clés pour persistance ou config malveillante                     |     |
| `esentutl.exe`                | Maintenance de bases de données ESE    | Utilisé pour exfiltrer ou encoder des données                                |     |
| `Makecab.exe`                 | Compresser des fichiers                | Peut être utilisé pour masquer/packer du code malveillant                    |     |
| `expand.exe`                  | Décompresser des .cab                  | Extraire du code malveillant en local à partir de .cab téléchargé            |     |
| `net.exe` / `net1.exe`        | Gestion réseau/utilisateurs            | Créer des utilisateurs, les ajouter à Administrateurs (escalade/persistance) |     |
sigma :

https://github.com/SigmaHQ/sigma


## Commandes `net` les plus courantes

|Commande|Description|
|---|---|
|`net user`|Affiche, ajoute ou modifie des comptes utilisateurs locaux|
|`net user <nom>`|Affiche les détails d’un utilisateur spécifique|
|`net localgroup`|Gère les groupes locaux|
|`net localgroup administrators`|Liste les membres du groupe admin local|
|`net group`|Gère les groupes dans un domaine Active Directory|
|`net accounts`|Affiche ou modifie la stratégie de mot de passe / verrouillage|
|`net session`|Affiche les sessions ouvertes sur la machine (SMB)|
|`net share`|Liste ou gère les dossiers partagés|
|`net view`|Affiche les partages d’un ordinateur ou les machines sur le réseau|
|`net file`|Liste les fichiers ouverts à distance sur une machine|
|`net use`|Gère les connexions aux ressources réseau (partages)|
|`net start`|Démarre un service Windows|
|`net stop`|Arrête un service Windows|
|`net statistics workstation`|Affiche des stats réseau de la machine locale|
|`net time`|Affiche ou synchronise l’heure réseau|
|`net print`|Gère les files d'attente d'impression|

---
## 🧪 Exemples utiles
```
net user backdoor P@ssw0rd! /add        :: Crée un utilisateur net localgroup administrators backdoor /add  :: Donne les droits admin net view \\192.168.1.10                     :: Liste les partages sur un hôte net use Z: \\192.168.1.10\c$ /user:admin :: Monte un partage avec auth net session                             :: Voir les connexions SMB 
```
