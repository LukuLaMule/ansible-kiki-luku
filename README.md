# ATELIER-01

## Exercice 1: Installation d'Ansible depuis les dépôts officiels

### Démarrage de la VM Ubuntu
```bash
cd ~/formation-ansible/atelier-01
vagrant up ubuntu
```

### Connexion à la VM
```bash
vagrant ssh ubuntu
```

### Rafraîchissement des paquets
```bash
sudo apt update
```

### Recherche du paquet Ansible
```bash
apt-cache search --names-only ansible
```

### Installation du paquet officiel
```bash
sudo apt install -y ansible
```

### Vérification de l'installation et version d'Ansible
```bash
ansible --version
```

### Déconnexion et suppression de la VM
```bash
exit
vagrant destroy -f ubuntu
```

---

## Exercice 2: Installation d'Ansible via un PPA

### Démarrage et connexion à la VM Ubuntu
(Pareil que pour l'exercice 1)

### Ajout du dépôt PPA Ansible
```bash
sudo apt-add-repository ppa:ansible/ansible -y
sudo apt update
```

### Installation d'Ansible depuis le PPA
```bash
sudo apt install -y ansible
```

### Vérification de la version installée
```bash
ansible --version
```

### Comparaison des versions
Noter la version installée et la comparer avec celle de l'exercice précédent.

### Déconnexion et suppression de la VM
```bash
exit
vagrant destroy -f ubuntu
```

---

## Exercice 3: Installation d'Ansible sur Rocky Linux via PIP et Virtualenv

### Démarrage de la VM Rocky Linux
```bash
vagrant up rocky
```

### Connexion à la VM
```bash
vagrant ssh rocky
```

### Installation des prérequis
```bash
sudo dnf install -y python3 python3-pip
```

### Création et activation d'un environnement virtuel
```bash
python3 -m venv ansible-venv
source ansible-venv/bin/activate
```

### Installation d'Ansible via PIP
```bash
pip install --upgrade pip
pip install ansible
```

### Vérification de l'installation et version d'Ansible
```bash
ansible --version
```

### Désactivation de l'environnement virtuel
```bash
deactivate
```

### Déconnexion et suppression de la VM
```bash
exit
vagrant destroy -f rocky
```
