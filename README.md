# With Ansible
## [Vagrant - Ubuntu-bionic 64 - Apache - PHP - Composer - Mariadb - nodejs - yarn]

La machine invitée contiendra :

* Ansible latest
* Apache2
* Mariadb 10.4
* PHP 7.2
* Composer latest
* nodejs 12.16
* yarn latest
* adminer latest

### 1 - Vérification

* Choisir le provider de gestion de machine virtuelle.
[Liste des providers](https://www.vagrantup.com/docs/providers/)

* Vérifier quelle version du provider est compatible avec la version de vagrant.
[Exemple avec VirtualBox](https://www.vagrantup.com/docs/virtualbox/)

### 2 - Installer votre provider
* [VirtualBox](https://www.virtualbox.org/)
* [VMware](https://www.vmware.com/)
* [Docker](https://www.docker.com/)
* [Hyper-v](https://docs.microsoft.com/fr-fr/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)

### 3 - Installer Vagrant
* [Vagrant](https://www.vagrantup.com/)

### 4 - Dossier projet
* Sur votre ordinateur créer le dossier qui va contenir votre projet de développement et la configuration de la machine virtuelle.

<img height="70" alt="creation_sossier" src="https://user-images.githubusercontent.com/26669933/74859827-f36cd200-5347-11ea-8b3d-e378526c2387.png">

### 5 - Initialisation de la machine vagrant
* Dans votre terminal placez-vous dans le dossier nouvellement créé
* lancez la commande  `vagrant init ubuntu/bionic64`
 
 Un fichier Vagrantfile sera créé dans le dossier du projet.
 
 
<img height="72" alt="vagrant_file" src="https://user-images.githubusercontent.com/26669933/74860597-37aca200-5349-11ea-9cde-e2ac4fd90485.png">

### 6 Configuration de la machnine virtuelle
* Ouvrir le fichier Vagrantfile.
* Décommenter la ligne  `config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"` et ajouter owner: "vagrant", group: "www-data" comme l'exemple ci-dessous :

`config.vm.network "forwarded_port", guest: 80, host: 8080, owner: "vagrant", group: "www-data"`

 
* Décommenter la ligne  `config.vm.synced_folder "../data", "/vagrant_data"` et modifier la ligne par les valeurs suivantes : `config.vm.synced_folder "./", "/var/www/html"` pour faire un lien symbolique entre le dossier du projet de la machine hôte avec le dossier html de la machine invitée.
* Remplacer le code 
 
   ```
   # config.vm.provider "virtualbox" do |vb|
   #   Display the VirtualBox GUI when booting the machine
   #   vb.gui = true
   #
   #   # Customize the amount of memory on the VM:
   #   vb.memory = "1024"
   # end
   ```
   par (pour allouer 2Go de mémmoire à la machine virtuelle vous pouvez bien sûre adapter la valeur à votre machine hôte)
   
 ```
   config.vm.provider "virtualbox" do |vb|
     #   # Display the VirtualBox GUI when booting the machine
     #   vb.gui = true
     #
     #   # Customize the amount of memory on the VM:
        vb.memory = "2048"
    end   
```

* Remplacer le code 
```
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
```

par (Ce code sera exécuter à l'installation de la machine pour installé toutes les technos nécessaire à votre projet Website Symfony)

```
config.vm.provision "shell", inline: <<-SHELL

     ip="127.0.0.1"
     pwddb="mot_de_passe_root_mariadb"
    
     apt update
     apt upgrade -y
     apt install -y python-software-properties
     apt update
     apt upgrade -y
     apt-get update
     apt-get install -y software-properties-common
     apt-add-repository --yes --update ppa:ansible/ansible
     apt-get -y install ansible
     echo "${ip}" >> /etc/ansible/host

     git clone https://github.com/Keolite/vagrant_ubuntu_bionic_lamp_72_composer_yarn.git /home/vagrant/ansible
     ansible-playbook /home/vagrant/ansible/general.yml -i "${ip}," -v -c local -u root --extra-vars "pwddb=${pwddb} "
    SHELL
```

### 7 Démarrage de la machine et installation des technos (Provision)
* Démarrer la machine virtuelle avec l'option de 'provision'
`vagrant up --provision`

### 8 Utiliser la machine virtuelle
* Pour accéder à la base de données saisir l'url `adminer.localhost:8080`

* Pour accéder à l'application saisir l'url `localhost:8080`

* Accéder à la machine invitée avec la commande `vagrant ssh`

<img width="500" alt="vagrantsshpng" src="https://user-images.githubusercontent.com/26669933/75088623-09e57a00-5550-11ea-8960-c96379511ec6.png">

* Se positionner dans le dossier html avec la commande `cd /var/www/html`