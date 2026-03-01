# TP3 — Déploiement d'une application 3-tiers avec Vagrant

## Architecture

```
┌──────────────────────┐     ┌──────────────────────┐     ┌──────────────────────┐
│    server-front      │────▶│    server-back       │────▶│    server-dba        │
│   192.168.33.13      │     │   192.168.33.11      │     │   192.168.33.12      │
│                      │     │                      │     │                      │
│  Nginx + Angular 17  │     │  Spring Boot 3       │     │  MySQL 8             │
│  Port hôte : 8080    │     │  JDK 17              │     │  Port : 3306         │
│                      │     │  Port hôte : 8083    │     │                      │
└──────────────────────┘     └──────────────────────┘     └──────────────────────┘
```

---

## Prérequis

- VirtualBox
- Vagrant

---

## Étape 1 — Démarrer les VMs

```bash
vagrant up server-dba
vagrant up server-back
vagrant up server-front
```

---

## Étape 2 — Déploiement de server-dba (MySQL)

```bash
vagrant ssh server-dba
sudo apt-get update -y
```

### Installation de MySQL

```bash
sudo apt-get install -y mysql-server
```

![Installation MySQL](installation_de_mysql.png)

```bash
sudo systemctl start mysql
sudo systemctl enable mysql
sudo mysql
```

```sql
CREATE DATABASE appdb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'appuser'@'%' IDENTIFIED BY 'AppPassword123!';
GRANT ALL PRIVILEGES ON appdb.* TO 'appuser'@'%';
FLUSH PRIVILEGES;
EXIT;
```

```bash
# Autoriser les connexions distantes
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
# Modifier : bind-address = 0.0.0.0

sudo systemctl restart mysql
```

### Informations de connexion

| Paramètre | Valeur          |
|-----------|-----------------|
| Host      | 192.168.33.12   |
| Port      | 3306            |
| Base      | appdb           |
| User      | appuser         |
| Password  | AppPassword123! |

---

## Étape 3 — Déploiement de server-back (Spring Boot)

```bash
vagrant ssh server-back
sudo apt-get update -y
```

### Installation de JDK 17

```bash
sudo apt-get install -y openjdk-17-jdk
```

![Installation JDK 17](installation_jdk.png)

```bash
sudo apt-get install -y maven
sudo mkdir -p /opt/app
```

### Upload et déploiement du JAR

```bash
# Depuis la machine hôte
vagrant upload examen-0.0.1-SNAPSHOT.war /tmp/examen-0.0.1-SNAPSHOT.war server-back
```

![Upload du WAR](Capture_d_écran_2026-02-17_133142.png)

```bash
# Depuis server-back : copier et lancer
sudo cp /tmp/examen-0.0.1-SNAPSHOT.war /opt/app/
sudo java -jar /opt/app/examen-0.0.1-SNAPSHOT.war
```

### Lancer les tests backend

```bash
cd /vagrant/backend
mvn test
```

---

## Étape 4 — Déploiement de server-front (Angular + Nginx)

```bash
vagrant ssh server-front
sudo apt-get update -y
sudo apt-get install -y nginx
```

```bash
# Installation Node.js 20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# Swap pour éviter les crashs mémoire
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# Build Angular
cd ~
npm install
npm run build:prod

# Déployer dans Nginx
sudo cp -r ~/dist/frontend/browser/* /var/www/html/
sudo systemctl restart nginx
```

---

## Accès aux applications

| Service          | URL                                |
|------------------|------------------------------------|
| Frontend Angular | http://localhost:8080              |
| API REST         | http://localhost:8083/api/produits |

---

## Commandes Vagrant utiles

```bash
vagrant status                  # État des VMs
vagrant halt                    # Arrêter toutes les VMs
vagrant destroy -f              # Supprimer toutes les VMs
vagrant ssh server-back         # Se connecter en SSH
```
