### **Exemples de LOLBins sous Windows**
(Living Off the Land Binaries)
MITRE : Utiliser des programmes **légitimes** déjà présents sur le système pour Éviter la détectionwd


|Binaire|Usage légitime|Usage détourné (LOLBins)|
|---|---|---|
|`powershell.exe`|Automatiser des tâches|Télécharger et exécuter du code malveillant (e.g., `Invoke-WebRequest`)|
|`cmd.exe`|Interface ligne de commande|Lancer des scripts, exécuter des commandes de persistance|
|`certutil.exe`|Gérer les certificats|Télécharger un fichier distant (`certutil -urlcache -split -f`)|
|`regsvr32.exe`|Enregistrer des DLLs|Charger une DLL malveillante distante via un script SCT|
|`mshta.exe`|Exécuter du code HTML/JS|Lancer du code malveillant via un fichier `.hta`|
|`rundll32.exe`|Exécuter des fonctions dans une DLL|Utilisé pour lancer du code arbitraire contenu dans une DLL|
|`wmic.exe`|Interface WMI|Exécution de commandes à distance ou récupération d’infos système|
|`bitsadmin.exe`|Gérer les téléchargements BITS|Télécharger et exécuter du contenu distant|
