# apprentissage_ansible



commande :

ansible --version 
il faut avoir un [default] section comme 

[default]
inventory = ./inventory

sinon la location est celle par défaut : 
/etc/ansible/hosts

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
example : 192.168.[4:7].[0:255] pour dire tous les ips de :
192.168.4....   192.168.6.... 192.168.7.... 192.168.8....
192.168.X.0 192.168.X.1 192.168.X.2 ...... 192.168.X.255

cela marche aussi avec des adress'es
server[1:5].example.com



