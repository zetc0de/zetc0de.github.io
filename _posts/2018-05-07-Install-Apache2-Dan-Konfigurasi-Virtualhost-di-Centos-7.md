---
layout: post
title: "Install Apache2 Dan Konfigurasi Virtualhost di Centos 7"
date: 2018-05-07 01:30
categories: webserver
---
<div align="center">
    <img src="https://upload.wikimedia.org/wikipedia/commons/4/45/Apache_HTTP_server_logo_%282016%29.png">
</div>

#INTRODUCTION

Server HTTP Apache atau Server Web/WWW Apache adalah server web yang dapat dijalankan di banyak sistem operasi (Unix, BSD, Linux, Microsoft Windows dan Novell Netware serta platform lainnya) yang berguna untuk melayani dan memfungsikan situs web. Protokol yang digunakan untuk melayani fasilitas web/www ini menggunakan HTTP.

Apache memiliki fitur-fitur canggih seperti pesan kesalahan yang dapat dikonfigur, autentikasi berbasis basis data dan lain-lain. Apache juga didukung oleh sejumlah antarmuka pengguna berbasis grafik (GUI) yang memungkinkan penanganan server menjadi mudah.

Apache merupakan perangkat lunak sumber terbuka dikembangkan oleh komunitas terbuka yang terdiri dari pengembang-pengembang di bawah naungan [Apache Software Foundation](https://id.wikipedia.org/wiki/Apache_Software_Foundation).

#Installation 

`Rencananya saya akan menginstall webapp pada subdomain blog.serverku.com`

Apache2 secara default tersedia di package Centos7, jadi tinggal install menggunakan perintah:
`yum -y install httpd`

Jika istallasi selesai, konfigurasi sistem anda agar menjalankan service apache2/httpd ini saat boot:
`systemctl start httpd`
`systemctl enable httpd`

Pada Centos7 saya menggunakan Firewalld, sehinggak kita harus mengatur agar membolehkan koneksi dari luar ke port 80(http) dan 443(https):

`firewall-cmd --add-service=http --permanent --zone=public`
`firewall-cmd --add-service=https --permanent --zone=public`
`firewall-cmd --reload`

Karena saya ingin mengakses webapp nya pada alamat blog.serverku.com, maka buat virtualhost yang mengarahkan kealamat tersebut.

`mkdir -p /var/www/blog.serverku.com/public_html`

Sekarang kita punya struktur directory untuk file kita, tapi owner nya masih root. Jika kita ingin webapp juga boleh memodifikasi, maka kita harus izinkan user apache untuk mengaksesnya:

`chown -R apache:apache /var/www/blog.serverku.com/public_html`
`chmod -R 755 /var/www`

#Buat Virtualhost

Untuk memulai, kita perlu mengatur directory tempat virtualhost kita akan disimpan,serta directory yang akan memberi tahu apache bahwa virtualhost siap melayani pengunjung. Directori `sites-available` akan menyimpan semua file konfigurasi virtualhost, sementara directory `sites-enable` akan menyimpan tautan simbolic ke host virtual yang ingin kita publikasikan, dalam kasus ini kita akan mempublikasikan host blog.serverku.com.

`mkdir /etc/httpd/sites-available`
`mkdir /etc/httpd/sites-enabled`

Sekarang kita beritahu ke Apache bahwa file-file konfigurasi virtualhost ada didalam directory `site-enabled`:

`nano /etc/httpd/conf/httpd.conf`

Dan berikan baris perintah berikut pada baris terakhir:
`IncludeOptional sites-enabled/*.conf`

#Konfigurasi Virtualhost

Buat file konfigurasinya :
`nano /etc/httpd/sites-available/blog.serverku.conf`

{% highlight ruby %}
<VirtualHost *:80>
     ServerAdmin havidzc0de@gmail.com
     DocumentRoot /var/www/blog.serverku.com/public_html
     ServerName www.serverku.com
     ErrorLog /var/log/httpd/blog.serverku.com_error.log
     CustomLog /var/log/httpd/blog.serverku.com_requests.log combine
</VirtualHost>
{% endhighlight ruby%}

Jika sudah, sekarang aktifkan virtualhost tersebut:

`ln -s /etc/httpd/sites-available/blog.serverku.conf /etc/httpd/sites-enabled/blog.serverku.conf`

Sekarang kita coba buat file html di dalam documentroot kita:
`nano var/www/blog.serverku.com/public_html/index.html`

{% highlight ruby %}
<!DOCTYPE html>
<html>
<head>
    <title>Belajar Web Server</title>
</head>
<body>
<div class="container" align="center">
    <h1>Hello World!</h1>
    <hr>
    <p>Site powered by me ^_^</p>
</div>
</body>
</html>
{% endhighlight ruby%}

Restart apache:
`systemctl restart httpd`

