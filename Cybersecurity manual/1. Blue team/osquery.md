
# OSQuery 
## Documentation avec les tables utiles sur différents OS : https://osquery.io/schema/5.5.1/


## 1. Commandes de base
 
| Commande        | Description                                     |
|------------------|-------------------------------------------------|
| `osqueryi`       | Lance l’interface interactive                   |
| `osqueryd`       | Lance l’agent en mode démon                     |
| `.tables`        | Liste toutes les tables disponibles             |
| `.schema <table>`| Affiche le schéma de la table spécifiée         |
| `.help`          | Affiche l’aide de la console interactive        |
| `.mode column`   | Affichage en colonnes                           |
| `.mode line`     | Affichage ligne par ligne                       |

## 2. Tables utiles par catégorie

### Processus

```sql
SELECT pid, name, path, cmdline FROM processes WHERE name LIKE '%powershell%';
```

### Réseau

```sql
SELECT * FROM listening_ports;
SELECT * FROM process_open_sockets WHERE remote_port > 1024;
```

### Démarrage / Persistence

```sql
SELECT * FROM startup_items;
SELECT * FROM autostart;
```

### Utilisateurs

```sql
SELECT uid, username, directory, shell FROM users;
SELECT * FROM logged_in_users;
```

### Activité système

```sql
SELECT * FROM file_events WHERE action = 'CREATED';
SELECT * FROM process_events WHERE cmdline LIKE '%mimikatz%';
```

### Logiciels installés

```sql
SELECT * FROM programs WHERE name LIKE '%chrome%';
SELECT name, version FROM rpm_packages;
```

### Services et modules

```sql
SELECT * FROM services WHERE path LIKE '%svchost%';
SELECT * FROM kernel_modules WHERE name = 'usb_storage';
```

## 3. Requêtes typiques en Threat Hunting

### Détection de shells suspects

```sql
SELECT * FROM processes WHERE name IN ('cmd.exe', 'powershell.exe', 'pwsh.exe') AND parent NOT IN ('explorer.exe', 'services.exe');
```

### Fichiers exécutables dans des répertoires temporaires

```sql
SELECT * FROM file WHERE directory LIKE '%Temp%' AND mode LIKE '%x%';
```

### Recherche de connexions sortantes vers ports suspects

```sql
SELECT * FROM process_open_sockets WHERE remote_port IN (4444, 8080, 53);
```

## 4. Intégration & Logs

- Les logs sont exportables via **osqueryd** avec :
```bash
osqueryd --flagfile=/etc/osquery/osquery.flags
```

- Format de sortie configurable (JSON, journald, filesystem, etc.)
- Intégration avec : ELK, Splunk, Fleet, Kolide, Wazuh, etc.

## 5. Bonnes pratiques

- Restreindre les tables autorisées en production
- Limiter les intervalles de collecte sur les tables lourdes (`process_events`, `file_events`)
- Activer la `read_only` mode sur les endpoints
- Superviser les erreurs via la table `osquery_schedule`
