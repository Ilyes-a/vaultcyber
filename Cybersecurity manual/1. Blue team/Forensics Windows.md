
# Windows Forensics

## Les outils : 
- - [Eric Zimmerman's Tools](https://github.com/EricZimmerman/) : Suite d'outils forensiques pour analyser le registre, le système de fichiers, les logs, les timelines, etc.

  - <span style="color:rgb(255, 0, 0)">RECmd</span> : <span style="color:orange">Analyse des hives de registre avec requêtes personnalisées</span>.  
    Permet d’interroger des clés précises (Run, Services, UserAssist, etc.) dans les hives offline.  
    Ex : détecter des valeurs de persistance créées par un malware dans NTUSER.DAT.  
    Fichiers requis :  
    - C:\Users\NomUser\NTUSER.DAT  
    - C:\Windows\System32\config\SYSTEM, SOFTWARE, SECURITY, SAM

  - <span style="color:rgb(255, 0, 0)">Registry Explorer</span> : <span style="color:orange">Navigateur GUI des fichiers de registre</span>. ✅✅  
    Permet d’explorer visuellement les hives, voir les clés, les valeurs binaires, et les timestamps LastWrite.  
    Ex : comparer les clés entre plusieurs profils utilisateurs.

  - <span style="color:rgb(255, 0, 0)">Registry Viewer</span> : <span style="color:orange">Outil GUI de FTK pour explorer les hives de registre</span>.  
    Interface simplifiée pour naviguer rapidement dans les valeurs d’un hive et annoter les résultats.  
    Ex : utilisé en forensic pour surligner les clés de type Run ou AppCompatFlags.

  - <span style="color:rgb(255, 0, 0)">MFTECmd.exe</span> : <span style="color:orange">Analyse complète du fichier $MFT</span>.  
    Permet de voir tous les fichiers du volume NTFS, même supprimés, avec les horodatages et chemins.  
    Ex : identifier tous les fichiers créés/modifiés lors d’une compromission, y compris ceux effacés après coup.  
    Fichier requis :  
    - \$MFT (à la racine du volume, extraite avec RawCopy, FTK, etc.)

  - <span style="color:rgb(255, 0, 0)">PECmd.exe</span> : <span style="color:orange">Analyse les fichiers Prefetch (exécutables récents)</span>.  
    Affiche le nom du programme, son chemin, le nombre d’exécutions et l’horodatage.  
    Ex : voir que notepad.exe a été exécuté 5 fois entre 14h et 17h.  
    Fichiers requis :  
    - C:\Windows\Prefetch\*.pf

  - <span style="color:rgb(255, 0, 0)">JLECmd.exe</span> : <span style="color:orange">Analyse les Jump Lists (applications et fichiers récents)</span>.  
    Permet de voir les fichiers ouverts par application, nombre d’ouvertures, chemin d’accès, horodatage.  
    Ex : prouver qu’un document Word sensible a été ouvert plusieurs fois depuis une clé USB.  
    Fichiers requis :  
    - C:\Users\NomUser\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations  
    - C:\Users\NomUser\AppData\Roaming\Microsoft\Windows\Recent\CustomDestinations

  - <span style="color:rgb(255, 0, 0)">LECmd.exe</span> : <span style="color:orange">Analyse les fichiers LNK (raccourcis Windows)</span>.  
    Affiche la cible du raccourci, l’horodatage, le chemin, le volume d’origine, etc.  
    Ex : voir qu’un raccourci vers C:\malwares\stealer.exe a été utilisé.  
    Fichiers requis :  
    - C:\Users\NomUser\AppData\Roaming\Microsoft\Windows\Recent\*.lnk

  - <span style="color:rgb(255, 0, 0)">EvtxECmd.exe</span> : <span style="color:orange">Analyse structurée des journaux Windows (.evtx)</span>.  
    Permet de parser les journaux d’événements système, application et sécurité, et d’extraire les champs clés.  
    Ex : extraire tous les Event ID 4688 (processus créés) dans le log Security.  
    Fichiers requis :  
    - C:\Windows\System32\winevt\Logs\*.evtx

  - <span style="color:rgb(255, 0, 0)">ShellBags Explorer</span> : <span style="color:orange">Affiche les dossiers ouverts via l’explorateur Windows</span>.  
    Permet de reconstituer l’activité de navigation utilisateur, même sur des dossiers supprimés.  
    Ex : prouver qu’un dossier caché C:\Secrets a été ouvert dans l’explorateur.  
    Fichiers requis :  
    - C:\Users\NomUser\NTUSER.DAT  
    - C:\Users\NomUser\AppData\Local\Microsoft\Windows\UsrClass.dat

  - <span style="color:rgb(255, 0, 0)">Timeline Explorer</span> : <span style="color:orange">Visualisation chronologique des artefacts forensiques (CSV)</span>.  
    Interface graphique pour lire les résultats des outils comme MFTECmd, PECmd, WxTCmd.  
    Ex : analyser toutes les actions système entre 13h00 et 13h30 sur un poste compromis.  
    Fichiers requis :  
    - Fichiers .csv générés par les outils Zimmerman.

  - <span style="color:rgb(255, 0, 0)">WxTCmd.exe</span> : <span style="color:orange">Timeline unifiée à partir de multiples artefacts système et registre</span>.  
    Combine les données de fichiers NTFS et artefacts utilisateurs pour créer une chronologie exhaustive.  
    Ex : reconstituer minute par minute les fichiers créés, déplacés, exécutés, ouverts par un attaquant.  
    Fichiers recommandés à placer dans un même dossier :  
    - \$MFT  
    - \$LogFile (C:\$LogFile)  
    - \$UsnJrnl (C:\$Extend\$UsnJrnl:$J)  
    - C:\Windows\Prefetch\*.pf  
    - C:\Users\NomUser\AppData\Roaming\Microsoft\Windows\Recent\AutomaticDestinations  
    - C:\Users\NomUser\AppData\Roaming\Microsoft\Windows\Recent\CustomDestinations  
    - C:\Users\NomUser\AppData\Roaming\Microsoft\Windows\Recent\*.lnk  
    - C:\Users\NomUser\NTUSER.DAT  
    - C:\Users\NomUser\AppData\Local\Microsoft\Windows\UsrClass.dat



# Forensic Disque :


### FAT = File allocation table

<span style="color:rgb(0, 176, 80)">Clusters :</span> basic storage unit of the FAT file system. Each file stored on a storage device can be considered a group of clusters
<span style="color:rgb(0, 176, 80)">Directory</span>: contains information about file identification, like file name...
<span style="color:rgb(0, 176, 80)">F</span><span style="color:rgb(0, 176, 80)">AT</span> = In summary, the bits that make up a file are stored in clusters. All the filenames on a file system, their starting clusters, and their lengths are stored in directories. And the location of each cluster on the disk is stored in the File Allocation Table.

### NTFS - Points Clés :

**<span style="color:rgb(0, 176, 80)">Journaling</span> : log des métadonnées dans `$LOGFILE` (aide à la récupération).
<span style="color:rgb(0, 176, 80)">Access Control</span>** : permissions utilisateur/fichier, contrairement à FAT.
 **<span style="color:rgb(0, 176, 80)">Volume Shadow Copy</span>** : snapshots de fichiers pour restauration.
 **<span style="color:rgb(0, 176, 80)">ADS (Alternate Data Streams)</span>** : plusieurs flux de données par fichier, utilisé par les navigateurs et aussi par des malwares.
<span style="color:rgb(0, 176, 80)"><span style="color:rgb(0, 176, 80)">$MFT</span></span> : base de données centrale listant tous les fichiers du volume.
**<span style="color:rgb(0, 176, 80)">$UsnJrnl</span>** : journal des changements de fichiers (forensic utile).
**<span style="color:rgb(0, 176, 80)">MFT Explorer</span>** : outil d’Eric Zimmerman pour lire/analyser le MFT.



# $MFT :base de données centrale listant tous les fichiers du volume.

- ###### FTK Imager : monte une image disque, puis exporte `$MFT`.
- ###### Autopsy : détecte et parse automatiquement `$MFT`.

La commande MFTECmd.exe -f  permet d’analyser le contenu du fichier $MFT, ce qui sert à :

- Lister tous les fichiers du disque, même supprimés (s’ils ne sont pas écrasés).
- Voir les métadonnées: noms, dates (création, modification, accès), tailles, chemins, etc.
- Identifier les fichiers cachés, renommer, déplacés ou récemment créés**.
- Observer l’activité suspecte (ex. fichiers créés lors d'une compromission).
- Reconstituer une timeline des actions sur le système.

```
MFTECmd.exe -f <path-to-$MFT-file> --csv <path-to-save-results-in-csv>
```



## ✅ Résumé des outils à utiliser selon le cas:

| Action observée                                | Outil principal            | Artefact                |
| ---------------------------------------------- | -------------------------- | ----------------------- |
| Logiciel exécuté (via système)                 | PECmd.exe                  | Prefetch                |
| Fichier ouvert par un logiciel                 | JLECmd.exe                 | Jump Lists              |
| Fichier lancé via raccourci                    | LECmd.exe                  | .lnk                    |
| Dossier visité dans explorateur                | ShellBagsExplorer          | Shellbags (registre)    |
| Exécutable lancé via GUI (Menu démarrer, etc.) | Registry Explorer ou RECmd | UserAssist (NTUSER.DAT) |
| Accès/Modif fichier NTFS                       | MFTECmd.exe, WxTCmd.exe    | $MFT / $UsnJrnl         |
|                                                |                            |                         |
