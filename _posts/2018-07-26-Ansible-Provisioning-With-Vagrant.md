---
layout: post
title: "Ansible Provisioning With Vagrant" 
date: 2018-07-26
categories: virtualization
---
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-04/ansible-vagrant.jpg?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-04/ansible-vagrant.jpg?raw=true)

Melanjutkan belajar provisioning menggunakan Vagrant, mulailah dengan memuat vagrantfile:
```
  config.vm.define "vm02" do |vm02|
      vm02.vm.box = "ubuntu/xenial64"
      vm02.vm.hostname = "vm02"
      vm02.vm.network :privat_enetwork, ip: "10.3.3.11"
      vm02.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
      end
      vm02.vm.network "forwarded_port", guest: 80, host:8002
      vm02.vm.provision "ansible" do |x|
	x.verbose = "v"
	x.playbook = "provision/playbook.yml"
      end
  end
```
**Penjelasan :**
- vm02 : Nama vm kita
- vm02.vm.box : Menggunakan box ubuntu/xenial64
- vm02.vm.hostname : Untuk setting hostname
- vm02.vm.network : Setting network dengan ip private dan prot forwarding.
- vm02.vm.provision : Provisioning menggunakna ansible.

Download file provisioning ansible [disini](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/files/provision.zip?raw=true), lalu extrak dan letakkan pada directory yang sama dengan vagrantfiile. Kemudian jalankan vm02 dengan perintah `vagrant up`.

![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-04/cek%20status.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-04/cek%20status.png?raw=true)

Pastikan vm02 dalam kondisi running, untuk provisioning kali ini kita akan coba install webserver apache dan build web dengan jinja2, silahkan oprek ansiblenya dan disesuaikan dengan kebutuhan. Untuk membuktikan hasil proovisioningnya, editlah variable nama pada file `provision/roles/apache/vars/main.yml` dengan nama anda. Untuk menjalankan provisioningnya gunakan perintah:
```
vagrant provision vm02
``` 
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-04/provisioning.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-04/provisioning.png?raw=true)

Dari provisioning diatas kita akan mendapatkan result sebagai berikut:
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-04/result.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-04/result.png?raw=true)