# Jarkom-Modul-2-D04-2023
# Anggota Kelompok
|                Nama                |    NRP     |
| :--------------------------------: | :--------: |
|       Muhammad Rafi Sutrisno       | 5025211167 |
|      Nadira Milha Nailul Fath      | 5025211253 |
## Topologi 8
<img width="531" alt="Screenshot 2023-10-17 053859" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/d275e2e0-c80d-4f57-905d-5d60741c676e">

## Network Configuration
- Router
  ```bash
  auto eth0
  iface eth0 inet dhcp

  auto eth1
  iface eth1 inet static
	address 192.193.1.1
	netmask 255.255.255.0

  auto eth2
  iface eth2 inet static
	address 192.193.2.1
	netmask 255.255.255.0
  ```
- NakulaClient
  ```bash
  auto eth0
  iface eth0 inet static
	address 192.193.1.2
	netmask 255.255.255.0
	gateway 192.193.1.1
  ```
- SadewaClient
  ```bash
  auto eth0
  iface eth0 inet static
	address 192.193.1.3
	netmask 255.255.255.0
	gateway 192.193.1.1
  ```
- AbimanyuWebServer
  ```bash
  auto eth0
  iface eth0 inet static
	address 192.193.1.4
	netmask 255.255.255.0
	gateway 192.193.1.1
  ```
- PrabukusumaWebServer
  ```bash
  auto eth0
  iface eth0 inet static
	address 192.193.1.5
	netmask 255.255.255.0
	gateway 192.193.1.1
  ```
- WisanggeniWebServer
  ```bash
  auto eth0
  iface eth0 inet static
	address 192.193.1.6
	netmask 255.255.255.0
	gateway 192.193.1.1
  ```
- ArjunaLoadBalancer
  ```bash
  auto eth0
  iface eth0 inet static
	address 192.193.2.2
	netmask 255.255.255.0
	gateway 192.193.2.1
  ```
- WerkudaraDNSSlave
  ```bash
  auto eth0
  iface eth0 inet static
	address 192.193.2.3
	netmask 255.255.255.0
	gateway 192.193.2.1
  ```
- YudhistiraDNSMaster
  ```bash
  auto eth0
  iface eth0 inet static
	address 192.193.2.4
	netmask 255.255.255.0
	gateway 192.193.2.1
  ```
Setelah melakukan network configuration, lakukan start project
## IP Configuration
```bash
Switch1 : 192.193.1.1
NakulaClient : 192.193.1.2
SadewaClient : 192.193.1.3
AbimanyuWebServer : 192.193.1.4
PrabukusumoWebServer : 192.193.1.5
WisanggeniWebServer : 192.193.1.6

Switch2 : 192.193.2.1
ArjunaLoadBalancer : 192.193.2.2
WerkudaraDNSSlave : 192.193.2.3
YudhistiraDNSMaster : 192.193.2.4
```
### Nomor 1
#### Soal
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian <a href='https://docs.google.com/spreadsheets/d/1OqwQblR_mXurPI4gEGqUe7v0LSr1yJViGVEzpMEm2e8/edit?usp=sharing'>sebagai berikut</a>. Folder topologi dapat diakses pada <a href='https://drive.google.com/drive/folders/1Ij9J1HdIW4yyPEoDqU1kAwTn_iIxg3gk?usp=sharing'>drive berikut</a>
#### Penyelesaian
Agar topologi dapat berjalan sesuai dengan fungsi diatas, terdapat script awal pada setiap node yang perlu dijalankan terlebih dahulu.
```bash
# Router
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.193.0.0/16
```
```bash
# Master & Slave
echo 'nameserver 192.168.122.1 # IP DNS' > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```
```bash
# Client
echo '
nameserver 192.168.122.1 # IP DNS
nameserver 192.193.2.4 # IP Yudhistira
nameserver 192.193.2.3 # IP Werkudara
' > /etc/resolv.conf
apt-get update
apt-get install dnsutils -y
apt-get install lynx -y
```
Testing dilakukan pada client dengan menggunakan ``ping google.com``

<img width="544" alt="no1nakula" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/553c3164-43ce-49ea-a7d1-9c2da81690ce">

<img width="541" alt="no1sadewa" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/4526132e-f091-425a-a83e-db94408f7065">

### Nomor 2
#### Soal
Buatlah website utama pada node arjuna dengan akses ke **arjuna.yyy.com** dengan alias **www.arjuna.yyy.com** dengan yyy merupakan kode kelompok.
#### Penyelesaian
Terdapat beberapa langkah untuk membuat website dengan akses **arjuna.D04.com** dengan alias **www.arjuna.D04.com**.
- Pada YudhistiraDNSMaster

  karena telah dilakukan install aplikasi bind9, selanjutnya akan dilakukan pembuatan direktori **jarkom**. kemudian pembuatan domain dilakukan dengan mengubah file **/etc/bind/named.conf.local**.
  ```bash
  mkdir /etc/bind/jarkom
  
  echo '
  zone "arjuna.D04.com" {
	type master;
	file "/etc/bind/jarkom/arjuna.D04.com";
  };' > /etc/bind/named.conf.local
  ```
Setelah itu **copy** file **/etc/bind/db.local** dan modifikasi file yang telah di-copy dengan ip mengarah ke node Arjuna, tambahkan juga CNAME (alias) agar dapat mengakses **www.arjuna.D04.com**, Serta restart bind9.
  ```bash
  cp /etc/bind/db.local /etc/bind/jarkom/arjuna.D04.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.D04.com. root.arjuna.D04.com. (
                     2022100601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.D04.com.
@       IN      A       192.193.2.2     ; IP Arjuna
www	IN	CNAME	arjuna.D04.com.
@       IN      AAAA    ::1


' > /etc/bind/jarkom/arjuna.D04.com

service bind9 restart
  ```
- Pada Client

  Tuliskan IP Yudhistira, agar bisa melakukan ping pada **arjuna.D04.com** dan **www.arjuna.D04.com**
  ```bash
  echo 'nameserver 192.193.2.4 # IP Yudhistira' > /etc/resolv.conf
  ```
  lakukan ping **arjuna.D04.com** dan **www.arjuna.D04.com**
  ```bash
  ping arjuna.D04.com -c 5
  
  ping www.arjuna.D04.com -c 5
  # atau
  host -t CNAME www.arjuna.D04.com
  ```
<img width="417" alt="no2sadewa" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/2022d719-4e5f-4d76-81ab-0cf69c29b557">


<img width="409" alt="no2nakula" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/fd07c913-3608-45fd-92c8-bea3e71900a0">

### Nomor 3
#### Soal
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke **abimanyu.yyy.com** dan alias **www.abimanyu.yyy.com**.
#### Penyelesaian
Cara penyelesaian soal nomor 3 sama dengan soal nomor 2, namun hanya mengganti nama domain dan alias menjadi **abimanyu.yyy.com** dan **www.abimanyu.yyy.com**
- Pada YudhistiraDNSMaster

  Pembuatan domain dilakukan dengan menambahkan script berikut pada file **/etc/bind/named.conf.local**.
  ```bash
  echo '
  zone "abimanyu.D04.com" {
	type master;
	file "/etc/bind/jarkom/abimanyu.D04.com";
  };' >> /etc/bind/named.conf.local
  ```
Setelah itu **copy** file **/etc/bind/db.local** dan modifikasi file yang telah di-copy dengan ip mengarah ke node Abimanyu, tambahkan juga CNAME (alias) agar dapat mengakses **www.abimanyu.D04.com**, Serta restart bind9.
  ```bash
  cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.D04.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.D04.com. root.abimanyu.D04.com. (
                     2022100601         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.D04.com.
@ 	IN      A       192.193.1.4     ; IP Abimanyu
www	IN	CNAME	abimanyu.D04.com.
@       IN      AAAA    ::1


' > /etc/bind/jarkom/abimanyu.D04.com

service bind9 restart
  ```
- Pada Client

  lakukan ping **abimanyu.D04.com** dan **www.abimanyu.D04.com**
  ```bash
  ping abimanyu.D04.com -c 5
  
  ping www.abimanyu.D04.com -c 5
  # atau
  host -t CNAME www.abimanyu.D04.com
  ```
<img width="411" alt="no3nakula" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/376dcf9a-3de5-46d9-84ca-bfa164f5c826">


<img width="404" alt="no3sadewa" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/8d1dfb1a-4c05-40ee-b0e3-1851e8a16436">

### Nomor 4
#### Soal
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain **parikesit.abimanyu.yyy.com** yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
#### Penyelesaian
- Pada YudhistiraDNSMaster
  tambahkan konfigurasi berikut pada **/etc/bind/jarkom/abimanyu.D04.com**
  ```bash
  echo ';
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     abimanyu.D04.com. root.abimanyu.D04.com. (
                     	2022100601         ; Serial
  			    604800         ; Refresh
  			     86400         ; Retry
  		           2419200         ; Expire
                            604800 )       ; Negative Cache TTL
	;
	@       	IN      NS      abimanyu.D04.com.
	@       	IN      A       192.193.1.4     ; IP Abimanyu
	www		IN	CNAME	abimanyu.D04.com.
	parikesit	IN	A	192.193.1.4	; IP Abimanyu
	@       	IN      AAAA    ::1
	' > /etc/bind/jarkom/abimanyu.D04.com

  service bind9 restart
  ```
- Pada Client

  Lakukan testing berikut
  ```bash
  ping parikesit.abimanyu.D04.com -c 5
  # atau
  host -t CNAME parikesit.abimanyu.D04.com
  ```
  <img width="437" alt="no4sadewa" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/c7b94e8b-4111-4ff2-86f5-872450ae6328">

  <img width="437" alt="no4nakula" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/89323f32-a544-4790-a8e6-87697552a68d">

### Nomor 5
#### Soal
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
#### Penyelesaian
Reverse DNS atau Record PTR digunakan untuk menerjemahkan alamat IP ke alamat domain yang sudah diterjemahkan sebelumnya
- Pada YudhistiraDNSMaster
  Edit file **/etc/bind/named.conf.local**, dan copy **/etc/bind/db.local**. Sesuaikan file yang sudah di copy kemudian lakukan restart bind
  ```bash
  echo 'zone "1.193.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/1.193.192.in-addr.arpa";
  };' > /etc/bind/named.conf.local

  cp /etc/bind/db.local /etc/bind/jarkom/1.193.192.in-addr.arpa

  echo ';
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     abimanyu.D04.com. root.abimanyu.D04.com. (
                    	2022100601         ; Serial
  			    604800         ; Refresh
  			     86400         ; Retry
  			   2419200         ; Expire
  			    604800 )       ; Negative Cache TTL
	;
	1.193.192.in-addr.arpa.       IN      NS      abimanyu.D04.com.
	4               		IN      PTR     abimanyu.D04.com.       ; byte ke 4 yudhistira' > /etc/bind/jarkom/1.193.192.in-addr.arpa
  service bind9 restart
  ```
- Pada Client

  Lakukan testing berikut
  ```bash
  host -t PTR 192.193.1.4
  ```
  <img width="346" alt="no5nakula" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/b3c3ab18-221f-405d-a31a-ba72cfb33ae8">

  <img width="347" alt="no5sadewa" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/fcb920f5-d2c6-431b-9c70-0e917dda8dad">

### Nomor 6
#### Soal
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
#### Penyelesaian
- Pada Yudhistira
  Ubah file **/etc/bind/named.conf.local** dengan konfigurasi berikut dan lakukan restart bind9
  ```bash
  echo 'zone "arjuna.D04.com" {
		type master;
    		notify yes;
    		also-notify { 192.193.2.3; }; // Masukan Werkudara
    		allow-transfer { 192.193.2.3; }; // Masukan Werkudara
   		file "/etc/bind/jarkom/arjuna.D04.com";
		};
		zone "abimanyu.D04.com" {
		type master;
    		notify yes;
    		also-notify { 192.193.2.3; }; // Masukan Werkudara
    		allow-transfer { 192.193.2.3; }; // Masukan Werkudara
   		file "/etc/bind/jarkom/abimanyu.D04.com";
		};
		zone "1.193.192.in-addr.arpa" {
    		type master;
    		file "/etc/bind/jarkom/1.193.192.in-addr.arpa";
		};' > /etc/bind/named.conf.local

  service bind9 restart
  ```
- Pada WerkudaraDNSSlave
  
  Sesuaikan konfigurasinya
  ```bash
  echo "nameserver 192.168.122.1" > /etc/resolv.conf

  apt-get update
  apt-get install bind9 -y

  echo 'zone "arjuna.D04.com" {
    	  type slave;
    	  masters { 192.193.2.4; }; // Masukan IP Yudhistira
   	  file "/var/lib/bind/arjuna.D04.com";
  	};

	zone "abimanyu.D04.com" {
   	  type slave;
          masters { 192.193.2.4; }; // Masukan IP Yudhistira
   	  file "/var/lib/bind/abimanyu.D04.com";
	};' >> /etc/bind/named.conf.local
  
  service bind9 restart
  ```
- Pada Client

  Tambahkan IP WerkudaraDNSSlave pada /etc/resolv.conf
  ```bash
  echo 'nameserver 192.193.2.3 # IP Werkudara' >> /etc/resolv.conf
  ```
  Lakukan ping, jika berhasil. Maka DNS Slave berhasil dibuat
  
  <img width="408" alt="no6sadewa" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/fd55b9fb-f85c-49ff-a3e7-a6086f478bf8">

  <img width="409" alt="no6nakula" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/5a61999a-a855-4fa8-9e2c-7deb4d2aadeb">
### Nomor 7
#### Soal
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu **baratayuda.abimanyu.yyy.com** dengan alias **www.baratayuda.abimanyu.yyy.com** yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
#### Penyelesaian
- Pada YudhistiraDNSMaster
  ubahlah file **/etc/bind/jarkom/abimanyu.D04.com** dan **/etc/bind/named.conf.options** dengan konfigurasi berikut
  ```bash
  echo ';
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     abimanyu.D04.com. root.abimanyu.D04.com. (
  			2022100601         ; Serial
                            604800         ; Refresh
                             86400         ; Retry
			   2419200         ; Expire
                            604800 )       ; Negative Cache TTL
	;
	@       	IN      NS      abimanyu.D04.com.
	@       	IN      A       192.193.1.4     ; IP Abhimanyu
	www		IN	CNAME	abimanyu.D04.com.
	parikesit	IN	A	192.193.1.4	; IP Abimanyu
	ns1		IN	A	192.193.2.3	; IP Werkudara
	baratayuda	IN	NS	ns1
	@       	IN      AAAA    ::1' > /etc/bind/jarkom/abimanyu.D04.com

  	echo 'options {
  	directory "/var/cache/bind";

	allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
	};' > /etc/bind/named.conf.options

  service bind9 restart
  ```
- Pada WerkudaraDNSSlave
  Ubahlah file **/etc/bind/named.conf.local**, buatlah direktori baratayuda, dan copy file **/etc/bind/db.local**
  ```bash
  echo 'options {
        directory "/var/cache/bind";
        allow-query{any; };

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
  };' > /etc/bind/named.conf.options

  echo 'zone "baratayuda.abimanyu.D04.com"{
	type master;
	file "/etc/bind/Baratayuda/baratayuda.abimanyu.D04.com";
  };' >> /etc/bind/named.conf.local

  mkdir /etc/bind/Baratayuda
  cp /etc/bind/db.local /etc/bind/Baratayuda/baratayuda.abimanyu.D04.com

  echo ';
	; BIND data file for local loopback interface
	;
	$TTL    604800
	@       IN      SOA     baratayuda.abimanyu.D04.com. root.baratayuda.abimanyu.D04.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
	;
	@       IN      NS      baratayuda.abimanyu.D04.com.
	@       IN      A       192.193.1.4     ; IP abimanyu
	www	IN	CNAME	baratayuda.abimanyu.D04.com.' > /etc/bind/Baratayuda/baratayuda.abimanyu.D04.com

  service bind9 restart

  ```
- Pada Client
  Testing dengan melakukan ping
  ```bash
  ping baratayuda.abimanyu.D04.com -c 5
  ping www.baratayuda.abimanyu.D04.com -c 5
  ```
  <img width="505" alt="no7sadewa" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/6ac1d6dc-c046-4b05-a0e7-15e7a8a119d4">

  <img width="509" alt="no7nakula" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/e11ac684-ce51-4e24-9d5f-a35661af99cc">

### Nomor 8
#### Soal
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses **rjp.baratayuda.abimanyu.yyy.com** dengan alias **www.rjp.baratayuda.abimanyu.yyy.com** yang mengarah ke Abimanyu.
#### Penyelesaian
Penyelesaian nomor 8, hanya dengan menambahkan subdomain dan CNAME pada WerkudaraDNSSlave
```bash
echo '	;
     	; BIND data file for local loopback interface
      	;
	$TTL    604800
	@       IN      SOA     baratayuda.abimanyu.D04.com. root.baratayuda.abimanyu.D04.com. (
                              	2         	; Serial
                        	604800    	; Refresh
                          	86400    	; Retry
                        	2419200         ; Expire
                        	604800 )      	; Negative Cache TTL
	;
	@       IN      NS      baratayuda.abimanyu.D04.com.
	@       IN      A       192.193.1.4     ; IP abimanyu
	rjp     IN      A       192.193.1.4     ; IP abimanyu
	www	IN	CNAME	baratayuda.abimanyu.D04.com.
	www.rjp	IN	CNAME	rjp

' > /etc/bind/Baratayuda/baratayuda.abimanyu.D04.com
```
Testing pada client
```bash
ping rjp.baratayuda.abimanyu.D04.com -c 5
ping www.rjp.baratayuda.abimanyu.D04.com -c 5
```
<img width="507" alt="no8nakula" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/ed21630b-1971-49ec-9a80-7b8c4aac1654">

<img width="506" alt="no8sadewa" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/e93fa0fe-9287-47ae-91d5-c048050994c9">

### Nomor 9
#### Soal
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
#### Penyelesaian
- Pada ArjunaLoadBalancer
  Implementasikan algoritma Round Robin dengan script berikut
  ```bash
  echo nameserver 192.168.122.1 > /etc/resolv.conf
  apt-get update
  apt-get install nginx -y

  # Default menggunakan Round Robin
  echo 'upstream myweb  {
 	server 192.193.1.4; #IP Abimanyu
 	server 192.193.1.5; #IP Prabukusuma
	server 192.193.1.6; #IP Wisanggeni
	}

	server {
 		listen 80;
 		server_name arjuna.D04.com;

 		location / {
 		proxy_pass http://myweb;
 		}
	}' > /etc/nginx/sites-available/lb-jarkom
 	ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

	service nginx restart
  ```
  - Pada AbhimanyuWebServer
    ```bash
    echo nameserver 192.168.122.1 > /etc/resolv.conf

	#INSTALL LINX
	apt-get update
	apt-get install nginx -y
	apt-get install php php-fpm -y
	apt-get install wget -y
	apt-get install unzip -y
	apt-get install libapache2-mod-php7.0

	mkdir /var/www/jarkom

	echo '
	<?php
 	echo "Halo, Kamu berada di Abimanyu";
 	?>' > /var/www/jarkom/index.php

	wget -O /var/www/jarkom/arjuna.yyy.com.zip "https://drive.google.com/u/0/uc?id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX&export=download"

	unzip -d /var/www/jarkom /var/www/jarkom/arjuna.yyy.com.zip && rm /var/www/jarkom/arjuna.yyy.com.zip 

	mv /var/www/jarkom/arjuna.yyy.com/index.php /var/www/jarkom

	rm -r /var/www/jarkom/arjuna.yyy.com


	echo 'server {

 		root /var/www/jarkom;

 		index index.php index.html index.htm;
 		server_name arjuna.D04.com;

 		location / {
 				try_files $uri $uri/ /index.php?$query_string;
 		}

 		# pass PHP scripts to FastCGI server
 		location ~ \.php$ {
 		include snippets/fastcgi-php.conf;
 		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
 		}

 	location ~ /\.ht {
 				deny all;
 		}

 		error_log /var/log/nginx/jarkom_error.log;
 		access_log /var/log/nginx/jarkom_access.log;
 	}' > /etc/nginx/sites-available/jarkom

 	ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
 	rm -rf /etc/nginx/sites-enabled/default
 	service nginx restart

 	service php7.0-fpm start
 	service php7.0-fpm restart
    ```
- Pada PrabukusumaWebServer
  ```bash
  	echo nameserver 192.168.122.1 > /etc/resolv.conf
	apt-get update
	apt-get install nginx -y
	apt-get install php php-fpm -y
	apt-get install wget -y
	apt-get install unzip -y
	php -v
	mkdir /var/www/jarkom

	echo '
 	<?php
 	echo "Halo, Kamu berada di Prabukusuma";
 	?>' > /var/www/jarkom/index.php

	wget -O /var/www/jarkom/arjuna.yyy.com.zip "https://drive.google.com/u/0/uc?id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX&export=download"

	unzip -d /var/www/jarkom /var/www/jarkom/arjuna.yyy.com.zip && rm /var/www/jarkom/arjuna.yyy.com.zip 

	mv /var/www/jarkom/arjuna.yyy.com/index.php /var/www/jarkom

	rm -r /var/www/jarkom/arjuna.yyy.com

	echo '
 	server {

 		root /var/www/jarkom;

 		index index.php index.html index.htm;
 		server_name arjuna.D04.com;

 		location / {
 			try_files $uri $uri/ /index.php?$query_string;
 		}

 		# pass PHP scripts to FastCGI server
 		location ~ \.php$ {
 		include snippets/fastcgi-php.conf;
 		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
 		}

 	location ~ /\.ht {
 		deny all;
 		}

 		error_log /var/log/nginx/jarkom_error.log;
 		access_log /var/log/nginx/jarkom_access.log;
 	}' > /etc/nginx/sites-available/jarkom

 	ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
 	rm -rf /etc/nginx/sites-enabled/default
 	service nginx restart

 	service php7.0-fpm start
 	service php7.0-fpm restart
  ```
- Pada WisanggeniWebServer
  ```bash
 	echo nameserver 192.168.122.1 > /etc/resolv.conf
	apt-get update
	apt-get install nginx -y
	apt-get install php php-fpm -y
	apt-get install wget -y
	apt-get install unzip -y

	mkdir /var/www/jarkom

	echo '
 	<?php
 	echo "Halo, Kamu berada di Wisanggeni";
 	?>
	' > /var/www/jarkom/index.php

	wget -O /var/www/jarkom/arjuna.yyy.com.zip "https://drive.google.com/u/0/uc?id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX&export=download"

	unzip -d /var/www/jarkom /var/www/jarkom/arjuna.yyy.com.zip && rm /var/www/jarkom/arjuna.yyy.com.zip 

	mv /var/www/jarkom/arjuna.yyy.com/index.php /var/www/jarkom

	rm -r /var/www/jarkom/arjuna.yyy.com

	echo '
 	server {

 		root /var/www/jarkom;

 		index index.php index.html index.htm;
 		server_name arjuna.D04.com;

 		location / {
 				try_files $uri $uri/ /index.php?$query_string;
 		}

 		# pass PHP scripts to FastCGI server
 		location ~ \.php$ {
 		include snippets/fastcgi-php.conf;
 		fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
 		}

 	location ~ /\.ht {
 				deny all;
 		}

 		error_log /var/log/nginx/jarkom_error.log;
 		access_log /var/log/nginx/jarkom_access.log;
 	}
	' > /etc/nginx/sites-available/jarkom

 	ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled
 	rm -rf /etc/nginx/sites-enabled/default
 	service nginx restart

 	service php7.0-fpm start
 	service php7.0-fpm restart
  ```
Testing pada client
```bash
lynx http://arjuna.D04.com
```
<img width="509" alt="no9 wisanggeni" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/cf591b0f-c8ab-4806-af56-5b0628ca64cd">

### Nomor 10
#### Soal
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003
#### Penyelesaian
Pada nomor 10, tambahkan port pada masing-masing web server dan pada load balancer
- Pada ArjunaLoadBalancer
  ```bash
  echo '
	upstream myweb  {
 		server 192.193.1.4:8001; #IP Abimanyu
 		server 192.193.1.5:8002; #IP Prabukusuma
		server 192.193.1.6:8003; #IP Wisanggeni
	}

	server {
 		listen 80;
 		server_name arjuna.D04.com;

 		location / {
 		proxy_pass http://myweb;
 		}
	}' > /etc/nginx/sites-available/lb-jarkom
  ```
- Pada AbhimanyuWebServer
  Tambahkan listen port 8001 pada **/etc/nginx/sites-available/jarkom**
  ```bash
  listen 8001;
  ```
- Pada PrabukusumaWebServer
  Tambahkan listen port 8002 pada **/etc/nginx/sites-available/jarkom**
  ```bash
  listen 8002;
  ```
- Pada WisanggeniWebServer
  Tambahkan listen port 8003 pada **/etc/nginx/sites-available/jarkom**
  ```bash
  listen 8003;
  ```
Testing pada client
```bash
lynx http://192.193.2.2:8001
lynx http://192.193.2.3:8002
lynx http://192.193.2.4:8003
```
<img width="518" alt="no10abimanyu" src="https://github.com/nadiramlha/Jarkom-Modul-2-D04-2023/assets/88009253/726648b7-cdce-4471-9357-a7b3abc7229b">

### no 11
#### Soal
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

#### Penyelesaian
Install Apache2 terlebih dahulu
```bash 
apt-get install apache2
service apache2 start
```

lalu copy default.conf menjadi abimanyu.D04.com.conf
```bash
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.D04.com.conf

```

setelah itu masukkan servername abimanyu.D04.com dan serveralias www.abimanyu.D04.com pada file conf dan atur root nya ke folder abimanyu.D04
```bash
echo '
<VirtualHost *:80>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/abimanyu.D04

	ServerName abimanyu.D04.com
 	ServerAlias www.abimanyu.D04.com
 
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>

' > /etc/apache2/sites-available/abimanyu.D04.com.conf

```
buat folder untuk root lalu pada folder tersebut diisi dengan file yang telah didownload dari Gdrive menggunakan wget.
```bash
mkdir /var/www/abimanyu.D04

wget -O /var/www/abimanyu.D04/abimanyu.D04.com.zip "https://drive.google.com/u/0/uc?id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc&export=download"

unzip -d /var/www/abimanyu.D04 /var/www/abimanyu.D04/abimanyu.D04.com.zip && rm /var/www/abimanyu.D04/abimanyu.D04.com.zip 

mv /var/www/abimanyu.D04/abimanyu.yyy.com/* /var/www/abimanyu.D04

rm -r /var/www/abimanyu.D04/abimanyu.yyy.com
```
Setalah itu start servernya
```bash
a2ensite abimanyu.D04.com

service apache2 restart

```


### no 12
#### Soal
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

#### Penyelesaian
pertama nyalakan modul rewrite
```bash
a2enmod rewrite
```
setelah itu mengecek jika permintaan adalah dir atau bukan, jika bukan dir maka akan melakukan rewrite rule dan akan langsung menskip index.php. Masukkan konfigurasi tersebut ke htaccesss pada folder abimanyu.D04
```bash
echo '
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-d

RewriteRule ^([^\.]+)$ index.php/$1 [NC,L]
' > /var/www/abimanyu.D04/.htaccess

```


### no 13
#### Soal
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

#### Penyelesaian
Pertama buat direktori untuk root yaitu parikesit.abimanyu.D04 dan buat juga file conf untuk parikesit.
```bash
mkdir /var/www/parikesit.abimanyu.D04
cp /etc/apache2/sites-available/abimanyu.D04.com.conf /etc/apache2/sites-available/parikesit.abimanyu.D04.com.conf
```

Setelah itu masukkan servername parikesti dan server alias www.parikesit ke file conf dan arahkan folder root ke folder parikesit.
```bash
echo '
<VirtualHost *:80>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.D04

	ServerName parikesit.abimanyu.D04.com
 	ServerAlias www.parikesit.abimanyu.D04.com
 
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>

' > /etc/apache2/sites-available/parikesit.abimanyu.D04.com.conf

```
Setelah itu isi folder root dengan folder yang telah didownload dari Gdrive parikesit.
```bash
wget -O /var/www/parikesit.abimanyu.D04/parikesit.abimanyu.yyy.com.zip "https://drive.google.com/u/0/uc?id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS&export=download"

unzip -d /var/www/parikesit.abimanyu.D04 /var/www/parikesit.abimanyu.D04/parikesit.abimanyu.yyy.com.zip && rm /var/www/parikesit.abimanyu.D04/parikesit.abimanyu.yyy.com.zip 

mv /var/www/parikesit.abimanyu.D04/parikesit.abimanyu.yyy.com/* /var/www/parikesit.abimanyu.D04
```
Karena folder secret belum ada maka saya buat folder secret sendiri menggunakan mkdir, lalu isi folder tersebut dengan sebuah html bebas.
```bash
mkdir /var/www/parikesit.abimanyu.D04/secret

echo '
<!DOCTYPE html>
<html>
<head>
    <title>Html asal</title>
</head>
<body>
    <h1>Html Bebas</h1>
    <p>Halaman Bebas.</p>
</body>
</html>

' > /var/www/parikesit.abimanyu.D04/secret/sapi.html

rm -r /var/www/parikesit.abimanyu.D04/parikesit.abimanyu.yyy.com
```
Setalah itu start servernya
```bash
a2ensite parikesit.abimanyu.D04.com

service apache2 restart

```

### no 14
#### Soal
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

#### Penyelesaian
Untuk mengatur directory listing maka harus mengedit file conf. maka edit seperti berikut :
```bash
echo '
<VirtualHost *:80>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.D04

      ServerName parikesit.abimanyu.D04.com
      ServerAlias www.parikesit.abimanyu.D04.com

```
Pada direktori public tambahkan Options +Indexes agar bisa melakukan directory listing dan pada folder secret tambahkan Options -Indexes agar pada folder secret tidak bisa melakukan directory listing.
```bash
      <Directory /var/www/parikesit.abimanyu.D04/public>
            Options +Indexes
      </Directory>

      <Directory /var/www/parikesit.abimanyu.D04/secret>
            Options -Indexes
      </Directory>
	
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>

' > /etc/apache2/sites-available/parikesit.abimanyu.D04.com.conf


a2ensite parikesit.abimanyu.D04.com

service apache2 restart
```

### no 15
#### Soal
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

#### Penyelesaian

Untuk melakukan kostumisai halaman error maka tambahkan ErrorDocument ke dalam file conf. Disini saya menggunakan halaman html error pada folder error yang didapat dari Gdrive.
```bash
echo '
<VirtualHost *:80>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.D04

	ServerName parikesit.abimanyu.D04.com
 	ServerAlias www.parikesit.abimanyu.D04.com
 
 	<Directory /var/www/parikesit.abimanyu.D04/public>
     		Options +Indexes
 	</Directory>

	<Directory /var/www/parikesit.abimanyu.D04/secret>
     		Options -Indexes
 	</Directory>
	
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
```
Disini tambahkan ErrorDocument lalu kode nya dan path ke file html yang diinginkan.
```bash
	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html

 
 </VirtualHost>

' > /etc/apache2/sites-available/parikesit.abimanyu.D04.com.conf

```
### no 16
#### Soal
Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi 
www.parikesit.abimanyu.yyy.com/js 

#### Penyelesaian
Tambahkan Alias pada file conf parikesit, dimana alias nya /js dan diarahkan ke "/var/www/parikesit.abimanyu.D04/public/js"
```bash
echo '
<VirtualHost *:80>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.D04

	ServerName parikesit.abimanyu.D04.com
 	ServerAlias www.parikesit.abimanyu.D04.com
 
        Alias "/js" "/var/www/parikesit.abimanyu.D04/public/js"

 	<Directory /var/www/parikesit.abimanyu.D04/public>
     		Options +Indexes
 	</Directory>

	<Directory /var/www/parikesit.abimanyu.D04/secret>
     		Options -Indexes
 	</Directory>
	
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined

	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html

 
 </VirtualHost>

' > /etc/apache2/sites-available/parikesit.abimanyu.D04.com.conf

```

### no 17
#### Soal
Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

#### Penyelesaian
Pertama buat folder untuk root baratayuda. lalu buat juga file conf nya.
```bash
mkdir /var/www/rjp.baratayuda.abimanyu.D04
cp /etc/apache2/sites-available/abimanyu.D04.com.conf /etc/apache2/sites-available/rjp.baratayuda.abimanyu.D04.com.conf

```
setelah itu agar bisa berjalan pada 2 port maka buat 2 virtual host dengan port masing masing 14000 dan 14400. Dengan servername dan alias yang sama pada keduanya dan root nya mengarah ke rjp.baratayuda.abimanyu.D04
```bash
echo '
<VirtualHost *:14000>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/rjp.baratayuda.abimanyu.D04

	ServerName rjp.baratayuda.abimanyu.D04.com
 	ServerAlias www.rjp.baratayuda.abimanyu.D04.com
 
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>

<VirtualHost *:14400>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/rjp.baratayuda.abimanyu.D04

	ServerName rjp.baratayuda.abimanyu.D04.com
 	ServerAlias www.rjp.baratayuda.abimanyu.D04.com
 
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>

' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.D04.com.conf

```
Lalu masukkan juga listen ke port 14000 da 14400 ke file ports.conf
```bash
echo '
Listen 80
Listen 14000
Listen 14400

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet

' > /etc/apache2/ports.conf
```
Lalu isi folder root tadi dengan folder dan file yang ada di Gdrive.
```bash
wget -O /var/www/rjp.baratayuda.abimanyu.D04/rjp.baratayuda.abimanyu.yyy.com.zip "https://drive.google.com/u/0/uc?id=1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6&export=download"

unzip -d /var/www/rjp.baratayuda.abimanyu.D04 /var/www/rjp.baratayuda.abimanyu.D04/rjp.baratayuda.abimanyu.yyy.com.zip && rm /var/www/rjp.baratayuda.abimanyu.D04/rjp.baratayuda.abimanyu.yyy.com.zip 

mv /var/www/rjp.baratayuda.abimanyu.D04/rjp.baratayuda.abimanyu.yyy.com/* /var/www/rjp.baratayuda.abimanyu.D04

rm -r /var/www/rjp.baratayuda.abimanyu.D04/rjp.baratayuda.abimanyu.yyy.com
```
Setalah itu start servernya
```bash
a2ensite rjp.baratayuda.abimanyu.D04.com

service apache2 restart

```

### no 18
#### Soal
Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

#### Penyelesaian
Masukkan kode tersebut ke file htpasswd pada folder root server baratayuda.
```bash
echo '
Wayang:$apr1$eWof.NhT$8QDgs.GtxHrmVUK0CMPBm/
' > /var/www/rjp.baratayuda.abimanyu.D04/.htpasswd

```
Lalu masukkan juga file berikut ini ke htaccess
```bash
echo '
AuthType Basic
AuthName "Authentication Required D04"
AuthUserFile /var/www/rjp.baratayuda.abimanyu.D04/.htpasswd
Require valid-user
' > /var/www/rjp.baratayuda.abimanyu.D04/.htaccess

```
Setelah itu tambahkan sedikit hal ke kedua virtual host baratayuda
```bash
echo '
<VirtualHost *:14000>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/rjp.baratayuda.abimanyu.D04

	ServerName rjp.baratayuda.abimanyu.D04.com
 	ServerAlias www.rjp.baratayuda.abimanyu.D04.com
```
Tambahkan Options +Indexes lalu AllowOverride All agar mengijinkan perubahan dan Require all granted
```bash
 	<Directory /var/www/rjp.baratayuda.abimanyu.D04>
                Options +Indexes
                AllowOverride All
                Require all granted
        </Directory>
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>

<VirtualHost *:14400>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/rjp.baratayuda.abimanyu.D04

	ServerName rjp.baratayuda.abimanyu.D04.com
 	ServerAlias www.rjp.baratayuda.abimanyu.D04.com
 
	<Directory /var/www/rjp.baratayuda.abimanyu.D04>
                Options +Indexes
                AllowOverride All
                Require all granted
        </Directory>
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>


' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.D04.com.conf
service apache2 restart

```
### no 19
#### Soal
Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

#### Penyelesaian
Tamahakan ServerAlias berupa Ip dari abimanyu ke virtual host abimanyu.D04.com
```bash
echo '
<VirtualHost *:80>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/abimanyu.D04

	ServerName abimanyu.D04.com
 	ServerAlias www.abimanyu.D04.com
	ServerAlias 192.193.1.4
 
 	<Directory /var/www/abimanyu.D04>
		Options +FollowSymLinks -Multiviews
		AllowOverride All
	</Directory>
	

    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
</VirtualHost>
' > /etc/apache2/sites-available/abimanyu.D04.com.conf
service apache2 restart

```

### no 20
#### Soal
Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

#### Penyelesaian
Tambahkan AllowOverride All ke folder parikesit.abimanyu.D04
```bash
echo '
<VirtualHost *:80>
    	
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.D04

	ServerName parikesit.abimanyu.D04.com
 	ServerAlias www.parikesit.abimanyu.D04.com

	<Directory /var/www/parikesit.abimanyu.D04>
     		AllowOverride All
 	</Directory>
 
 	<Directory /var/www/parikesit.abimanyu.D04/public>
     		Options +Indexes
		AllowOverride All
 	</Directory>

	<Directory /var/www/parikesit.abimanyu.D04/secret>
     		Options -Indexes
 	</Directory>
	
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined

	ErrorDocument 404 /error/404.html
	ErrorDocument 403 /error/403.html
```
Lalu tambahkan rewrite on dan cek jika permintaan mengandung abimanyu maka permintaan akan ditulis ulang menggunakan rewrite rule  yang mengarah ke file abimanyu.png
```bash
	RewriteEngine On

	# Arahkan permintaan yang mengandung substring "abimanyu" ke URL yang ditentukan
	RewriteCond %{REQUEST_URI} abimanyu
	RewriteRule .* http://parikesit.abimanyu.D04.com/public/images/abimanyu.png [R,L]

 
 </VirtualHost>

' > /etc/apache2/sites-available/parikesit.abimanyu.D04.com.conf

```
