---
layout: post
title: "How to create own vagrant box" 
date: 2018-07-30
categories: virtualization
---
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/vagrantbox.webp?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/vagrantbox.webp?raw=true)

Setelah beberapa kali kita mencoba menggunakan vagrant dari vagrantbox ubuntu/xenial64 yang jika kita sadari, kita menggunakan pure box dari ubuntu, alias masih kosong blong bawaan ubuntu. Mungkin kita ingin beberapa tools yg terinstall pada vm kita, seperti tools build-essential,vim,atau tools-tools keseharina kita yang tidak ada pada OS bawaan ubuntu. 

Untuk mengakali hal itu, kita dapan membuat vagrantbox milik kita sendiri. Dengan tool-tool kesayangan kita, kita seperti dapat membuat OS dengan tools sesuai keinginan kita sendiri. 

Untuk memulainya,buatlah VM dengan OS Ubuntu 18.04 lalu masuk kedalam Virtual Mechine, dan buatlah user baru di vm tersebut.
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/setuser.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/setuser.png?raw=true)
Jika sudah, login ke user tersebut dan cek versi ubuntunya:
```
cat /etc/lsb_release
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/lsb-release.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/lsb-release.png?raw=true)

### Port Forwarding dari Virtualbox
Klik Ubuntu servernya - klik 'Settings' button.
Pada bagian 'Adapter 1' pastikan jenis network 'NAT'.
Klik option 'Advanced' di bawah dan klik 'Port Forwarding'.
Isikan detail Port-Forwarding:
  Name: ssh
  Protocol: TCP
  Host IP: kosongkan
  Host Port: 2222
  Guest IP: kosongkan
  Guest Port: 22
Klik tombol 'Ok', dan 'Ok' lagi untuk save.

![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/set%20nat.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/set%20nat.png?raw=true)

- Install Paket yang dibutuhkan

Nyalankan ubuntu servernya.
Install paket-paket yang dibutuhkan
```
sudo apt update
sudo apt install vim git unzip net-tools wget
```
- Setup Root Password
```
sudo passwd root
```
- Setup user 'vagrant'
```
useradd -m -s /bin/bash vagrant
passwd vagrant
```
Beri password 'vargant'.

- Konfigurasi sudoers vagrant user
```
sudo vim /etc/sudoers.d/vagrant
```
Paste konfigurasi berikut.
```
# add vagrant user
vagrant ALL=(ALL) NOPASSWD:ALL
```
Testing Vagrant user.
```
su - vagrant
sudo su
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/sudo%20su.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/sudo%20su.png?raw=true)

### Konfigurasi key dan SSH

Download vagrant key.
```
mkdir -p /home/vagrant/.ssh
chmod 0700 /home/vagrant/.ssh
wget --no-check-certificate \
          https://raw.github.com/mitchellh/vagrant/master/keys/vagrant.pub \
          -O /home/vagrant/.ssh/authorized_keys
chmod 0600 /home/vagrant/.ssh/authorized_keys
chown -R vagrant /home/vagrant/.ssh
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/vagrant%20pub.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/vagrant%20pub.png?raw=true)

- Konfigurasi ssh login dengan key.
```
cd /etc/ssh/
vim sshd_config
```
Ubah line seperti berikut.
```
AuthorizedKeysFile %h/.ssh/authorized_keys
```

- Update dan upgrade Paket
```
sudo apt update
sudo apt upgrade -y
```

### Installasi Guest Tools

- Install Paket dependensi.
```
export versi=$(uname -r)
sudo apt-get install -y gcc build-essential linux-headers-$versi
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/install%20guest.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/install%20guest.png?raw=true)

- Download ISO VirtualBox Guest Additional ke Komputer Anda

https://download.virtualbox.org/virtualbox/5.2.18/VBoxGuestAdditions_5.2.18.iso

- Mount dan Install VirtualBox guest additon

Klik ubuntu server yang masih 'running', dan klik menu 'Devices'.
Klik 'Insert Guest Addition CD Image..'

Masuk ke ubuntu server dan mount.
```
sudo mount /dev/cdrom /mnt
cd /mnt
sudo ./VBoxLinuxAdditions.run
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/guest%20addition.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/guest%20addition.png?raw=true)

- Clean the Box

```
sudo apt autoremove
sudo apt clean

sudo dd if=/dev/zero of=/EMPTY bs=1M
sudo rm -f /EMPTY

history -c
tekan 'CTRL+d' untuk exit dari terminal.
```

- Packaging Box Vagrant

Balik ke terminal komputer.
```
vagrant package --base zetc0de-ubuntu-vm
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/packaging.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/vagrant-05/packaging.png?raw=true)

- Import Box and Testing

```
vagrant box add ubuntu-zetc0de-1804 package.box

mkdir ~/Vagrant/box-ku/
cd  ~/Vagrant/box-ku/

vagrant init ubuntu-zetc0de-1804
vagrant up
```
Coba ssh vm tersebut.

```
vagrant ssh
```
### Pastikan masuk ke tidak ada error dan bisa masuk ke server dengan perintah tersebut. 