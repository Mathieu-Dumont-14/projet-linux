# Projet Linux Avancé

## Mon Premier Serveur Sécurisé & Monitoré

> **Auteur :** _Mathieu Dumont_  
> **Date :** _31/03/2025_  
> **Distribution :** Debian  

---

## Étape 0 – Outils de base à installer

Ce document décrit les outils de base à installer sur un système Debian, leurs rôles et une commande typique pour les utiliser.

## Installation des outils

Exécutez la commande suivante pour installer tous les outils nécessaires :

```sh
sudo apt update && sudo apt install -y \
  htop curl wget zsh git nano vim tree lsof ncdu ufw fail2ban \
  openssh-server sudo cron rsync net-tools iproute2 screen tmux lsb-release
```

## Description des outils

### 1. htop
- **Installation** : `sudo apt install -y htop`
- **Rôle** : Affiche une vue interactive des processus en cours.
- **Commande typique** : `htop`

### 2. curl
- **Installation** : `sudo apt install -y curl`
- **Rôle** : Permet de récupérer du contenu via HTTP/HTTPS.
- **Commande typique** : `curl -I https://example.com`

### 3. wget
- **Installation** : `sudo apt install -y wget`
- **Rôle** : Télécharge des fichiers depuis le web.
- **Commande typique** : `wget https://example.com/fichier.zip`

### 4. zsh
- **Installation** : `sudo apt install -y zsh`
- **Rôle** : Un shell alternatif à bash, souvent préféré pour sa personnalisation.
- **Commande typique** : `zsh`

### 5. oh-my-zsh
- **Installation** :
  ```sh
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
  ```
- **Rôle** : Un framework pour gérer la configuration de zsh.
- **Commande typique** : `exec zsh`

### 6. git
- **Installation** : `sudo apt install -y git`
- **Rôle** : Système de gestion de versions distribué.
- **Commande typique** : `git clone https://github.com/user/repo.git`

### 7. nano
- **Installation** : `sudo apt install -y nano`
- **Rôle** : Éditeur de texte simple en ligne de commande.
- **Commande typique** : `nano fichier.txt`

### 8. vim
- **Installation** : `sudo apt install -y vim`
- **Rôle** : Éditeur de texte avancé en ligne de commande.
- **Commande typique** : `vim fichier.txt`

### 9. tree
- **Installation** : `sudo apt install -y tree`
- **Rôle** : Affiche une arborescence des fichiers.
- **Commande typique** : `tree`

### 10. lsof
- **Installation** : `sudo apt install -y lsof`
- **Rôle** : Liste les fichiers ouverts par les processus.
- **Commande typique** : `lsof -i :80`

### 11. ncdu
- **Installation** : `sudo apt install -y ncdu`
- **Rôle** : Affiche l’utilisation du disque de manière interactive.
- **Commande typique** : `ncdu`

### 12. ufw
- **Installation** : `sudo apt install -y ufw`
- **Rôle** : Firewall simple à configurer.
- **Commande typique** : `ufw allow 22`

### 13. fail2ban
- **Installation** : `sudo apt install -y fail2ban`
- **Rôle** : Protège contre les tentatives de connexion brute-force.
- **Commande typique** : `systemctl status fail2ban`

### 14. openssh-server
- **Installation** : `sudo apt install -y openssh-server`
- **Rôle** : Permet d’accéder au serveur via SSH.
- **Commande typique** : `systemctl status ssh`

### 15. sudo
- **Installation** : `sudo apt install -y sudo`
- **Rôle** : Permet d’exécuter des commandes en tant qu’administrateur.
- **Commande typique** : `sudo apt update`

### 16. cron
- **Installation** : `sudo apt install -y cron`
- **Rôle** : Planificateur de tâches automatisées.
- **Commande typique** : `crontab -e`

### 17. rsync
- **Installation** : `sudo apt install -y rsync`
- **Rôle** : Outil de synchronisation de fichiers.
- **Commande typique** : `rsync -av source/ destination/`

### 18. net-tools
- **Installation** : `sudo apt install -y net-tools`
- **Rôle** : Fournit des outils réseau comme `ifconfig` et `netstat`.
- **Commande typique** : `ifconfig`

### 19. iproute2
- **Installation** : `sudo apt install -y iproute2`
- **Rôle** : Outils modernes pour la gestion des réseaux.
- **Commande typique** : `ip a`

### 20. screen
- **Installation** : `sudo apt install -y screen`
- **Rôle** : Permet d’exécuter des sessions de terminal persistantes.
- **Commande typique** : `screen -S session_name`

### 21. tmux
- **Installation** : `sudo apt install -y tmux`
- **Rôle** : Alternative à `screen` pour gérer plusieurs sessions.
- **Commande typique** : `tmux new -s session_name`

### 22. lsb-release
- **Installation** : `sudo apt install -y lsb-release`
- **Rôle** : Fournit des informations sur la distribution Linux.
- **Commande typique** : `lsb_release -a`

---

## Étape 1 – Déploiement initial

### 1. Mise à jour du système
```sh
sudo apt update && sudo apt upgrade -y && sudo apt autoremove -y
```

### 2. Création d'un utilisateur admin avec sudo
```sh
sudo adduser admin
sudo usermod -aG sudo admin
```

### 3. Activation de l'authentification SSH par clé

#### 3.1 Générer une clé SSH sur Windows

Depuis PowerShell ou Git Bash sur votre machine Windows :
```sh
ssh-keygen -t rsa -b 4096 -C "votre@email.com"
```
Appuyez sur `Entrée` pour accepter l'emplacement par défaut. Une paire de clés sera créée :
- Clé publique : `C:\Users\VotreNom\.ssh\id_rsa.pub`
- Clé privée : `C:\Users\VotreNom\.ssh\id_rsa`

#### 3.2 Copier la clé publique sur le VPS

Utilisez cette commande depuis votre machine Windows (avec Git Bash ou PowerShell si OpenSSH est installé) pour copier la clé automatiquement :
```sh
ssh-copy-id -i C:\Users\VotreNom\.ssh\id_rsa.pub admin@IP_DU_SERVEUR
```

Si la commande `ssh-copy-id` ne fonctionne pas, copiez manuellement le contenu de la clé publique (`id_rsa.pub`) et ajoutez-le sur le VPS avec :
```sh
sudo mkdir -p /home/admin/.ssh
sudo nano /home/admin/.ssh/authorized_keys
```
Collez la clé, puis :
```sh
sudo chown -R admin:admin /home/admin/.ssh
sudo chmod 700 /home/admin/.ssh
sudo chmod 600 /home/admin/.ssh/authorized_keys
```

> 🔒 **Important** : Ne pas mettre la clé dans `/etc/ssh/`, ce dossier est réservé à la configuration du serveur SSH (comme `sshd_config`). Les clés utilisateur doivent être dans le dossier `~/.ssh/` de l'utilisateur concerné (ex : `/home/admin/.ssh/`).

#### 3.3 Modifier la configuration SSH
```sh
sudo nano /etc/ssh/sshd_config
```
Modifier les lignes suivantes :
```
PasswordAuthentication no
PubkeyAuthentication yes
```
Puis redémarrer le service SSH :
```sh
sudo systemctl restart ssh
```

### 4. Interdiction de la connexion root
```sh
sudo nano /etc/ssh/sshd_config
```
Modifier :
```
PermitRootLogin no
```
Puis redémarrer SSH :
```sh
sudo systemctl restart ssh
```

---

## Étape 2 – Sécurisation

### 1. Configuration du pare-feu UFW
```sh
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```

### 2. Installation et configuration de Fail2Ban
```sh
sudo apt install -y fail2ban
sudo systemctl enable fail2ban
sudo nano /etc/fail2ban/jail.local
```
Ajouter :
```
[sshd]
enabled = true
bantime = 3600
findtime = 600
maxretry = 5
```
Redémarrer Fail2Ban :
```sh
sudo systemctl restart fail2ban
```

---

## Étape 3 – Monitoring

### 1. Installation de Netdata
Via Netdata Cloud :
- Se connecter à [https://app.netdata.cloud](https://app.netdata.cloud)
- Créer ou rejoindre un espace
- Cliquer sur "Connect Nodes" et copier la commande d'installation Debian
- Exécuter la commande sur le VPS

### 2. Supervision CPU, RAM, Disque, Réseau
Accessible via [Netdata Cloud](https://app.netdata.cloud/spaces/)

### 3. Alerte disque > 80%
```sh
sudo nano /etc/netdata/health.d/disk_space.conf
```
Ajouter :
```ini
alarm: disk_space_usage
  on: disk_space
  lookup: average -10m
  units: %
  warn: $this > 80
  crit: $this > 90
```
Redémarrer Netdata :
```sh
sudo systemctl restart netdata
```

---

## Étape 4 – Serveur Web & Reverse Proxy

### 1. Installer et configurer Nginx
```sh
sudo apt install -y nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```

### 2. Reverse proxy vers un conteneur Docker
```sh
sudo nano /etc/nginx/sites-available/reverse-proxy
```
Ajouter :
```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
Activer la config :
```sh
sudo ln -s /etc/nginx/sites-available/reverse-proxy /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

### 3. Certificat HTTPS avec Certbot
```sh
sudo apt install -y certbot python3-certbot-nginx
sudo certbot --nginx -d example.com
```
Vérifier le renouvellement automatique :
```sh
sudo certbot renew --dry-run
```

---

## Étape 5 – Test de charge

### 1. Installation Apache Benchmark (ab)
```sh
sudo apt install apache2-utils
```
### 2. Lancer un test
```sh
ab -n 1000 -c 10 http://example.com/
ab -V
```

### screen du résultats
![alt text](<capture resulat test ab.png>)

> Le serveur est réactif et supporte une charge importante avec peu de latence.

---

## Étape 6 – Maintenance & Sauvegarde

### 1. Script Bash de sauvegarde
```bash
#!/bin/bash
BACKUP_DIR="/home/backup"
DATE=$(date +%F-%H-%M)
DEST="$BACKUP_DIR/backup_$DATE.tar.gz"

# Créer le dossier s'il n'existe pas
mkdir -p "$BACKUP_DIR"

# Créer l'archive
sudo tar -czvf "$DEST" /etc /var/log /home/mathieu

```

### 2. Automatisation via cron (toutes les 6h)
```sh
crontab -e
```
Ajouter :
```
0 */6 * * * /home/admin/backup.sh
```

---

## Étape 7 – Documentation

### 1. Versionnement Git
```sh
git init
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:username/projet-linux.git
git push -u origin main
```

---