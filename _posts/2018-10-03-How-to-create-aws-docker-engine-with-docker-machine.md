---
layout: post
title: "How to create AWS Docker Engine with Docker Machine"
date: 2018-10-03
categories: virtualization,docker
---

Instalasi Docker tidak akan kita bahas panjang lebar, karena cara dan langkah-langkahnya bisa berubah-ubah seiring waktu. Silahkan lihat dokumentasi di website docker.

Sebaiknya instalasi dilakukan dengan koneksi internet yang mumpuni dan tidak terbatas kuota. Pada saat instalasi, kita akan mendownload image berukuran ratusan MB.

Docker Machine adalah sistem untuk mengelola docker engine. Dia bisa menginstal engine baru di berbagai target. Saat ini dia mendukung VirtualBox, Digital Ocean, dan Amazon. Dengan Docker Machine, kita bisa membuat dan menjalankan container dengan docker client, dan hasilnya container kita berjalan di local (dengan VirtualBox) atau di cloud (dengan DigitalOcean atau Amazon).

### Install docker-machine
```
curl -L https://github.com/docker/machine/releases/download/v0.13.0/docker-machine-`uname -s`-`uname -m` >/tmp/docker-machine && \
chmod +x /tmp/docker-machine && \
sudo cp /tmp/docker-machine /usr/local/bin/docker-machine
```

Setelah instalasi selesai, pastikan kita bisa menjalankan perintah berikut di command line.
```
docker -v
docker-machine -v
```

Dengan kedua aplikasi tersebut, kita sudah bisa bermain-main dengan Docker. Sebagian besar tutorial di internet mengajarkan kita untuk menjalankan docker di laptop kita sendiri. Akan tetapi, saya tidak menganjurkan Anda untuk melakukannya. Proses pembuatan image docker sangat rakus internet. Sekali membuat image, dia akan mendownload beratus-ratus megabytes. Ini tidak masalah kalau koneksi internet kita kencang dan bebas kuota.

Untungnya, ada solusi yang lebih quota-friendly, yaitu menggunakan layanan cloud. Dengan menjalankan Docker Machine di cloud, kita menggunakan koneksi internet server virtual kita untuk membuat image. Sedangkan kuota yang kita gunakan hanya sebatas untuk koneksi SSH ke server kita.

Docker mendukung berbagai cloud services, diantaranya:

- Amazon Web Services
- Microsoft Azure
- Digital Ocean
- dan sebagainya

Kali ini kita akan gunakan cloud provider AWS yang ada trialnya.

```
docker-machine create --driver amazonec2 --amazonec2-region "us-east-1" coba-aws
```
Masuk ke AWS console maka akan terlihat instance baru yang telah kita buat tadi:


Cek apakah docker-machine kita sudah aktive atau belum:
```
docker-machine ls
```
Tandanya jika docker-machine kita aktive adalah ditandai dengan asterisk(\*), atau coba cek dengan perintah:
```
docker-machine active
```

Jika belum active, maka aktive kan dengan perintah:
```
docker-machine env coba-aws
eval $(docker-machine env coba-aws)
```
Cek kembali apakah sudah aktive dengan perintah `docker-machine active`

Jika telah active, kita coba menjalankan `hello-world` pada docker engine kita.
```
docker run hello-world
```

Maka ketika kita cek di sisi server akkan terlihat container hello-world sedang dalam keadaan `running`.



