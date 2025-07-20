
##  Techniques d’anti-analyse des malwares

### 🔒 Packing et Obfuscation
- Contenu compressé/chiffré pour cacher les strings/APIs
- `pecheck` → flags suspects : `MEM_WRITE + MEM_EXECUTE`, peu d’imports, pas de .text

---

### 🕵️ Sandbox Evasion
| Technique | Description |
|----------|-------------|
| `long sleep calls` | Délai artificiel pour **tromper les sandboxes à durée limitée** |
| `user activity detection` | Attend activité souris/clavier |
| `footprinting user activity` | Vérifie historique navigateur, fichiers récents... |
| `detecting VMs` | Détecte VMware/VirtualBox via pilotes/artefacts |

---

## 🧠 Interpréter les APIs Importées (pecheck/strings)

| API | Signification probable |
|-----|------------------------|
| `CreateProcessA`, `TerminateProcess` | Manipulation de processus |
| `LoadLibraryA`, `GetProcAddress` | Chargement dynamique de librairies |
| `InternetOpen`, `URLDownloadToFile` | Accès internet / téléchargement |
| `RegSetValueExA`, `RegQueryValueExA` | Interaction avec le registre |
| `GetComputerNameW`, `SetFileTime` | Collecte info système / manipulation fichiers |
