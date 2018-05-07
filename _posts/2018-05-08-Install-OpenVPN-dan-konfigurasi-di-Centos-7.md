---
layout: post
title: "Install OpenVPN dan Konfigurasi di Centos 7"
date: 2018-04-15 23:52
categories: OpenVPN
---

<div align="center">
    <img src="https://upload.wikimedia.org/wikipedia/commons/8/88/Ovpntech_logo-s_REVISED.png">
</div>

###Introduction
OpenVPN adalah aplikasi perangkat lunak open-source yang mengimplementasikan teknik virtual private network [(VPN)](https://en.wikipedia.org/wiki/Virtual_private_network) untuk membuat koneksi point-to-point atau site-to-site yang aman dalam konfigurasi yang diarahkan atau dijembatani dan fasilitas akses jarak jauh. Ini menggunakan protokol keamanan khusus [9] yang memanfaatkan [SSL / TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security) untuk pertukaran kunci. OpenVPN mampu melintasi [network address translators](https://en.wikipedia.org/wiki/Network_address_translator) (NAT) dan [firewall](https://en.wikipedia.org/wiki/Firewall_(computing)). OpenVPN ditulis oleh James Yonan dan diterbitkan di bawah GNU General Public License (GPL). 

###Step by step
- Enable the epel-repository in CentOS.
- Install openvpn and easy-rsa.
- Create Vars Variables.
- Configure easy-rsa.
- Configure OpenVPN.
- Configure Firewalld and Enable Port Forwarding.
- Start openVPN Server.
- Setting up the OpenVPN client application.

###Enable the epel-repository in CentOS.
```
yum install epel-release
```

###Install openvpn and easy-rsa.
```
yum -y install openvpn easy-rsa 
```

###Create Vars Variables
```
cd /etc/openvpn/easy-rsa/3/
vim vars
```

```
set_var EASYRSA                 "$PWD"
set_var EASYRSA_PKI             "$EASYRSA/pki"
set_var EASYRSA_DN              "cn_only"
set_var EASYRSA_REQ_COUNTRY     "FR"
set_var EASYRSA_REQ_PROVINCE    "Paris"
set_var EASYRSA_REQ_CITY        "Paris"
set_var EASYRSA_REQ_ORG         "CLIENT1 CERTIFICATE AUTHORITY"
set_var EASYRSA_REQ_EMAIL       "zetc0de@random.io"
set_var EASYRSA_REQ_OU          "zetc0de EASY CA"
set_var EASYRSA_KEY_SIZE        2048
set_var EASYRSA_ALGO            rsa
set_var EASYRSA_CA_EXPIRE       7500
set_var EASYRSA_CERT_EXPIRE     365
set_var EASYRSA_NS_SUPPORT      "no"
set_var EASYRSA_NS_COMMENT      "zetc0de CERTIFICATE AUTHORITY"
set_var EASYRSA_EXT_DIR         "$EASYRSA/x509-types"
set_var EASYRSA_SSL_CONF        "$EASYRSA/openssl-1.0.cnf"
set_var EASYRSA_DIGEST          "sha256"
```

```
chmod +x vars
```

###Configure easy-rsa.

##### - Initialization
```
./easyrsa init-pki
```

##### - Build ca
```
./easyrsa build-ca nopass
```

##### - Generate dh
```
./easyrsa gen-dh
```

##### - Generate and Sign Server Key
```
./easyrsa gen-req zetc0de-openvpn nopass
./easyrsa sign-req server zetc0de-openvpn
```

##### - Generate and Sign Client Key
```
./easyrsa gen-req client nopass
./easyrsa sign-req client client
```

##### - Generate crl
```
./easyrsa gen-crl
```

##### - Copy Server Certificate

```
cp pki/ca.crt /etc/openvpn/server/
cp pki/issued/zetc0de-openvpn.crt /etc/openvpn/server/
cp pki/private/zetc0de-openvpn.key /etc/openvpn/server/
```

##### - Copy crl and dh certificate
```
cp pki/dh.pem /etc/openvpn/server/
cp pki/crl.pem /etc/openvpn/server/
```

#### Copy Client Certificate
```
cp pki/ca.crt /etc/openvpn/client/
cp pki/issued/client.crt /etc/openvpn/client/
cp pki/private/client.key /etc/openvpn/client/
```

###Configure openvpn

##### Server Config
```
cd /etc/openvpn/
vim server.conf
```

```
#change with your port
port 4095

#You can use udp or tcp
proto udp

# "dev tun" will create a routed IP tunnel.
dev tun

#Certificate Configuration

#ca certificate
ca /etc/openvpn/server/ca.crt

#Server Certificate
cert /etc/openvpn/server/zetc0de-openvpn.crt

#Server Key and keep this is secret
key /etc/openvpn/server/zetc0de-openvpn.key

#See the size a dh key in /etc/openvpn/keys/
dh /etc/openvpn/server/dh.pem

crl-verify /etc/openvpn/server/crl.pem

#Internal IP will get when already connect
server 192.168.200.0 255.255.255.0

#this line will redirect all traffic through our OpenVPN
push "redirect-gateway def1"

#Provide DNS servers to the client, you can use goolge DNS
push "dhcp-option DNS 84.200.69.80"
push "dhcp-option DNS 84.200.70.40"

#Enable multiple client to connect with same key
duplicate-cn

keepalive 20 60
persist-key
persist-tun
compress lzo
daemon

cipher AES-256-CBC

tls-version-min 1.2
auth SHA512
tls-cipher TLS-DHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-256-CBC-SHA256:TLS-DHE-RSA-WITH-AES-128-GCM-SHA256:TLS-DHE-RSA-WITH-AES-128-CBC-SHA256
auth-nocache

user nobody
group nobody

#enable log
log-append /var/log/openvpn.log

#Log Level
verb 3
```

###Configure Firewalld and Enable Port Forwarding

##### Firewalld Configuration
```
firewall-cmd --permanent --add-service openvpn
firewall-cmd --permanent --zone=trusted --add-interface=tun0
firewall-cmd --permanent --zone=trusted --add-masquerade
DEV=$(ip route get 8.8.8.8 | awk 'NR==1 {print $(NF-2)}')
firewall-cmd --permanent --direct --passthrough ipv4 -t nat -A POSTROUTING -s  192.168.200.0/24 -o $DEV -j MASQUERADE
firewall-cmd --reload
```

```
cd /lib/firewalld/services/
vim openvpn.xml
```

```
Change Default Port
```

##### Enable Port Forward 
```
vim /etc/sysctl.conf
```

```
net.ipv4.ip_forward = 1
```

#####  Disable SELinux
```
vim /etc/sysconfig/selinux
```

```
SELINUX=disabled
```

```
sysctl -p
systemctl restart openvpn@server
systemctl enable openvpn@server
```


###Start openVPN Server.
```
systemctl start openvpn@server
```

###SettingUp The OpenVPN Client Application

##### Client Configuraition
```
cd /etc/openvpn/client/
vim zetc0de.ovpn
```

```
client
dev tun
proto udp

#Server IP and Port
remote 65.135.62.215 3068 ##diganti dengan IP server dan Port OpenVPN anda

resolv-retry infinite
nobind
persist-key
persist-tun
mute-replay-warnings
ca ca.crt
cert client.crt
key client.key
remote-cert-tls server
compress lzo

verb 3
cipher AES-256-CBC
auth SHA512
auth-nocache
tls-version-min 1.2
tls-cipher TLS-DHE-RSA-WITH-AES-256-GCM-SHA384:TLS-DHE-RSA-WITH-AES-256-CBC-SHA256:TLS-DHE-RSA-WITH-AES-128-GCM-SHA256:TLS-DHE-RSA-WITH-AES-128-CBC-SHA256
```

```
cd /etc/openvpn/
tar -czf zetc0de.tar.gz client/*
```

```
mv zetc0de.tar.gz /home/lightmoon/
chown -R lightmoon:lightmoon /home/lightmoon/
```

##### Download Client
```
scp -P 4053 -i Keys/1/satu lightmoon@37.59.221.239:~/zetc0de.tar.gz .
```
