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
