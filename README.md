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
