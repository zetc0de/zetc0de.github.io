---
layout: post
title: "Bash Provisioning With Vagrant" 
date: 2018-06-24 10:35
categories: virtualization
---
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/Vagrant-03/bash.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/Vagrant-03/bash.png?raw=true)

Sebagai DevOps, kita tidak terlepas dengan yang namanya **Provisioning**, saya tidak akan menjelaskan lebih detail makna provisioning itu sendiri, tapi disini kita akan mencoba bash provisioning pada Virtual Mechine menggunakan Vagrant. Langsung saja, siapkan 1 vm atau lebih mengguanan vagrant. 
```
mkdir -p ~/Vagrant/vagrant-provbash
cd ~/Vagrant/vagrant-provbash
```
Lalu buatlah sebuah vagrantfile dengan config:
```
# Inisialisasi Vagrantfile configuration
Vagrant.configure("2") do |config|

  # definisikan vm01
  config.vm.define "vm01" do |vm01|
      vm01.vm.box = "ubuntu/xenial64"
      vm01.vm.hostname = "serverku"
      vm01.vm.network :private_network, ip: "10.3.3.15"
      vm01.vm.provision "shell", path: "lamp.sh"
      vm01.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
      end
      vm01.vm.network "forwarded_port", guest: 80, host:3000
  end

end
```
**Penjelasan :**
- Menggunakan box ubuntu/xenial64
- Dengan hostname **serverku**
- Mengguanakn private network dengan ip 10.3.3.15
- Menggunakan provision **bash**, ada banyak opsi untuk provision, kita akan mengguanakn bash pada case ini. 
- Path : Tempat script bash berada.
- Dengan provider virtualbox, RAM 512 MB.
- Port fordward ke host 3000.
- Anda dapat menambah vm sendiri, tinggal copas config yg ada dengan nama vm yg berbeda, misal vm02,vm03,dst.

Pada case kali ini kita akan coba provisioning LAMP STACK menggunakan bash. Buatlah file `lamp.sh` pada directory yg sama, isikan script berikut:
```
#!/bin/bash

export DEBIAN_FRONTEND="noninteractive"
password="toor"
sudo su
apt update
debconf-set-selections <<< "mysql-server mysql-server/root_password password $password"
debconf-set-selections <<< "mysql-server mysql-server/root_password_again password $password"
apt install -y apache2 mysql-server libapache2-mod-fastcgi php7.0-fpm php7.0 
echo "<?php phpinfo(); ?>" > /var/www/html/index.php
a2enmod actions

rm /etc/apache2/sites-available/000-default.conf 
wget https://gist.githubusercontent.com/zetc0de/b0e54218db3f77fc328218f3a20894f1/raw/73a13cd240e3630edaf57975c36737ed5f29e832/000-default.conf -O /etc/apache2/sites-available/000-default.conf ufw allow http

systemctl restart apache2
systemctl restart mysql 
echo -e "-------------------------------"
echo -e "-[ https://zetc0de.github.io ]-"
echo -e "-------------------------------"
```
Jangan lupa untuk memberi hak akses execute pada filenya :
```
chmod +x lamp.sh
```
Lalu jalankan vm01 dan pastikan vm01 telah running dengan perintah:
```
vagrant up vm01
vagrant status vm01
```
Kemudian saatnya provisioning, gunakan perintah:
```
vagrant provisioning vm01
```
Maka akan terlihat proses provisioning vm01 dengan script bash. Dengan provisioning kita dimudahkan untuk mensetup banyak server dengan sekali kerja. Bayangkan jika anda diminta untuk mensetup 1000 server? Menarik bukan? :)

Anda dapat melihat hasil dari provisioning tadi dengan mengakses 127.0.0.1:3000 di browser anda. 

![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/Vagrant-03/result.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/Vagrant-03/result.png?raw=true)

Terlihat apache,mysql,dan php-fpm kita sudah dalam kondisi running. :)




