# Ansible 


    all:
        vars:
            ansible_user: centos
            ansible_ssh_private_key_file: ../../../id_rsa

Cette section définit des variables globales. 
On indique le chemin d'accès vers la clé privé SSH à utiliser pour l'authentification. 

        children:
            prod:
                hosts: arno.bruno.takima.cloud

Ici on indique l'hôte "arno.bruno.takima.cloud" dans le groupe "prod". 

#### Commandes de base : 
`ansible` permet l'exécution de commandes sur des hôtes distants.

`ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"`
Par exemple cette commande exécute setup.yml sur tous les hôtes répertoriés. 

#### Playbooks

    - hosts: all

Spécifie le groupe d'hôtes sur lesquels le playbook est exécuté. 

    gather_facts: false

désactive la collecte des facts

    become: true

Indique à Ansible d'acquérir des droits administrateurs pour certaines actions. 

    # Install Docker
    roles:
        - docker
        - network
        - db
        - backend
        - httpd

La section "roles" spécifie les rôles Ansible à exécuter. 

#### Docker role documentation 

Installation du package `device-mapper-persistent-data` en utilisant le module `yum`. Ce package est utilisé pour la gestion du stockage (gestion des volumes logiques). 

    - name: Install device-mapper-persistent-data
        yum:
            name: device-mapper-persistent-data
            state: latest

Installation du package `lvm2`, complémentaire au package `device-mapper-persistent-data` :  

    - name: Install lvm2
        yum:
            name: lvm2
            state: latest

Execution d'une commande shell pour ajouter le repo git au container : 

    - name: add repo docker
        command:
            cmd: sudo yum-config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

Installation du package `docker-ce` :

    - name: Install Docker
        yum:
            name: docker-ce
            state: present

On s'assure que le service Docker est démarré : 

    - name: Make sure Docker is running
        service: name=docker state=started
            tags: docker
