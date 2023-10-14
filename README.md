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

![soal1-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/a23c091f-0c96-4398-98b3-e994c06c106c)

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

![soal1-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/b90467f9-64e8-4f13-85be-41fba492b2e4)

## Soal No.2
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

### Install Bind
```
apt-get update
apt-get install bind9 -y
```

### Setting Domain di Yudhistira
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

### Setting Nameserver pada Client (Nakula dan Sadewa)
```
nano /etc/resolv.conf
nameserver 10.13.1.2 ; IP Yudhistira

ping arjuna.b09.com -c 5
```

### Pembuktian
`ping ajuna.b09.com -c 5` pada nakula dan sadewa

![soal2-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/95def53f-e584-4368-8b91-06e354f3e881)
![soal2-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/15bbf71e-2d65-4589-9b16-08cdf8004f29)

## Soal No.3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

### Setting Domain di Yudhistira
```
nano /etc/bind/named.conf.local
zone "abimanyu.b09.com" {
	type master;
	file "/etc/bind/prak2/abimanyu.b09.com";
};

cp /etc/bind/db.local /etc/bind/prak2/abimanyu.b09.com

nano /etc/bind/prak2/abimanyu.b09.com
@ IN SOA abimayu.b09.com. root.abimayu.b09.com. (
    2023101001 ; serial
    604800 ; refresh
    86400 ; retry
    2419200 ; expire
    604800 ) ; minimum ttl
; Name server
@ IN NS abimamyu.b09.com.
@ IN A 10.13.3.4 ; IP Abimayu
www IN CNAME abimayu.b09.com.

service bind9 restart
```

### Pembuktian pada Client (Nakula dan Sadewa)
`ping  abimanyu.b09.com -c 5` pada nakula dan sadewa

![soal3-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/41b294fb-90c7-443d-8af9-28a79e2339bc)
![soal3-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/3df0bd0b-0fbd-4d48-ab8b-aac8574e715d)

## Soal No.4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

### Membuat Subdomain di Yudhistira
```
nano /etc/bind/prak2/abimanyu.b09.com
tambahkan
parikesit IN A 10.13.3.4 ; IP Abimanyu

service bind9 restart
```

### Pembuktian pada Client (Nakula dan Sadewa)
`ping parikesit.abimanyu.b09.com -c 5` pada nakula dan sadewa

![soal4-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/61c00552-29e9-4a97-92cf-b10d56bf266f)
![soal4-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/36bb9f14-b393-4595-a5bd-88a8816a1568)

## Soal No.5 
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

### Reverse Domain di Yudhistira
```
nano /etc/bind/named.conf.local

zone "1.13.10.in-addr.arpa" {
    type master;
    file "/etc/bind/prak2/1.13.10.in-addr.arpa";
};

cp /etc/bind/db.local /etc/bind/prak2/1.13.10.in-addr.arpa

nano /etc/bind/prak2/1.13.10.in-addr.arpa
@ IN SOA abimayu.b09.com. root.abimayu.b09.com. (
    2023101001 ; serial
    604800 ; refresh
    86400 ; retry
    2419200 ; expire
    604800 ) ; minimum ttl
;
1.13.10.in-addr.arpa. IN NS abimamyu.b09.com.
2 IN PTR abimamyu.b09.com.

service bind9 restart
```

### Pembuktian pada Client (Nakula dan Sadewa)
```
apt-get update

apt-get install dnsutils

nano /etc/resolv.conf
nameserver 10.13.1.2 # IP Yudhistira

host -t PTR 10.13.1.2
```

![soal5-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/f32abb53-606d-4906-9629-1ce288d2d93f)
![soal5-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/6947c377-5a57-498f-982f-21f2eb041b1d)

## Soal No.6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

### Setting di Yudhistira
```
nano /etc/bind/named.conf.local
zone "arjuna.b09.com" {
    type master;
    notify yes;
    also-notify { 10.13.2.2; };
    allow-transfer { 10.13.2.2; };
    file "/etc/bind/prak2/arjuna.b09.com";
};

service bind9 restart
```

### Setting di Werkudara
```
apt-get update

apt-get install bind9 -y

nano /etc/bind/named.conf.local
zone "arjuna.b09.com" {
    type slave;
    masters { 10.13.1.2; }; // Masukan IP Yudhistira tanpa tanda petik
    file "/var/lib/bind/arjuna.b09.com";
};

service bind9 restart
```

### Setting di Client (Nakula dan Sadewa)
```
nano /etc/resolv.conf
nameserver 10.13.1.2 ; IP Yudhistira
nameserver 10.13.2.2 ; IP Werkudara
```

### Pembuktian
`ping arjuna.b09.com -c 5` pada client

![soal6-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/46a1206d-022d-4935-a7fd-4c1b78c4eeee)
![soal6-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/e2c95faa-db8a-44cf-81a9-8ec343103ebc)

## Soal No.7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

### Setting di Yudhistira
```
nano /etc/bind/prak2/abimanyu.b09.com
tambahkan
ns1 	IN  	A  	10.13.2.2 ; IP Werkudara
baratayuda IN   	NS  ns1

nano /etc/bind/named.conf.options
//dnssec-validation auto;
allow-query{any;};

nano /etc/bind/named.conf.local
zone "abimanyu.b09.com" {
	type master;
	file "/etc/bind/prak2/abimanyu.b09.com";
	allow-transfer { 10.13.2.2; };
};

service bind9 restart
```

### Setting di Werkudara
```
nano /etc/bind/named.conf.options
//dnssec-validation auto;
allow-query{any;};

nano /etc/bind/named.conf.local
zone "baratayuda.abimanyu.b09.com" {
    type master;
    file "/etc/bind/baratayuda/baratayuda.abimanyu.b09.com";
};

mkdir /etc/bind/baratayuda

cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.b09.com

nano /etc/bind/baratayuda/baratayuda.abimanyu.b09.com
@ IN SOA baratayuda.abimanyu.b09.com. root.baratayuda.abimanyu.b09.com. (
    2023101001 ; serial
    604800     ; refresh
    86400      ; retry
    2419200    ; expire
    604800 )   ; minimum TTL
; Name server
@ IN NS baratayuda.abimanyu.b09.com.
@ IN A 10.13.2.2 ; IP Werkudara

service bind9 restart
```

### Pembuktian pada Client (Nakula dan Sadewa)
`ping baratayuda.abimanyu.b09.com -c 5` pada nakula dan sadewa

![soal7-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/47aebdb8-3e73-46fe-b49b-4afd73740b53)
![soal7-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/9d8366e6-b59a-4378-bb6a-bbd50d2b2772)

## Soal No.8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu

### Setting di Werkudara
```
nano /etc/bind/baratayuda/baratayuda.abimanyu.b09.com
rjp IN A 10.13.2.2 ; IP Werkudara

service bind9 restart
```

### Pembuktian pada Client (Nakula dan Sadewa)
`ping rjp.baratayuda.abimanyu.b09.com -c 5` pada nakula dan sadewa

![soal8-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/420cdbea-e8a0-4575-8481-02deeee234e4)
![soal8-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/a6c699f2-a7bb-40da-abca-df68bc2725b3)

## Soal No.9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

### Setting di Arjuna sebagai Load Balancer
```
apt-get update
apt-get install nginx

service nginx restart
service nginx status

nano /etc/nginx/sites-available/lb-prak2
upstream myweb {
	server 10.13.3.3;
	server 10.13.3.4;
	server 10.13.3.5;
}

server {
	listen 80;
	server_name arjuna.a09.com;

	location / {
	proxy_pass http://myweb:8000/;
	}
}

ln -s /etc/nginx/sites-available/lb-prak2 /etc/nginx/sites-enabled
```

### Setting di Prabukusuma sebagai Worker
```
apt-get update
apt-get install nginx php php-fpm -y

php -v

mkdir /var/www/prak2

nano /var/www/prak2/index.php
<?php
echo "Welcome to Prabukusuma";
?>

nano /etc/nginx/sites-available/prak2
server {
	listen 80;
	root /var/www/prak2;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}
	
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/prak2_error.log;
	access_log /var/log/nginx/prak2_access.log;
}

ln -s /etc/nginx/sites-available/prak2 /etc/nginx/sites-enabled

rm -f /etc/nginx/sites-enabled/default

service php7.0-fpm start
service nginx reload
service nginx restart

nginx -t

service nginx restart
```

### Setting di Abimanyu sebagai Worker
```
apt-get update
apt-get install nginx php php-fpm -y

php -v

mkdir /var/www/prak2

nano /var/www/prak2/index.php
<?php
echo "Welcome to Abimanyu";
?>

nano /etc/nginx/sites-available/prak2
server {
	listen 80;
	root /var/www/prak2;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}
	
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/prak2_error.log;
	access_log /var/log/nginx/prak2_access.log;
}

ln -s /etc/nginx/sites-available/prak2 /etc/nginx/sites-enabled

rm -f /etc/nginx/sites-enabled/default

service php7.0-fpm start
service nginx reload
service nginx restart

nginx -t

service nginx restart
```

### Setting di Wisanggeni sebagai Worker
```
apt-get update
apt-get install nginx php php-fpm -y

php -v

mkdir /var/www/prak2

nano /var/www/prak2/index.php
<?php
echo "Welcome to Wisanggeni";
?>

nano /etc/nginx/sites-available/prak2
server {
	listen 80;
	root /var/www/prak2;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}
	
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/prak2_error.log;
	access_log /var/log/nginx/prak2_access.log;
}

ln -s /etc/nginx/sites-available/prak2 /etc/nginx/sites-enabled

rm -f /etc/nginx/sites-enabled/default

service php7.0-fpm start
service nginx reload
service nginx restart

nginx -t

service nginx restart
```
### Setting di Client (Nakula dan Sadewa)
```
apt-get update
apt-get install lynx
```

### Pembuktian
`lynx 10.13.3.5` pada client

![soal9](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/7345e86d-d91a-4f8f-bcdf-03c37ceaa5ad)

`lynx 10.13.3.4` pada client

(gambar)

`lynx 10.13.3.3` pada client

(gambar)

## Soal No.10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003

### Setting di Arjuna sebagai Load Balancer
```
nano /etc/nginx/sites-available/lb-prak2
upstream myweb {
	server 10.13.3.3:8003;
	server 10.13.3.4:8002;
	server 10.13.3.5:8001;
}

server {
	listen 80;
	server_name arjuna.a09.com;

	location / {
	proxy_pass http://myweb/;
	}
}

ln -s /etc/nginx/sites-available/lb-prak2 /etc/nginx/sites-enabled
```

### Setting di Prabukusuma sebagai Worker
```
nano /etc/nginx/sites-available/prak2
server {
	listen 8001;
	root /var/www/prak2;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

	
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/prak2_error.log;
	access_log /var/log/nginx/prak2_access.log;
}

ln -s /etc/nginx/sites-available/prak2 /etc/nginx/sites-enabled

rm -f /etc/nginx/sites-enabled/default

service php7.0-fpm start
service nginx reload
service nginx restart

nginx -t

service nginx restart
```

### Setting di Abimanyu sebagai Worker
```
nano /etc/nginx/sites-available/prak2
server {
	listen 8002;
	root /var/www/prak2;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

	
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/prak2_error.log;
	access_log /var/log/nginx/prak2_access.log;
}

ln -s /etc/nginx/sites-available/prak2 /etc/nginx/sites-enabled

rm -f /etc/nginx/sites-enabled/default

service php7.0-fpm start
service nginx reload
service nginx restart

nginx -t

service nginx restart
```

### Setting di Wisanggeni sebagai Worker
```
nano /etc/nginx/sites-available/prak2
server {
	listen 8003;
	root /var/www/prak2;

	index index.php index.html index.htm;
	server_name _;

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}

	
	location ~ \.php$ {
		include snippets/fastcgi-php.conf;
		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
	}

	location ~ /\.ht {
		deny all;
	}

	error_log /var/log/nginx/prak2_error.log;
	access_log /var/log/nginx/prak2_access.log;
}

ln -s /etc/nginx/sites-available/prak2 /etc/nginx/sites-enabled

rm -f /etc/nginx/sites-enabled/default

service php7.0-fpm start
service nginx reload
service nginx restart

nginx -t

service nginx restart
```
### Pembuktian
`lynx 10.13.3.5:8001` pada client

(gambar)

`lynx 10.13.3.4:8002` pada client

(gambar)

`lynx 10.13.3.3:8003` pada client

(gambar)

## Soal No.11
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

### Setting di Abimanyu
```
```

### Pembuktian
`lynx abimanyu.b09.com` pada client

(gambar)
