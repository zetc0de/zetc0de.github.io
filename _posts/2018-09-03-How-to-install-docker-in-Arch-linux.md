---
layout: post
title: "How to install docker in Arch Linux"
date: 2018-09-03
categories: virtualization
---
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker.png?raw=true)

## Introduction

Docker adalah salah satu platform yang dibangun berdasarkan teknologi container. Docker merupakan sebuah project open-source yang menyediakan platform terbuka untuk developer maupun sysadmin untuk membangun,mengemas,dan menjalankan aplikasi dimanapun sebagai sebuah wadah(container) yang ringan. Dengan sangat populernya docker, sebagian orang sering menganggap docker adalah sebutan lain dari container. (codepolitan)

## Installasi
- Enable the loop module
- Install docker
- Start and Enable Docker
- Using docker on Arch Linux

### Enable The Loop Module
Cek module loop:
```
sudo su
lsmod | grep loop
```
Jika tidak ada result apapun artinya module loop belum terload,mari kita enable module loop:
```
tee /etc/modules-load.d/loop.conf <<< "loop"
modprobe loop
```

Cek kembali dan pastikan module loop sudah enabled.

### Install docker
Anda dapat menginstall docker melalui repository comunity (AUR), biasanya bernama `docker-git`, tapi disini saya akan install melalui repository arch:
```
sudo pacman -S docker
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/install.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/install.png?raw=true)

### Start Docker
Sebelum memulai docker, anda harus start service docker terlebih dahulu, dan pastikan docker dalam kondisi running:
```
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/status%20docker.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/status%20docker.png?raw=true)

Jika sudah running, kita coba jalankan `docker info` dan didapatkan:
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker%20info.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker%20info.png?raw=true)

Artinya docker hanya dapat berjalan dalam kondisi root. Untuk menjalankan docker dalam mode user biasa. maka kita harus menambahkan user tersebut kedalam docker group:
```
sudo su
gpasswd -a user docker
```
`Note: user diganti dengan username anda`
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker%20group.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker%20group.png?raw=true)

Jangan lupa untuk re-login untuk apply perubahan.

Maka ketika kita akses `docker info` dengan user biasa akan mendapatkan result seperti ini:
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker%20info2.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker%20info2.png?raw=true)

### Using docker on Arch Linux
Coobalah dengan menjalankan perintah-perintah docker, anda dapat melihatnya dengan perintah:
```
docker -h
```
Akan ada banyak command docker, salah satunya adalah `docker pull`, docker pull digunakan untuk mendownload docker images dari registry ke client:
```
docker pull ubuntu:18.04
```
![https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker%20pull.png?raw=true](https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/docker-01/docker%20pull.png?raw=true)

Perintah diatas akan mendownload images ubuntu dengan tag 18.04, anda dapat melihat berbagai images di [https://hub.docker.com](Docker Hub).



