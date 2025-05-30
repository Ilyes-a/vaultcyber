
# Linux Forensics – Commandes essentielles

## Informations système

```bash
cat /etc/os-release               # Détail OS
cat /etc/hostname                 # Nom machine
cat /etc/timezone                # Fuseau horaire
```

## Comptes utilisateurs

```bash
cat /etc/passwd | column -t -s :  # Utilisateurs + UID/GID
cat /etc/group                   # Groupes + membres
sudo cat /etc/sudoers            # Liste sudoers
```

## Connexions et réseau

```bash
ip address show                  # IP et MAC
cat /etc/network/interfaces      # Interfaces réseau
netstat -natp                    # Connexions actives
cat /etc/hosts                   # Résolution DNS locale
cat /etc/resolv.conf            # DNS configurés
```

## Connexions & authentifications

```bash
sudo last -f /var/log/wtmp       # Connexions réussies temps des sessions
sudo last -f /var/log/btmp       # Connexions échouées
cat /var/log/auth.log | tail    # Auth logs
```

## Services et démarrage

```bash
ls /etc/init.d/                 # Services au boot
cat /etc/crontab                # Cron jobs commands that run periodically                                      after a set amount of time

cat ~/.bashrc                   # Persistence user
cat /etc/bash.bashrc           # Persistence global
cat /etc/profile                # Autre init global
ps aux                         # voir les process en cours
```

## Exécution de commandes

```bash
cat ~/.bash_history             # Historique user
cat /var/log/auth.log* | grep -i COMMAND | tail  # sudo cmds
cat ~/.viminfo                  # Fichiers ouverts avec vim
```

## Logs système

```bash
cat /var/log/syslog* | head    # Syslog
cat /var/log/auth.log* | head  # Auth logs
ls /var/log/                   # Logs apps tierces
ls /var/log/apache2/           # Logs Apache
```
