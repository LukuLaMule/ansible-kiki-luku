# Formation Ansible - Exercices par Atelier

## Sommaire
- [Ansible par la pratique (3) – Installation](#atelier-01)
- [Ansible par la pratique (4) – Authentification](#atelier-03)
- [Ansible par la pratique (6) – Configuration de base](#atelier-06)


## ATELIER-01

### Exercice 1: Installation d'Ansible depuis les dépôts officiels

#### Démarrage de la VM Ubuntu
```bash
cd ~/formation-ansible/atelier-01
vagrant up ubuntu
```

#### Connexion à la VM
```bash
vagrant ssh ubuntu
```

#### Rafraîchissement des paquets
```bash
sudo apt update
```

#### Recherche du paquet Ansible
```bash
apt-cache search --names-only ansible
```

#### Installation du paquet officiel
```bash
sudo apt install -y ansible
```

#### Vérification de l'installation et version d'Ansible
```bash
ansible --version
```

#### Déconnexion et suppression de la VM
```bash
exit
vagrant destroy -f ubuntu
```

---

### Exercice 2: Installation d'Ansible via un PPA

#### Démarrage et connexion à la VM Ubuntu
(Pareil que pour l'exercice 1)

#### Ajout du dépôt PPA Ansible
```bash
sudo apt-add-repository ppa:ansible/ansible -y
sudo apt update
```

#### Installation d'Ansible depuis le PPA
```bash
sudo apt install -y ansible
```

#### Vérification de la version installée
```bash
ansible --version
```

#### Comparaison des versions
Noter la version installée et la comparer avec celle de l'exercice précédent.

#### Déconnexion et suppression de la VM
```bash
exit
vagrant destroy -f ubuntu
```

---

### Exercice 3: Installation d'Ansible sur Rocky Linux via PIP et Virtualenv

#### Démarrage de la VM Rocky Linux
```bash
vagrant up rocky
```

#### Connexion à la VM
```bash
vagrant ssh rocky
```

#### Installation des prérequis
```bash
sudo dnf install -y python3 python3-pip
```

#### Création et activation d'un environnement virtuel
```bash
python3 -m venv ansible-venv
source ansible-venv/bin/activate
```

#### Installation d'Ansible via PIP
```bash
pip install --upgrade pip
pip install ansible
```

#### Vérification de l'installation et version d'Ansible
```bash
ansible --version
```

#### Désactivation de l'environnement virtuel
```bash
deactivate
```

#### Déconnexion et suppression de la VM
```bash
exit
vagrant destroy -f rocky
```

---

### Gestion des autres VMs

#### Démarrer une VM Debian
```bash
vagrant up debian
vagrant ssh debian
```

#### Démarrer une VM SUSE
```bash
vagrant up suse
vagrant ssh suse
```

#### Détruire une VM spécifique
```bash
vagrant destroy -f debian  
vagrant destroy -f suse    
```

---

## ATELIER-02

### Exercice : Configuration du Control Host et connexion aux Target Hosts

#### Accéder au répertoire de l'atelier 03
```bash
cd ~/formation-ansible/atelier-03
```

#### Démarrer les VM
```bash
vagrant up
```

#### Connexion au Control Host
```bash
vagrant ssh ansible
```

#### Vérifier la présence d'Ansible
```bash
type ansible
```

#### Configurer la résolution DNS locale
Éditer le fichier `/etc/hosts` sur le Control Host :
```bash
sudo vim /etc/hosts
```
Ajouter :
```
192.168.56.10      control 	      
192.168.56.20      target01
192.168.56.30      target02
192.168.56.40      target03
```

#### Collecter les clés SSH des Target Hosts
```bash
ssh-keyscan -t rsa target01 target02 target03 >> ~/.ssh/known_hosts
```

#### Tester la connectivité des Target Hosts
```bash
for HOST in target01 target02 target03; do ping -c 1 -q $HOST; done
```
#### On créer une paire de clef
```bash
ssh-keygen 
```

#### Je distribue la clé publique sur mes Target Hosts en fournissant à chaque fois le mot de passe vagrant :

```bash

ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03

```


#### Générer et distribuer la clé SSH
```bash
vagrant ssh ansible
ssh-keygen -t rsa -b 3072
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```

#### Tester le ping 
```bash
ansible all -i target01,target02,target03 -m ping
```

#### Quitter le Control Host et supprimer les VM
```bash
exit
vagrant destroy -f
```

---



