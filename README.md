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

### Setting di Worker (Prabukusuma, Abimanyu, Wisanggeni)
```
apt-get update
apt-get install nginx php php-fpm -y

php -v

mkdir /var/www/prak2

apt-get install git -y

git -c http.sslVerify=false clone https://github.com/melanierefman/jarkom-prak2-arjuna /var/www/prak2

nano > /etc/nginx/sites-available/prak2
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
```
### Setting di Client (Nakula dan Sadewa)
```
apt-get update
apt-get install lynx
```

### Pembuktian
`lynx 10.13.3.5` pada client

![soal9-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/dfa99ef1-3c66-4e33-ace5-2a6e602e247b)

`lynx 10.13.3.4` pada client

![soal9-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/4e402666-510b-4c45-903a-4e91e4b5d98c)

`lynx 10.13.3.3` pada client

![soal9-3](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/1fdd1c19-7b38-4f03-bef9-a6d1a44b01cc)

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

![soal10-1](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/a58dee73-7385-439d-9a27-67614ad5baad)

`lynx 10.13.3.4:8002` pada client

![soal10-2](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/7d4cbac1-daa9-45f6-9357-cca0d24308a4)

`lynx 10.13.3.3:8003` pada client

![soal10-3](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/17666ba2-0283-4a4d-b57d-7fb05383484b)

## Soal No.11
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

### Setting di Abimanyu
```
apt-get install apache2 -y

service apache2 start

apt-get install libapache2-mod-php7.0 -y

apt-get install git -y

cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.b09.com.conf

rm /etc/apache2/sites-available/000-default.conf

echo '
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName abimanyu.b09.com
    ServerAlias www.abimanyu.b09.com
    DocumentRoot /var/www/abimanyu.b09

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.b09.com.conf

mkdir /var/www/abimanyu.b09

git -c http.sslVerify=false clone https://github.com/melanierefman/jarkom-prak2-abimanyu /var/www/abimanyu.b09

a2ensite abimanyu.b09.com

service apache2 restart
```

### Pembuktian
`lynx abimanyu.b09.com` pada client

![soal11](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87106838/e5b9ab08-ec61-48cb-a4cc-f93d1f19448f)

## Soal No.12
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.
### Jawab
pada soal ini, kita menggunakan system rewrite menggunakan
`a2enmod rewrite`
kemudian kita lakukan restart pada apache2 dengan
`service apache2 restart`
lalu membuat file .htaccess
`touch /var/www/abimanyu.b07/.htaccess`

lalu di dalam file `.htaccess` tulis config berikut :
```
RewriteEngine On
RewriteBase /

RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^(.*)$ index.php/$1 [L]
```

kemudian masukkan beberapa konfigurasi pada `/etc/apache2/sites-available/abimanyu.b09.com.conf` seperti berikut :
```
<Directory /var/www/abimanyu.b09>
                Options Indexes FollowSymLinks
                AllowOverride All
                Require all granted
        </Directory>
```
lalu kita bisa akses di :
```
lynx www.abimanyu.yyy.com/home
```
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/a5813626-92f8-4f36-a630-381ef7c29163)

## Soal No.13
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy
### Jawab
isi dari config soal13.sh
```
touch /etc/apache2/sites-available/parikesit.abimanyu.b09.com.conf
echo '
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.b09
        ServerName parikesit.abimanyu.b09.com
        ServerAlias www.parikesit.abimanyu.b09.com

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.b09.com.conf
a2ensite parikesit.abimanyu.b09.com
service apache2 restart
mkdir /var/www/parikesit.abimanyu.b09
cp -v -r /root/parikesit.abimanyu.yyy.com/* /var/www/parikesit.abimanyu.b09/
```
## Pembuktian berhasil
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/5cff5d69-a8aa-4bdd-8d5c-f354b776303b)

## Soal No.14
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).
### Jawab
kita membuat folder secret di dalam `/var/www/parikesit.abimanyu.b09` dengan menggunakan command disamping dan membuat file htmlnya karena file tidak ada atau tidak disediakan.
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/8c389532-fbeb-4563-a8ed-f21a5e06a35a)
kemudian kita melakukan edit pada file konfigurasi di `/etc/apache2/sites-available/parikesit.abimanyu.b09.com.conf` dengan menambahkan sebagai berikut :
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/3f2b1d90-6552-4018-aa74-0805a3627337)

## Pembuktian
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/6dfb6a94-765a-4405-b1e0-467c1254c654)
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/8f2a2e8c-f62b-405f-a773-9a0243545de5)

## Soal No.15
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.
### Jawab
kita config ke dalam `nano /etc/apache2/apache2.conf`
lalu kita masukkan config error seperti ini :
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/63c66fc5-de46-45db-a10f-d5fa15c74676)

## Pembuktian
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/f73cf42d-b82d-4507-8018-571f4dae34e2)

## Soal No.16
Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi www.parikesit.abimanyu.yyy.com/js
### Jawab

pertama2, kita masuk ke :
```
nano /etc/apache2/sites-available/parikesit.abimanyu.b07.com.conf
```
lalu menambahkan ini :
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/0875c705-3c91-486c-a344-4f9d362cad58)

## Pembuktian
lalu jalankan lynx parikesit.abimanyu.b09.com/js
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/b90b3929-ee0b-40d2-a9e6-d7720b5702fa)

## Soal No.17
Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.
### Jawab
```
touch /etc/apache2/sites-available/rjp.baratayuda.abimanyu.b09.com.conf
echo '
<VirtualHost *:14000>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.b09
        ServerName rjp.baratayuda.abimanyu.b09.com
        ServerAlias www.rjp.baratayuda.abimanyu.b09.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:14400>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.b09
        ServerName rjp.baratayuda.abimanyu.b09.com
        ServerAlias www.rjp.baratayuda.abimanyu.b09.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.b09.com.conf

echo '
# This is the main Apache server configuration file.  It contains the
# configuration directives that give the server its instructions.
# See http://httpd.apache.org/docs/2.4/ for detailed information about
# the directives and /usr/share/doc/apache2/README.Debian about Debian specific
# hints.
#
#
# Summary of how the Apache 2 configuration works in Debian:
# The Apache 2 web server configuration in Debian is quite different to
# upstream's suggested way to configure the web server. This is because Debian's
# default Apache2 installation attempts to make adding and removing modules,
# virtual hosts, and extra configuration directives as flexible as possible, in
# order to make automating the changes and administering the server as easy as
# possible.

# It is split into several files forming the configuration hierarchy outlined
# below, all located in the /etc/apache2/ directory:
#
#       /etc/apache2/
#       |-- apache2.conf
#       |       `--  ports.conf
#       |-- mods-enabled
#       |       |-- *.load
#       |       `-- *.conf
#       |-- conf-enabled
#       |       `-- *.conf
#       `-- sites-enabled
#               `-- *.conf
#
#
# * apache2.conf is the main configuration file (this file). It puts the pieces
#   together by including all remaining configuration files when starting up the
#   web server.
#
# * ports.conf is always included from the main configuration file. It is
#   supposed to determine listening ports for incoming connections which can be
#   customized anytime.
#
# * Configuration files in the mods-enabled/, conf-enabled/ and sites-enabled/
#   directories contain particular configuration snippets which manage modules,
#   global configuration fragments, or virtual host configurations,
#   respectively.
#
#   They are activated by symlinking available configuration files from their
#   respective *-available/ counterparts. These should be managed by using our
#   helpers a2enmod/a2dismod, a2ensite/a2dissite and a2enconf/a2disconf. See
#   their respective man pages for detailed information.
#
# * The binary is called apache2. Due to the use of environment variables, in
#   the default configuration, apache2 needs to be started/stopped with
#   /etc/init.d/apache2 or apache2ctl. Calling /usr/bin/apache2 directly will not
#   work with the default configuration.


# Global configuration
#

#
# ServerRoot: The top of the directory tree under which the server
# configuration, error, and log files are kept.
#
# NOTE!  If you intend to place this on an NFS (or otherwise network)
# mounted filesystem then please read the Mutex documentation (available
# at <URL:http://httpd.apache.org/docs/2.4/mod/core.html#mutex>);
# you will save yourself a lot of trouble.
#
# Do NOT add a slash at the end of the directory path.
#
#ServerRoot "/etc/apache2"

#
# The accept serialization lock file MUST BE STORED ON A LOCAL DISK.
#
#Mutex file:${APACHE_LOCK_DIR} default

#
# The directory where shm and other runtime files will be stored.
#

DefaultRuntimeDir ${APACHE_RUN_DIR}

#
# PidFile: The file in which the server should record its process
# identification number when it starts.
# This needs to be set in /etc/apache2/envvars
#
PidFile ${APACHE_PID_FILE}

#
# Timeout: The number of seconds before receives and sends time out.
#
Timeout 300

#
# KeepAlive: Whether or not to allow persistent connections (more than
# one request per connection). Set to "Off" to deactivate.
#
KeepAlive On

#
# MaxKeepAliveRequests: The maximum number of requests to allow
# during a persistent connection. Set to 0 to allow an unlimited amount.
# We recommend you leave this number high, for maximum performance.
#
MaxKeepAliveRequests 100

#
# KeepAliveTimeout: Number of seconds to wait for the next request from the
# same client on the same connection.
#
KeepAliveTimeout 5


# These need to be set in /etc/apache2/envvars
User ${APACHE_RUN_USER}
Group ${APACHE_RUN_GROUP}

#
# HostnameLookups: Log the names of clients or just their IP addresses
# e.g., www.apache.org (on) or 204.62.129.132 (off).
# The default is off because it be overall better for the net if people
# had to knowingly turn this feature on, since enabling it means that
# each client request will result in AT LEAST one lookup request to the
# nameserver.
#
HostnameLookups Off

# ErrorLog: The location of the error log file.
# If you do not specify an ErrorLog directive within a <VirtualHost>
# container, error messages relating to that virtual host will be
# logged here.  If you do define an error logfile for a <VirtualHost>
# container, that host errors will be logged there and not here.
#
ErrorLog ${APACHE_LOG_DIR}/error.log

#
# LogLevel: Control the severity of messages logged to the error_log.
# Available values: trace8, ..., trace1, debug, info, notice, warn,
# error, crit, alert, emerg.
# It is also possible to configure the log level for particular modules, e.g.
# "LogLevel info ssl:warn"
#
LogLevel warn

# Include module configuration:
IncludeOptional mods-enabled/*.load
IncludeOptional mods-enabled/*.conf

# Include list of ports to listen on
Include ports.conf


# Sets the default security model of the Apache2 HTTPD server. It does
# not allow access to the root filesystem outside of /usr/share and /var/www.
# The former is used by web applications packaged in Debian,
# the latter may be used for local directories served by the web server. If
# your system is serving content from a sub-directory in /srv you must allow
# access here, or in any related virtual host.
<Directory />
        Options FollowSymLinks
        AllowOverride None
        Require all denied
</Directory>

<Directory /usr/share>
        AllowOverride None
        Require all granted
</Directory>

<Directory /var/www/>
        Options Indexes FollowSymLinks
        AllowOverride None
        Require all granted
</Directory>

#<Directory /srv/>
#       Options Indexes FollowSymLinks
#       AllowOverride None
#       Require all granted
#</Directory>




# AccessFileName: The name of the file to look for in each directory
# for additional configuration directives.  See also the AllowOverride
# directive.
#
AccessFileName .htaccess

#
# The following lines prevent .htaccess and .htpasswd files from being
# viewed by Web clients.
#
<FilesMatch "^\.ht">
        Require all denied
</FilesMatch>


#
# The following directives define some format nicknames for use with
# a CustomLog directive.
#
# These deviate from the Common Log Format definitions in that they use %O
# (the actual bytes sent including headers) instead of %b (the size of the
# requested file), because the latter makes it impossible to detect partial
# requests.
#
# Note that the use of %{X-Forwarded-For}i instead of %h is not recommended.
# Use mod_remoteip instead.
#
LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
LogFormat "%h %l %u %t \"%r\" %>s %O" common
LogFormat "%{Referer}i -> %U" referer
LogFormat "%{User-agent}i" agent

# Include of directories ignores editors' and dpkg's backup files,
# see README.Debian for details.

# Include generic snippets of statements
IncludeOptional conf-enabled/*.conf

# Include the virtual host configurations:
IncludeOptional sites-enabled/*.conf

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet

ErrorDocument 404 /error/404.html
ErrorDocument 403 /error/403.html

listen 14000
listen 14400' > /etc/apache2/apache2.conf

a2ensite rjp.baratayuda.abimanyu.b09.com
service apache2 restart
mkdir /var/www/rjp.baratayuda.abimanyu.b09
cp -v /root/rjp.baratayuda.abimanyu.yyy.com/* /var/www/rjp.baratayuda.abimanyu.b09/
```
## Soal No.18
Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.
### Jawab
```
htpasswd -cb /etc/apache2/.htpasswd Wayang baratayudab09
# htpasswd -D /etc/apache2/.htpasswd Wayang (untuk menghapus)
echo '
<VirtualHost *:14000>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.b09
        ServerName rjp.baratayuda.abimanyu.b09.com
        ServerAlias www.rjp.baratayuda.abimanyu.b09.com

        <Directory /var/www/rjp.baratayuda.abimanyu.b09>
                AuthType Basic
                AuthName "Authentication Required"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>

        <Files "password">
                Order allow,deny
                Deny from all
        </Files>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

<VirtualHost *:14400>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.b09
        ServerName rjp.baratayuda.abimanyu.b09.com
        ServerAlias www.rjp.baratayuda.abimanyu.b09.com

        <Directory /var/www/rjp.baratayuda.abimanyu.b09>
                AuthType Basic
                AuthName "Authentication Required"
                AuthUserFile /etc/apache2/.htpasswd
                Require valid-user
        </Directory>

        <Files "password">
                Order allow,deny
                Deny from all
        </Files>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.b09.com.conf
service apache2 restart
```

## Soal No.19
Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)
### Jawab
tambahkan config ini ke `nano abimanyu.b07.com.conf` :
```
<VirtualHost *:80>
        ServerName 10.13.3.4
        Redirect permanent / http://www.abimanyu.b09.com/
</VirtualHost>
```
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/935b9f34-1b20-431e-9f37-5da24f3f38fe)

## Outputnya 
![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/73494839-daa5-4015-8952-48a441f731b1)

![image](https://github.com/melanierefman/Jarkom-Modul-2-B09-2023/assets/87845735/67f5a9d9-6e0d-465e-af27-02ac66a8b0ac)

## Soal No.20
Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.
### Jawab
```
a2enmod rewrite
touch /var/www/parikesit.abimanyu.b09/.htaccess

echo '
RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/(.*)(abimanyu)(.*\.(png|jpg))
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule abimanyu http://parikesit.abimanyu.b09.com/public/images/abimanyu.png$1 [L,R=301]' > /var/www/parikesit.abimanyu.b09/.htaccess

echo '
<VirtualHost *:80>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.b09
        ServerName parikesit.abimanyu.b09.com
        ServerAlias www.parikesit.abimanyu.b09.com

        <Directory /var/www/parikesit.abimanyu.b09/public>
                Options +Indexes
        </Directory>

        <Directory /var/www/parikesit.abimanyu.b09/secret>
                Options -Indexes
        </Directory>

        <Directory /var/www/parikesit.abimanyu.b09/public/js>
                Options +Indexes
        </Directory>

	<Directory /var/www/parikesit.abimanyu.b09>
        	Options +FollowSymLinks -Multiviews
        	AllowOverride All
	</Directory>

        Alias "/js" "/var/www/parikesit.abimanyu.b09/public/js"

        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf

</VirtualHost>' > nano /etc/apache2/sites-available/parikesit.abimanyu.b09.com.conf

service apache2 restart

```
