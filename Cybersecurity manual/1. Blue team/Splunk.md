
# Splunk Query 

## 1. Recherches de base

```
index=web_logs
index=sysmon source="WinEventLog:Security"
index=firewall action=blocked src_ip=10.0.0.0/8
index=windows sourcetype=WinEventLog:Security EventCode=4625
```

## 2. Filtres par champ

```
index=windows status=500
index=auth user=root OR user=admin
index=apache status!=200
index=oswin source="WinEventLog:Security" EventCode IN (4624, 4625)
```

## 3. Combinaisons logiques

```
index=logs (status=500 OR status=404) AND method=GET
index=windows NOT (EventCode=4624 OR EventCode=4634)
```

## 4. Fonctions statistiques

```
index=apache | stats count by status
index=auth | stats avg(duration) by user
index=proxy | stats dc(src_ip) as unique_ips by user
index=fw | stats sum(bytes_out) as total_out by src_ip
```

## 5. timechart

```
index=apache | timechart count by status
index=sysmon | timechart span=5m avg(cpu_usage) by host
index=proxy | timechart span=1h dc(user) as distinct_users
```

## 6. eventstats / streamstats

```
index=apache | eventstats avg(response_time) as avg_time | where response_time > avg_time
index=fw | streamstats count by src_ip
index=sysmon | streamstats current=f window=3 avg(cpu) as avg_cpu
```

## 7. eval

```
index=web | eval ratio=bytes/response_time
index=linux | eval level=if(status==200, "OK", "ERROR")
index=proxy | eval is_admin=if(user="admin", 1, 0)
```

## 8. table / fields / sort / rename

```
index=sysmon | table _time, host, process_name
index=apache | fields - _raw
index=web | sort -bytes
index=fw | rename src_ip as source_ip
```

## 9. regex / rex

```
index=web | regex _raw=".*login.*"
index=web | rex "user=(?<username>[^&]+)"
index=web | rex field=uri_path "/(?<resource>[a-z]+)\.html"
```

## 10. top / rare

```
index=web | top user
index=web | rare src_ip
```

## 11. transaction

```
index=auth | transaction user startswith="login" endswith="logout"
index=fw | transaction src_ip maxspan=30s
```

## 12. lookup

```
index=web | lookup user_roles user OUTPUT role
index=access | lookup threat_list ip OUTPUT is_malicious
```

## 13. fonctions de date/heure

```
index=sysmon | eval timestamp=strftime(_time, "%Y-%m-%d %H:%M:%S")
index=auth | eval time_diff=now()-_time | where time_diff < 300
```

## 14. sous-requêtes avec map

```
| inputlookup compromised_users.csv | map search="search index=auth user=$user$"
```

## 15. Triage d'alerte

```
index=windows sourcetype="WinEventLog:Security" EventCode=4625 | stats count by src_ip, user | where count > 10
```

## 16. Conditions imbriquées

```
index=auth | eval level=case(status=200, "ok", status=403, "forbidden", status=500, "error")
```

## 17. alertes temporelles

```
index=proxy | timechart span=1h count by action | where count > 100
```

## 18. Requêtes Splunk avancées (détection, SOC)

```
index=windows EventCode=4688 New_Process_Name="*cmd.exe*" | stats count by user, host
index=sysmon EventCode=1 | where process_name="powershell.exe" AND parent_process!="explorer.exe"
index=windows EventCode=4663 Object_Name="*.ps1" | stats count by user
index=sysmon CommandLine="*Invoke-WebRequest*" | stats count by host
index=* "powershell.exe" OR "cmd.exe" | stats count by user
```

## 19. Exfiltration / Anomalies

```
index=fw bytes_out>10000000 | stats sum(bytes_out) by src_ip
index=web method=POST uri_path="/upload" | stats count by user
```

## 20. Fichiers suspects

```
index=sysmon File_Name="*.exe" Image="*temp*"
index=sysmon | regex CommandLine=".*(wget|curl).*"
```

# Sources de logs Splunk

| **Sourcetype / Index** | **Description** |
|------------------------|-----------------|
| `wineventlog`          | Contient les logs d’événements Windows standards (Application, Sécurité, Système). |
| `winRegistry`          | Logs liés au registre Windows (création, modification, suppression de clés/valeurs). |
| `XmlWinEventLog`       | Logs Sysmon détaillés (processus, connexions réseau, accès fichiers, etc.). Crucial pour l'investigation. |
| `fortigate_utm`        | Logs issus des pare-feux Fortinet (FortiGate). Permet de tracer les flux, blocages, et politiques UTM. |
| `iis`                  | Logs du serveur web IIS (requêtes HTTP, codes de réponse, chemins accédés). |
| `Nessus:scan`          | Résultats des scans de vulnérabilités du scanner Nessus. |
| `Suricata`             | Détails des alertes générées par l’IDS Suricata. Montre quelle règle a été déclenchée et pourquoi. |
| `stream:http`          | Flux réseau liés au trafic HTTP capturé. |
| `stream:DNS`           | Flux réseau liés au trafic DNS (requêtes, réponses, domaines résolus). |
| `stream:icmp`          | Flux réseau liés au trafic ICMP (ping, echo request/reply). |

Exemples : 

index="* " sourcetype=stream:http dest_ip="192.168.250.70"

Index=bot 