# Formation Ansible - Exercices par Atelier

## Sommaire
- [Ansible par la pratique (3) – Installation](#atelier-01)
- [Ansible par la pratique (4) – Authentification](#atelier-03)
- [Ansible par la pratique (6) – Configuration de base](#atelier-06)
- [Ansible par la pratique (8) – Idempotence](#atelier-07)
- [Ansible par la pratique (10) – Un serveur web simple](#atelier-10)
- [Ansible par la pratique (12) – Handler](#atelier-12)
- [Ansible par la pratique (12) – Variable](#atelier-14)
- [Ansible par la pratique (13) – Variables enregistrées](#atelier-15)
- [Ansible par la pratique (14) – Facts et variables implicites](#atelier-16)


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
```

## ATELIER-07 : Idempotence avec Ansible

### Exercice : Idempotence avec Ansible

## 1. Accéder au répertoire de l'atelier 07
```bash
cd ~/formation-ansible/atelier-07
```

## 2. Démarrer les machines virtuelles
```bash
vagrant up
```

## 3. Se connecter au Control Host
```bash
vagrant ssh ansible
```

## 4. Se placer dans le répertoire du projet Ansible
```bash
cd ansible/projets/ema/
ls
```
On voit bien que les fichiers ansible.cfg et inventory sont présents : 
![alt text](image-4.png)

## 5. Installation des paquets tree, git et nmap sur toutes les cibles
### Première et deuxeieme exécution
```bash
ansible all -m package -a "name=tree state=present"
ansible all -m package -a "name=git state=present"
ansible all -m package -a "name=nmap state=present"
```
### Deuxième exécution
![alt text](image-5.png)
![alt text](image-6.png)
![alt text](image-7.png)

## 6. Désinstallation des paquets
### Première et deuxieme exécution
```bash
ansible all -m package -a "name=tree state=absent"
ansible all -m package -a "name=git state=absent"
ansible all -m package -a "name=nmap state=absent"
```
![alt text](image-8.png)

## 7. Copier le fichier `/etc/fstab` du Control Host vers les Target Hosts
### Première exécution
```bash
ansible all -m copy -a "src=/etc/fstab dest=/tmp/test3.txt mode=0644"
```
![alt text](image-9.png)


## 8. Supprimer le fichier `/tmp/test3.txt` sur les Target Hosts
### Première et duexieme exécution
```bash
ansible all -m file -a "path=/tmp/test3.txt state=absent"
```
![alt text](image-10.png)

## 9. Vérifier l'espace utilisé sur la partition principale des Target Hosts
```bash
ansible all -m command -a "df -h /"
```

![alt text](image-11.png)

Ici on remarque que chaque exécution retourne exactement le même résultat pour chaque Target Host. Cela confirme que la commande n’altère pas l’état du système (elle est purement informative)

## 10. Quitter le Control Host
```bash
exit
```

## 11. Supprimer toutes les VM de l’atelier
```bash
vagrant destroy -f
```

## ATELIER-10
### Exercice : Un serveur web simple

## Exercice 1 : 

1. **Démarrer les VMs**  
```bash
cd ~/formation-ansible/atelier-10
vagrant up
```

2. **Connexion au Control Host**
```bash
vagrant ssh ansible
```

3. **Se rendre dans le repertoire du projet**
```bash
cd ansible/projets/ema/
```

4. **Créer un playbook `apache-debian.yml`**
```yml
---  # Apache sur l’hôte Debian
- hosts: debian
  tasks:
    - name: Mettre à jour les caches
      apt:
        update_cache: true
        cache_valid_time: 3600

    - name: Installer Apache sur Debian
      apt:
        name: apache2
        state: present

    - name: Démarrer et activer le service Apache sur Debian
      service:
        name: apache2
        state: started
        enabled: true

    - name: Déployer la page web sur Debian
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Debian Apache</title>
            </head>
            <body>
              <h1>Apache web server running on Debian Linux</h1>
            </body>
          </html>
```

5. **Vérifier la syntaxe du fichier `apache-debian.yml`**
```bash
yamllint apache-debian.yml
```
Aucune sortie, le fichier `apache-debian.yml` est correct

6. **Créer un playbook `apache-rocky.yml`**
```yml
---  # Apache sur l’hôte Rocky
- hosts: rocky
  tasks:
    - name: Installer Apache sur Rocky
      dnf:
        name: httpd
        state: present

    - name: Démarrer et activer le service Apache sur Rocky
      service:
        name: httpd
        state: started
        enabled: true

    - name: Déployer la page web sur Rocky
      copy:
        dest: /var/www/html/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>Rocky Apache</title>
            </head>
            <body>
              <h1>Apache web server running on Rocky Linux</h1>
            </body>
          </html>
```

7. **Vérifier la syntaxe du fichier `apache-rocky.yml`**
```bash
yamllint apache-rocky.yml
```
Aucune sortie, le fichier `apache-rocky.yml` est correct

8. **Créer un playbook `apache-suse.yml`**
```yml
---  # Apache sur l’hôte SUSE
- hosts: suse
  tasks:
    - name: Installer Apache sur SUSE
      zypper:
        name: apache2
        state: present

    - name: Démarrer et activer le service Apache sur SUSE
      service:
        name: apache2
        state: started
        enabled: true

    - name: Déployer la page web sur SUSE
      copy:
        dest: /srv/www/htdocs/index.html
        mode: 0644
        content: |
          <!doctype html>
          <html>
            <head>
              <meta charset="utf-8">
              <title>SUSE Apache</title>
            </head>
            <body>
              <h1>Apache web server running on SUSE Linux</h1>
            </body>
          </html>
```

9. **Vérifier la syntaxe du fichier `apache-suse.yml`**
```bash
yamllint apache-suse.yml
```
Aucune sortie, le fichier `apache-suse.yml` est correct

10. **Tester la connectivité**
```bash
ansible debian -m ping
debian | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

ansible rocky -m ping
rocky | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

ansible suse -m ping
suse | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
![alt text](image-12.png)

11. **Exécuter le playbook `apache-debian.yml`**

```bash
ansible-playbook apache-debian.yml
```
![alt text](image-13.png)

12. **Exécuter le playbook `apache-rocky.yml`**

```bash
ansible-playbook apache-rocky.yml
```
![alt text](image-14.png)

13. **Exécuter le playbook `apache-suse.yml`**

```bash
ansible-playbook apache-suse.yml
```
![alt text](image-15.png)

14. **Vérifier les résultats**
```
curl debian
curl rocky
curl suse
```

![alt text](image-16.png)

## ATELIER-12

### Exercice : Handler

1. **Démarrer les VMs**  
```bash
cd ~/formation-ansible/atelier-12
vagrant up
```

2. **Connexion au Control Host**
```bash
vagrant ssh control
```

3. **Création d'un playbook `chrony.yml`**
```bash
cd /ansible/projet/ema/playbooks
nano chrony.yml
```
```yml
---  # Assure la synchronisation NTP
- hosts: redhat
  become: true
  tasks:
    - name: Installe le package chrony
      dnf:
        name: chrony
        state: present

    - name: Démarre et active le deamon chronyd
      service:
        name: chronyd
        state: started
        enabled: true

    - name: Déploiement de la configuration chrony
      copy:
        dest: /etc/chrony.conf
        mode: '0644'
        content: |
          # /etc/chrony.conf
          server 0.fr.pool.ntp.org iburst
          server 1.fr.pool.ntp.org iburst
          server 2.fr.pool.ntp.org iburst
          server 3.fr.pool.ntp.org iburst
          driftfile /var/lib/chrony/drift
          makestep 1.0 3
          rtcsync
          logdir /var/log/chrony
      notify: Restart chronyd

  handlers:
    - name: Restart chronyd
      service:
        name: chronyd
        state: restarted
```

5. **Vérifier la syntaxe du fichier `chrony.yml`**
```bash
yamllint chrony.yml
```
Aucune sortie, le fichier `chrony.yml` est correct

6. **Exécuter le playbook `chrony.yml`**
```bash
ansible-playbook chrony.yml
```

![alt text](image-17.png)

7. **Vérifier l'idempotence** \
On peut relancer le playbook pour s'assurer qu’aucun changement n’est appliqué si la configuration est déjà en place, si ça indique "ok", c'est que rien n'a changé
```bash
ansible-playbook chrony.yml
```

![alt text](image-18.png)

8. **Vérifier l'état des services** \
On peut aussi verifier l'état des services 
```bash
ansible redhat -a "systemctl status chronyd"

target03 | CHANGED | rc=0 >>
● chronyd.service - NTP client/server
     Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; preset: enabled)
     Active: active (running) since Mon 2025-02-17 10:58:23 UTC; 7min ago
       Docs: man:chronyd(8)
             man:chrony.conf(5)
    Process: 5981 ExecStart=/usr/sbin/chronyd $OPTIONS (code=exited, status=0/SUCCESS)
   Main PID: 5983 (chronyd)
      Tasks: 1 (limit: 5822)
     Memory: 944.0K
        CPU: 49ms
     CGroup: /system.slice/chronyd.service
             └─5983 /usr/sbin/chronyd -F 2

target02 | CHANGED | rc=0 >>
● chronyd.service - NTP client/server
     Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; preset: enabled)
     Active: active (running) since Mon 2025-02-17 10:58:22 UTC; 7min ago
       Docs: man:chronyd(8)
             man:chrony.conf(5)
    Process: 5986 ExecStart=/usr/sbin/chronyd $OPTIONS (code=exited, status=0/SUCCESS)
   Main PID: 5988 (chronyd)
      Tasks: 1 (limit: 5822)
     Memory: 952.0K
        CPU: 47ms
     CGroup: /system.slice/chronyd.service
             └─5988 /usr/sbin/chronyd -F 2

target01 | CHANGED | rc=0 >>
● chronyd.service - NTP client/server
     Loaded: loaded (/usr/lib/systemd/system/chronyd.service; enabled; preset: enabled)
     Active: active (running) since Mon 2025-02-17 10:58:22 UTC; 7min ago
       Docs: man:chronyd(8)
             man:chrony.conf(5)
    Process: 5984 ExecStart=/usr/sbin/chronyd $OPTIONS (code=exited, status=0/SUCCESS)
   Main PID: 5986 (chronyd)
      Tasks: 1 (limit: 5822)
     Memory: 948.0K
        CPU: 48ms
     CGroup: /system.slice/chronyd.service
             └─5986 /usr/sbin/chronyd -F 2
```

9. **Quitter et supprimer**
```bash
exit
vagrant destroy -f
```

## ATELIER-14
### Exercice : Variable

1. **Démarrer les VMs**  
```bash
cd ~/formation-ansible/atelier-14
vagrant up
```

2. **Connexion au Control Host**
```bash
vagrant ssh control
```

3. **Se rendre dans le repertoire du projet**
```bash
cd ansible/projets/ema/
```

4. **Création d'un playbook (play vars)**
```bash
nano playbooks/myvars1.yml
```
```yml
---
- hosts: all
  gather_facts: false
  vars:
    mycar: "Volkswagen Polo II"
    mybike: "Harley-Davidson"
  tasks:
    - name: Afficher ma voiture et ma moto préférées (play vars)
      debug:
        msg: "J'adore la voiture: {{ mycar }}, et la moto: {{ mybike }}"
```

5. **Vérifier la syntaxe du fichier `myvars1.yml`**
```bash
yamllint myvars1.yml
```
Aucune sortie, le fichier `myvars1.yml` est correct

6. **Exécuter le playbook**
```bash
ansible-playbook myvars1.yml
```
![alt text](image-19.png)

7. **Exécuter le playbook en précisant `mycar`**
```bash
ansible-playbook myvars1.yml -e mycar="Alpine"
```
![alt text](image-20.png)

8. **Exécuter le playbook en précisant `mybike`**
```bash
ansible-playbook myvars1.yml -e mybike="Suzuki"
```
![alt text](image-21.png)

9. **Exécuter le playbook en précisant `mycar` et `mybike`**
```bash
ansible-playbook myvars1.yml -e mycar="Rolls-Royce" -e mybike="Yamaha" 
```
![alt text](image-22.png)

10. **Création d'un playbook (set_fact)**
```bash
nano playbooks/myvars2.yml
```
```yml
---
- hosts: all
  gather_facts: false
  tasks:
    - name: Définir la voiture et la moto préférées avec set_fact
      set_fact:
        mycar: "Volkswagen Polo II"
        mybike: "Harley-Davidson"
    - name: Afficher ma voiture et ma moto préférées (set_fact)
      debug:
        msg: "J'adore la voiture: {{ mycar }}, et la moto: {{ mybike }}"
```

11. **Vérifier la syntaxe du fichier `myvars2.yml`**
```bash
yamllint myvars2.yml
```
Aucune sortie, le fichier `myvars2.yml` est correct

12. **Exécuter le playbook**
```bash
ansible-playbook myvars2.yml
```
![alt text](image-23.png)

13. **Exécuter le playbook en précisant `mycar`**
```bash
ansible-playbook myvars2.yml -e mycar="Alpine"
```
![alt text](image-24.png)

14. **Exécuter le playbook en précisant `mybike`**
```bash
ansible-playbook myvars2.yml -e mybike="Suzuki"
```
![alt text](image-25.png)

15. **Exécuter le playbook en précisant `mycar` et `mybike`**
```bash
ansible-playbook myvars2.yml -e mycar="Rolls-Royce" -e mybike="Yamaha" 
```
![alt text](image-26.png)

16. **Créer un dossier `group_vars` dans la racine du projet**
```bash
mkdir -p group_vars
ls

ansible.cfg  group_vars  inventory  playbooks
```

17. **Créer le fichier `group_vars/all.yml`**
```bash
nano group_vars/all.yml
```
```yml
mycar: "VW"
mybike: "BMW"
```

18. **Créer un dossier `host_vars` dans la racine du projet**
```bash
mkdir -p host_vars
ls

ansible.cfg  group_vars  host_vars  inventory  playbooks
```

19. **Créer le fichier `host_vars/target02.yml`**
```bash
nano host_vars/target02.yml
```
```yml
mycar: "Mercedes"
mybike: "Honda"
```

20. **Création d'un playbook (group_vars/host_vars)**
```bash
nano playbooks/myvars3.yml
```
```yml
---
- hosts: all
  gather_facts: false
  tasks:
    - name: Afficher ma voiture et ma moto préférées (group_vars/host_vars)
      debug:
        msg: "J'adore la voiture: {{ mycar }}, et la moto: {{ mybike }}"
```

21. **Vérifier la syntaxe du fichier `myvars3.yml`**
```bash
yamllint myvars3.yml
```
Aucune sortie, le fichier `myvars3.yml` est correct

22. **Exécuter le playbook**
```bash
ansible-playbook myvars3.yml
```
![alt text](image-27.png)

23. **Création d'un playbook (variables interactives)**
```bash
nano playbooks/display_user.yml
```
```yml
---
- hosts: all
  gather_facts: false
  vars_prompt:
    - name: "user"
      prompt: "Entrez votre super nom d'utilisateur"
      default: "microlinux"
      private: false
    - name: "password"
      prompt: "Entrez votre mot de passe sécurisé"
      default: "yatahongaga"
      private: true
  tasks:
    - name: Afficher le nom d'utilisateur et le mot de passe
      debug:
        msg: "Ton super nom d'utilisateur est {{ user }} et ton mot de passe est {{ password }}"
```

24. **Vérifier la syntaxe du fichier `display_user.yml`**
```bash
yamllint display_user.yml
```
```bash
display_user.yml
  16:81     error    line too long (96 > 80 characters)  (line-length)
```


25. **Exécuter le playbook**
```bash
ansible-playbook display_user.yml
```
![alt text](image-28.png)

Si on ne rentre aucunes valeurs : 

![alt text](image-29.png)

26. **Quitter et supprimer**
```bash
exit
vagrant destroy -f
```

## ATELIER-15
### Exercice : Variables enregistrées

1. **Démarrer les VMs**  
```bash
cd ~/formation-ansible/atelier-15
vagrant up
```

2. **Connexion au Control Host**
```bash
vagrant ssh ansible
```

3. **Se rendre dans le repertoire du projet**
```bash
cd ansible/projets/ema/
```
### 2. Playbook `kernel.yml`

Le playbook doit afficher les informations détaillées du noyau de tous les hôtes cibles en utilisant la commande `uname -a`.

```yaml
--- # kernel.yml
- hosts: all
  gather_facts: false

  tasks:
    - name: Afficher les informations du noyau
      command: uname -a
      changed_when: false
      register: kernel_info

    - name: Afficher les informations avec le paramètre msg
      debug:
        msg: "Noyau : {{ kernel_info.stdout }}"

    - name: Afficher les informations avec le paramètre var
      debug:
        var: kernel_info.stdout_lines
```
1. **Vérifier la syntaxe du fichier `kernel.yml`**
```bash
yamllint kernel.yml
```
### Exécution :
```bash
ansible-playbook kernel.yml
```


![alt text](image-30.png)


### 3. Playbook `packages.yml`

Le playbook doit afficher le nombre total de paquets RPM installés sur les hôtes `rocky` et `suse`.

```yaml
--- # packages.yml
- hosts: rocky,suse
  gather_facts: false

  tasks:
    - name: Compter le nombre total de paquets RPM installés
      shell: rpm -qa | wc -l
      changed_when: false
      register: rpm_count

    - name: Afficher le nombre total de paquets
      debug:
        msg: "Nombre total de paquets RPM : {{ rpm_count.stdout | trim }}"
```
1. **Vérifier la syntaxe du fichier `kernel.yml`**
```bash
yamllint kernel.yml
```
### Exécution :
```bash
ansible-playbook packages.yml
```
![alt text](image-31.png)

## 4. Quitter le Control Host
```bash
exit
```

## 5. Supprimer toutes les machines virtuelles
```bash
vagrant destroy -f
```

## ATELIER-16
### Exercice : Facts et variables implicites
1. **Démarrer les VMs**  
```bash
cd ~/formation-ansible/atelier-16
vagrant up
```

2. **Connexion au Control Host**
```bash
vagrant ssh ansible
```

3. **Se rendre dans le repertoire du projet**
```bash
cd ansible/projets/ema/
```