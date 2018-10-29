# Practica-02-LAMP_VAGRANT
###### Debemos de tener instalado Virtualbox, Vagrant y visual code o similar.

## Creacion de Vagrantfile
Lo primero que tenemos que saber es donde vamos a guardar los archivos de a maquina, dentro de ese directorio:

``vagrant init`` 

Para que se nos cree un archivo llamado vagrantfile donde configuraremos la maquina.
Lo abrimos ``code vagrantfile`` y tenemos que tener la siguiente configuracion.
````
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.network "private_network", ip: "192.168.33.10"
  config.vm.provision "shell", path: "script.sh"
end

````

## Configuracion de *provision*
Creamos un archivo aparte es visual code que se llame ``script.sh`` debe ser la misma ruta que hemos realizado en el paso anterior. Dentro se ese script.sh configuramos:
````
#! /bin/bash
apt-get update

# Instalación de apache
apt-get install -y apache2
cp /vagrant/index.php /var/www/html
apt-get install -y php libapache2-mod-php php-mysql

# Instalación  de las utilidades dconf
apt-get -y install debconf-utils

# Instalación de la contraseña root
DB_ROOT_PASSWD=root
debconf-set-selections <<< "mysql-server mysql-server/root_password password $DB_ROOT_PASSWD"
debconf-set-selections <<< "mysql-server mysql-server/root_password_again password $DB_ROOT_PASSWD"

# Instalación de mysql
apt-get install -y mysql-server

# Clonar el repositorio de la aplicacion web
apt-get install -y git
rm -f /var/www/html/index.html
cd /tmp
git clone https://github.com/josejuansanchez/iaw-practica-lamp.git
cp iaw-practica-lamp/src/. /var/www/html/ -R
cd /var/www/html
chown www-data:www-data * -R 
mysql -u root -p$DB_ROOT_PASSWD < /tmp/iaw-practica-lamp/db/database.sql
````
Encendemos la maquina:
````vagrant up````


Actualizamos:
````vagrant provision````

Accedemos a la maquina:
``vagrant ssh``
