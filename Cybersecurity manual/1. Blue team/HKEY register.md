
# Clés de Registre Windows HKEY

## Hives – Appellations des branches principales du registre

| Abréviation | Nom complet               | Description                                                             |
|------------|----------------------------|-------------------------------------------------------------------------|
| HKCU       | HKEY_CURRENT_USER          | Paramètres spécifiques à l’utilisateur actuellement connecté.          |
| HKLM       | HKEY_LOCAL_MACHINE         | Paramètres globaux système (logiciels, pilotes, services…).            |
| HKCR       | HKEY_CLASSES_ROOT          | Fusion de HKLM\Software\Classes et HKCU\Software\Classes.          |
| HKU        | HKEY_USERS                 | Contient les profils de tous les utilisateurs chargés.                 |
| HKCC       | HKEY_CURRENT_CONFIG        | Infos sur le profil matériel en cours (affichage, imprimantes…).       |

---

##  Localisation physique des fichiers de hives (fichiers du registre)

| Hive              | Emplacement physique                                          | Monte dans…                        |
|------------------|---------------------------------------------------------------|------------------------------------|
| DEFAULT           | `C:\Windows\System32\Config\DEFAULT`                     | `HKU\DEFAULT`                     |
| SAM               | `C:\Windows\System32\Config\SAM`                         | `HKLM\SAM`                        |
| SECURITY          | `C:\Windows\System32\Config\SECURITY`                    | `HKLM\SECURITY`                   |
| SOFTWARE          | `C:\Windows\System32\Config\SOFTWARE`                    | `HKLM\SOFTWARE`                   |
| SYSTEM            | `C:\Windows\System32\Config\SYSTEM`                      | `HKLM\SYSTEM`                     |
| NTUSER.DAT        | `C:\Users\<user>\NTUSER.DAT`                              | `HKCU` (à la connexion de l’utilisateur) |
| USRCLASS.DAT      | `C:\Users\<user>\AppData\Local\Microsoft\Windows\USRCLASS.DAT` | `HKCU\Software\Classes`  |
| Amcache.hve       | `C:\Windows\AppCompat\Programs\Amcache.hve`             | Non monté par défaut              |

---

## Journaux et sauvegardes des hives

| Type                     | Détails                                                                 |
|--------------------------|-------------------------------------------------------------------------|
| Fichiers LOG             | `SAM.LOG`, `SYSTEM.LOG1`, etc. – changelog des écritures en attente    |
| Backups                  | `C:\Windows\System32\Config\RegBack\` – copie automatique tous les 10 jours |
| Usage forensique         | Contient souvent des modifications récentes non encore enregistrées dans la hive principale |

---

## 📁 Emplacements clés à inspecter 

| Clé de registre                                       | Objectif / Usage typique                             |
|--------------------------------------------------------|------------------------------------------------------|
| HKCU\Software\Microsoft\Windows\CurrentVersion\Run       | Persistance utilisateur (logon sans privilèges)      |
| HKLM\Software\Microsoft\Windows\CurrentVersion\Run       | Persistance globale (logon tous utilisateurs)        |
| HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce   | Exécution unique au prochain logon                   |
| HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce   | Idem au niveau système                               |
| HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon | Hijack shell, Userinit                              |
| HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options | Redirection EXE, debugger |
| HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System | Bypass UAC, désactivation sécurité                |
| HKLM\SYSTEM\CurrentControlSet\Services                     | Manipulation de services Windows                     |
| HKLM\SYSTEM\CurrentControlSet\Control\Lsa                 | Bypass logon, stockage de mots de passe              |
| HKCU\Software\Microsoft\Terminal Server Client             | Historique connexions RDP                            |
| HKCU\Software\Microsoft\Windows\Shell\BagMRU             | Navigation fichiers / artefacts utilisateur          |
| HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs | Docs ouverts récemment                       |

---

### 🔹 Clés de persistance classiques (`Run`, `RunOnce`, etc.)

```powershell
Get-ItemProperty -Path 'HKLM:\Software\Microsoft\Windows\CurrentVersion\Run'
Get-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Run'
Get-ItemProperty -Path 'HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce'
Get-ItemProperty -Path 'HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce'
```

### 🔹 Clé `Winlogon` (Shell hijack, Userinit, etc.)

```powershell
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon'
```

### 🔹 IFEO (Image File Execution Options) – Hijack EXE

```powershell
Get-ChildItem -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options' |
  ForEach-Object { Get-ItemProperty -Path $_.PsPath }
```

### 🔹 Clés de tâches planifiées suspectes

```powershell
Get-ScheduledTask | Where-Object { $_.TaskPath -like '*\Microsoft\Windows\*' -or $_.TaskName -like '*update*' }
```

---

##  Recommandations

- Logguer les événements **Sysmon 12, 13, 14** (clé, valeur, suppression registre).
- Utiliser **Autoruns** pour visualiser les clefs de démarrage et tâches cachées.
- Vérifier les fichiers de hives manuellement avec des outils comme **RECmd**, **Registry Explorer**.
- Ne pas oublier d’analyser les **.LOG1/.LOG2** dans `System32\Config` pour les writes récents.
- Inspecter **NTUSER.DAT** et **USRCLASS.DAT** même hors session (montage offline).

---

---

##  Artefacts Forensiques du Registre – Synthèse (Windows Forensics Cheatsheet)

| Catégorie                       | Chemins de registre / fichiers                               | Description |
|--------------------------------|----------------------------------------------------------------|-------------|
| **Infos système & utilisateurs** | `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion`<br>`HKLM\SYSTEM\CurrentControlSet` | OS, configuration système |
| **Nom de machine**             | `HKLM\SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName` | Nom d’hôte |
| **Fuseau horaire**             | `HKLM\SYSTEM\CurrentControlSet\Control\TimeZoneInformation` | Horodatages |
| **Interfaces réseau**          | `HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces` | Cartes réseau et IP |
| **Programmes lancés au démarrage** | `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`<br>`HKLM\...\RunOnce`<br>`HKLM\...\Policies\Explorer\Run` | Persistance |
| **Utilisateurs Windows**       | `HKLM\SAM\Domains\Account\Users`                          | Comptes locaux |
| **Fichiers récents / MRU**     | `NTUSER.DAT\...\RecentDocs`, `Office\FileMRU`, `ComDlg32`  | Activité utilisateur |
| **ShellBags**                  | `USRCLASS.DAT\...\Shell\Bags`, `BagMRU`                     | Navigation fichiers |
| **Historique recherches**      | `TypedPaths`, `WordWheelQuery`                                | Barres d’adresse / recherche |
| **UserAssist**                 | `UserAssist\{GUID}\Count`                                   | Exécutables lancés |
| **ShimCache**                  | `SYSTEM\CurrentControlSet\Control\Session Manager\AppCompatCache` | Exécution de binaire |
| **Amcache**                    | `C:\Windows\AppCompat\Programs\Amcache.hve`               | Fichiers exécutés récemment |
| **BAM / DAM**                  | `SYSTEM\CurrentControlSet\Services\bam\UserSettings`      | Programmes exécutés en background |
| **USB**                        | `Enum\USBSTOR`, `Enum\USB`, `Windows Portable Devices`      | Dispositifs externes |
| **Temps de connexion/déconnexion USB** | `Properties\{GUID}`                                        | Timestamps périphériques |

---