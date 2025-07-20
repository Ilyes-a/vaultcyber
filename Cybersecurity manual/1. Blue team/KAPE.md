# 🛠️ KAPE Cheat Sheet
**KAPE (Kroll Artifact Parser and Extractor)** est un outil **de triage forensique rapide** destiné à collecter et analyser efficacement des artéfacts numériques sur des systèmes Windows. Il est principalement utilisé dans les contextes **d’investigation numérique**, **d’incident response** ou d’analyse **post-compromission**.

---

## 🎯 Objectifs principaux de KAPE

|Fonction|Description|
|---|---|
|**Collecte**|Copier rapidement les fichiers critiques (logs, registres, préfetch, etc.) d’un système live ou image disque sans attendre un dump complet.|
|**Traitement**|Exécuter des outils d’analyse (comme ceux d’Eric Zimmerman) sur les fichiers collectés pour en extraire des informations exploitables (CSV, TXT).|
## 📌 Objectif
- **Targets** = Collecte de fichiers/artéfacts
- **Modules** = Traitement des fichiers collectés

---

## 📁 Arborescence de base
```
KAPE/
├── kape.exe              # CLI
├── gkape.exe             # GUI
├── Get-KAPEUpdate.ps1    # Script de mise à jour
├── Targets/              # Fichiers .tkape
│   ├── Compound/         # Cibles composées (!KapeTriage, !SANS_triage, ...)
│   ├── !Local/           # Cibles locales non synchronisées
│   ├── !Disabled/        # Cibles désactivées
├── Modules/              # Fichiers .mkape
│   ├── Compound/         # Modules composés (!EZParser, ...)
│   ├── bin/              # Exécutables (ex: outils Eric Zimmerman)
│   ├── !Local/
│   ├── !Disabled/
```

---

## 🎯 Targets (.tkape)

- Syntaxe : définit les chemins/artéfacts à copier.
- Exemple Prefetch :
```tkape
Paths=C:\Windows\Prefetch,C:\Windows.old\Prefetch
FileMask=*.pf
```

### Cibles composées :
- `!KapeTriage` : collecte large pour triage
- `!SANS_triage`, `EvidenceOfExecution`, etc.

---

## ⚙️ Modules (.mkape)

- Syntaxe : définit l'exécutable, les arguments, et l’export
- Exemple :
```mkape
Program=PECmd.exe
Arguments=-d %source% --csv %destination% -q
OutputExtension=csv
```

- Module composé : `!EZParser`

---

## 📦 Répertoire bin/
Contient les exécutables nécessaires non natifs (Eric Zimmerman Tools : PECmd.exe, EvtxECmd.exe, etc.)

---

## 🧑‍💻 Commandes CLI

### Collecte seule (target)
```bash
kape.exe --tsource C: --target KapeTriage --tdest C:\chemin\target
```

### Traitement seul (module)
```bash
kape.exe --msource C:\chemin\target --module !EZParser --mdest C:\chemin\module
```

### Collecte + Traitement
```bash
kape.exe --tsource C: --target KapeTriage --tdest C:\target --module !EZParser --mdest C:\module
```

---

## 🧾 Variables

| Variable | Description                    |
|----------|--------------------------------|
| `%d`     | Timestamp (ex: 20250608T1523)  |
| `%m`     | Nom machine                    |
| `%s`     | Lettre de lecteur système      |

---

## 🧩 Options principales

| Switch         | Description                                         |
|----------------|-----------------------------------------------------|
| `--tsource`    | Source de collecte (ex: C:)                         |
| `--tdest`      | Répertoire destination des fichiers collectés       |
| `--target`     | Nom du ou des targets                               |
| `--tflush`     | Supprime le contenu du dossier cible avant collecte |
| `--mdest`      | Répertoire sortie des modules                       |
| `--module`     | Nom du ou des modules                               |
| `--vss`        | Traite les VSC                                      |
| `--zip`        | Compresse dans une archive ZIP                      |
| `--vhdx`       | Archive en VHDX                                     |
| `--debug`      | Debug output                                        |
| `--sync`       | MAJ targets/modules depuis GitHub                   |

---

## 🗂️ Mode batch (_kape.cli)

Contenu du fichier :
```
--tsource C: --target KapeTriage --tdest C:\target --mdest C:\module --module !EZParser
```
Puis exécution :
```bash
kape.exe (en tant qu'administrateur)
```

---

## 🔎 Analyse des résultats

- Résultats des modules dans `--mdest`, classés par catégories (ex: Registry, EventLogs, FileDeletion, etc.)
- Visualisation possible avec EZViewer (inclus)

---

## 📚 Documentation
- Site officiel : [https://ericzimmerman.github.io/KapeDocs/](https://ericzimmerman.github.io/KapeDocs/)