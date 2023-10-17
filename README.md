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
