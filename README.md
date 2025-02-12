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

## ATELIER-03

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
## ATELIER-06

### Ansible par la pratique (6) – Configuration de base

#### Démarrer les vm 
```bash
vagrant up 
```

#### Connectez-vous au Control Host :

```bash
vagrant ssh control
```

#### Éditez /etc/hosts de manière à ce que les Target Hosts soient joignables par leur nom d’hôte simple.

```bash
nano /etc/hosts
```

```bash
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
127.0.2.1 control control

192.168.56.10 control
192.168.56.20 target01
192.168.56.30 target02
192.168.56.40 target03
``` 
### Générer une clé SSH (laisser les options par défaut)
```bash
ssh-keygen -t rsa -b 3072
```

### Copier la clé publique sur les Target Hosts
```bash
ssh-copy-id vagrant@target01
ssh-copy-id vagrant@target02
ssh-copy-id vagrant@target03
```
## 6. Installer Ansible
```bash
sudo apt update && sudo apt install -y ansible

vagrant@control:$ ansible --version | head -n 2
ansible 2.10.8
  config file = None
```
## 7. Envoyer un ping Ansible sans configuration
```bash
ansible all -i target01,target02,target03 -m ping
```
![alt text](image.png)


## 8. Créer un répertoire de projet
```bash
mkdir ~/monprojet
cd ~/monprojet
```

## 9. Créer un fichier de configuration Ansible
```bash
touch ansible.cfg

vagrant@control:~/monprojet$ ls 
ansible.cfg

```
## 10. Vérifier si `ansible.cfg` est bien pris en compte
```bash

vagrant@control:~/monprojet$ ANSIBLE_CONFIG=~/monprojet/ansible.cfg 
```

Résultas
```bash
ansible --version
ansible 2.10.8
  config file = /home/vagrant/monprojet/ansible.cfg
  configured module search path = ['/home/vagrant/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.10.12 (main, Mar 22 2024, 16:50:05) [GCC 11.4.0]
vagrant@control:~/monprojet$ 

```

## 11. Définir un inventaire
Créer un fichier `hosts` dans `monprojet` :
```bash
echo -e "[testlab]\ntarget01\ntarget02\ntarget03" > hosts
```
## 12. Activer la journalisation Ansible
Ajouter ceci à `ansible.cfg` :
```bash
echo -e "[defaults]\nlog_path = ~/journal/ansible.log" > ansible.cfg
```
## 13. Tester la journalisation
```bash
ansible all -i hosts -m ping
cat ~/journal/ansible.log
```
![alt text](image-1.png)

## 14. Définir explicitement l’utilisateur `vagrant`
Ajouter cette ligne au fichier `hosts` :
```bash
echo -e "[testlab:vars]\nansible_user=vagrant" >> hosts
```
## 15. Envoyer un ping Ansible vers le groupe `[all]`
```bash
ansible all -i hosts -m ping

```

J'ai pris un fail car mon fichier hosts était mal configuré, il manquait les hosts target : 
```txt
vagrant@control:~/journal$ cat ~/journal/hosts 
[testlab]
target01
target02
target03

[testlab:vars]
ansible_user=vagrant
vagrant@control:~/journal$ 
```
C'est mieux une fois édité : 
![alt text](image-2.png)

## 16. Définir l'élévation des droits pour `vagrant`
Ajouter à `hosts` :
```bash
echo "ansible_become=true" >> hosts
```


## 17. Afficher la première ligne du fichier `/etc/shadow` sur les Target Hosts
```bash
ansible all -i hosts -m command -a "head -n 1 /etc/shadow"
```
![alt text](image-3.png)


## 18. Quitter le Control Host
```bash
exit
```

## 19. Supprimer toutes les VM de l’atelier
```bash
vagrant destroy -f