# apprentissage_ansible

on peut code en **INI**
            [group1]
            web1.example.com
            web2.example.com

            [group2]
            web2.example.com
            192.0.2.42
            [group_couple:childen]
            groupe1
            groupe2
ou en **yaml**
            all:
              children:
                group_couple:
                  children:
                    group1:
                      hots: 
                        web1.example.com: {}
                        web2.example.com: {}
                   group2:
                      hosts:
                        web2.example.com: {}
                        192.0.2.42: {}

## inventory

inventory file:
            web1.example.com
            web2.example.com
            192.0.2.42

si tu veux grouper, un nom avec _ : 
            [group1]
            web1.example.com
            web2.example.com

            [group2]
            web2.example.com
            192.0.2.42

tu peux faire des groupes de groupes:
avec :
            [group_couple:childen]
            groupe1
            groupe2



2 groupes particuliee
all inclue every host in the inventoryun 
ungrouped unclue tout les hosts qui ne sont dans aucun groupe que all


on peut faire des ranges de hsts  avec [start:end] 
example :
            192.168.[4:7].[0:255] pour dire tous les ips de :
            192.168.4....   192.168.6.... 192.168.7.... 192.168.8....
            192.168.X.0 192.168.X.1 192.168.X.2 ...... 192.168.X.255

cela marche aussi avec des adress'es
            server[1:5].example.com


le fichier de config ansible est :
/etc/ansible/ansible.cfg
voir les valeurs par défaults (par exemple: inventory)


## connection

agentless,pas besoin de custom agent. c'est included.

sudo pour action admin
ssh utilisé (config dans defaults section)

basis section
[default] 
            sets default for ansible operation
[priviliege_escalation] 
            configure how ansible performs privilege escalation on managed hosts

default =>
* remote_user : utilisateur utilisé sur la machine managé
* remote_port : specifié le port sshd utilisé sur la machine managé (défault 22)
* ask_pass : controle will prompt you for ssh password  mais faut mieux key-based auth for ssh

priviliege_escalation => 
* become : boolean   ???  (default is no)
* become_user : quel utilisateur sur la machine ansible sera celui qui donne privilege   (défault is root)
* become_method : control comment ansible devient user (default sudo , mais ça pourrait être juste su)
* become_ask_pass  mot de passe pour become_method (default is no)

typiquement le ansible.cfg file:
            [defaults]
            inventory = ./inventory
            remote_user = ansible
            ask_pass = false

            [priviliege_escalation]
            become = true
            become_user = root
            become_ask_pass = false
  


arborescence HOST-BASED CONNECTION VARIABLE :

        - project 
            - ansible.cfg
            - host_vars
                - server1.example
                - server2.exmple
            - inventory
  
dans host_vars on peut spécifier des variables spécifique

* ansible_host: ip address or hostname utilise pour la connnexion 
* ansible_port : specifie le ssh port pour  ce host
* ansible_user : utilisateur utilisé pour ce host
* ansible_become : utilisateur utilise pour privilege excalation pour  ce host
* ansible_become_method : specifie la method to use privilege escalation pour  ce host (sudo ou su etc...)



exemple :
host_vars/server1.exemple.com contient

            #variable pour server1
            ansible_host: 192.2.36.104
            ansible_port: 34102
            ansible_user: root
            ansbile_become:false

# preparation managed Host

* ssh key based auth 
* specific account only for ansible use





# commande :

### ansible --version 
il faut avoir un [default] section comme 

            [default]
            inventory = ./inventory

sinon la location est celle par défaut : 
/etc/ansible/hosts

# ad hoc commande

débute par ansible  
tu dois spécifié host-pattern
-m pour les options que le module ansible doit lancer
-a pour les arguments obligatoire pour le module

cela donne :  ansible host-pattern -m module -a %module argument% -i inventory

exemple de ad hoc commande :
            * ansible all -m ping
            * ansible -m user -a 'name=newbie uid=4000 state=present'

pour specifier un mdp , on utilise -k ou --ask-pass
pour spécifier un user, on utilise -u remot_user 
pour enable privilege escalation , on utilise -b équivalent become:yes
pour spécifier le mdp de l'escalation, on utilie le grand -K ou --ask-become-pass
pour spécifier la méthode d'escalation, on utilise --become-method  (défault = sudo)





### ansible-inventory -y --list
cela donne le resultat du current inventory en YAML

### ansible-inventory -i inventory --list
donne un fichier json de l'inventory

### ansible %host recherche% --list-hosts
cela permet de recherche le host recherche 
                        ansible web1.example.com --list-hosts


#### -k or --ask-passe 
pour connection mdp

#### -u remote_user 
pour utiliser u user particulier

#### -b 
pour l"equivalend become=true

#### -K o --ask-become-pass
pour passer un password escalation

#### --become-method 
pour passer une methode autre que celle par défaut (sudo )



# documentation

obtenir la liste des docs:
                        * ansible-doc -l 
                        * ansible-doc -l | grep service

pour obtenir la doc précise (exemple : ping) :
                        * ansible-doc -m ping  
                        * ansible-doc -m service
                        * ansible-doc -m user

obtenir les infos de base:
            * ansible --version

voir le fichier de config: 
                        cat ansible.cfg

voir l'inventory :
                        cat inventory

commande ping limité:
                        ansible all --limit web01 -i inventory -k -m ping
cela te donne le ssh password
cela te donne le ping de web01

restart le service sshd :
                        ansible all -m service -a 'state=restart name=sshd'

creer un user :
                        ansible webservers -m user -a 'name=test password=secure_password state=present'

verifier :
                        ssh webservers
                        tail /etc/passwd

on retrouve une ligne pour test


si on veut supprimer un user 
                        ansible webservers -m user -a 'name=test password=secure_password state=absent'

si on reverifie, il a disparu de la liste


