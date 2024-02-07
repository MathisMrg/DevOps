# Ansible

ansible all -i inventories/setup.yml -m ping
![alt text](img/a.png)

ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
![alt text](img/image.png)

ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become
![alt text](img/image-1.png)

### Question : 3-1 Document your inventory and base commands

- (cf. ansible/inventories/setup.yml)

ansible-playbook -i inventories/setup.yml playbook.yml
![alt text](img/image-2.png)

### Install Docker with playbook
![alt text](img/image-3.png)

ansible-galaxy init roles/docker
- Role roles/docker was created successfully

### Question : 3-2 Document your playbook
- (cf. ansible/playbook.yml)

### Call the docker role from your playbook to check your refactor and your installation.
- On va dans le role qui a ete creer et on deplace la task dans le folder tasks du role
- Dans le playbook on appel le role
- On relance la commande playbook
![alt text](img/image-4.png)

### Creer tt les roles
ansible-galaxy init roles/app
ansible-galaxy init roles/database
ansible-galaxy init roles/proxy
ansible-galaxy init roles/network

### Question : Document your docker_container tasks configuration.

//TODO

### Bonus: Front
ansible-galaxy init roles/front
Ajouter le zib du front, modifier le httpd conf pour acceder à l'api et au front
modfier le env du front pour qu'il redirige vers l'api definit dans httpd
build l'image
creer un role dans le playbook
mettre à jour

### Bonus CI ansible deploy
creer un dockerfile avec ansible
ansible-vault encrypt ./var.yml
ansible-playbook -i inventories/setup.yml playbook.yml --ask-vault-pass
ansible-galaxy init roles/ansible_container