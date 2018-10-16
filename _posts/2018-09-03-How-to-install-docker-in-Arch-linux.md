---
layout: post
title: "How to install docker in Arch Linux"
date: 2018-09-03
categories: virtualization
---

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

### Start Docker
Sebelum memulai docker, anda harus start service docker terlebih dahulu, dan pastikan docker dalam kondisi running:
```
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker
```

Jika sudah running, kita coba jalankan `docker info` dan didapatkan:

Artinya docker hanya dapat berjalan dalam kondisi root. Untuk menjalankan docker dalam mode user biasa. maka kita harus menambahkan user tersebut kedalam docker group:
```
sudo su
gpasswd -a user docker
```
`Note: user diganti dengan username anda`

Jangan lupa untuk re-login untuk apply perubahan.

Maka ketika kita akses `docker info` dengan user biasa akan mendapatkan result seperti ini:


### Using docker on Arch Linux
Coobalah dengan menjalankan perintah-perintah docker, anda dapat melihatnya dengan perintah:
```
docker -h
```
Akan ada banyak command docker, salah satunya adalah `docker pull`, docker pull digunakan untuk mendownload docker images dari registry ke client:
```
docker pull ubuntu:18.04
```

Perintah diatas akan mendownload images ubuntu dengan tag 18.04, anda dapat melihat berbagai images di [https://hub.docker.com](Docker Hub).



