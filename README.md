# Praktikum Jarkom Modul 1 Kelompok B09

Anggota Kelompok B09:
| Nama | NRP |
| ---------------------- | ---------- |
| Melanie Sayyidina Sabrina Refman | 5025211029 |
| I Gusti Agung Ngurah Adhi Sanjaya | 5025211056 |

----
## Soal No.1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. 

### Topologi 1

(gambar)

### Konfigurasi Node
Konfigurasi sesuai modul GNS. Menggunakan IP Prefix 10.13 -> IP Kelompok B09.

#### Konfigurasi Pandudewanata
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.13.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.13.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.13.3.1
	netmask 255.255.255.0
```

#### Konfigurasi Yudhistira
```auto eth0
iface eth0 inet static
	address 10.13.1.2
	netmask 255.255.255.0
	gateway 10.13.1.1
```

#### Konfigurasi Nakula
```
auto eth0
iface eth0 inet static
	address 10.13.1.3
	netmask 255.255.255.0
	gateway 10.13.1.1
```

#### Konfigurasi Werkudara
```
auto eth0
iface eth0 inet static
	address 10.13.2.2
	netmask 255.255.255.0
	gateway 10.13.2.1
```

#### Konfigurasi Sadewa
```
auto eth0
iface eth0 inet static
	address 10.13.2.3
	netmask 255.255.255.0
	gateway 10.13.2.1
```

#### Konfigurasi Arjuna
```
auto eth0
iface eth0 inet static
	address 10.13.3.2
	netmask 255.255.255.0
	gateway 10.13.3.1
```

#### Konfigurasi Wisanggeni
````
auto eth0
iface eth0 inet static
	address 10.13.3.3
	netmask 255.255.255.0
	gateway 10.13.3.1
````

#### Konfigurasi Abimanyu
````
auto eth0
iface eth0 inet static
	address 10.13.3.4
	netmask 255.255.255.0
	gateway 10.13.3.1
````

#### Konfigurasi Prabukusuma
```
auto eth0
iface eth0 inet static
	address 10.13.3.5
	netmask 255.255.255.0
	gateway 10.13.3.1
```

### Setting di Web Console

#### Pandudewanata
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.13.0.0/16
cat /etc/resolv.conf
```

#### Node Lain
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Pembuktian
`ping google.com` pada node lain

(gambar)

## Soal No.2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

### Setting Domain Yudhistira

#### Install Bind
```
apt-get update
apt-get install bind9 -y
```

### Setting Domain
```
nano /etc/bind/named.conf.local
zone "arjuna.b09.com" {
	type master;
	file "/etc/bind/prak2/arjuna.b09.com";
};

mkdir /etc/bind/prak2

cp /etc/bind/db.local /etc/bind/prak2/arjuna.b09.com

nano /etc/bind/prak2/arjuna.b09.com
@ IN SOA arjuna.b09.com. root.arjuna.b09.com. (
    2023090201 ; serial
    604800 ; refresh
    86400 ; retry
    2419200 ; expire
    604800 ) ; minimum ttl
; Name server
@ IN NS arjuna.b09.com.
@ IN A 10.13.3.2 ; IP Arjuna
www IN CNAME arjuna.b09.com.

service bind9 restart
```

#### Setting Nameserver pada Client (Nakula dan Sadewa)
```
nano /etc/resolv.conf
  nameserver 10.13.1.2 ; IP Yudhistira

ping arjuna.b09.com -c 5
```

### Pembuktian
`ping ajuna.b09.com -c 5` pada nakula dan sadewa

(gambar)

