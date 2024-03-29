# Jarkom_Modul5_Lapres_E07
Praktikum Modul 5 Jarkom 2020<br/>
05111840000051 Juwita Kartika Rani<br/>
05111840000115 Muhammad Rafi Yudhistira<br/>
05111840007004 Siti Munawaroh<br/>

### A. Topologi Jaringan
![topo](https://user-images.githubusercontent.com/56763570/103144380-543e0700-46dd-11eb-9835-02f1bf23c952.JPG)

Berdasarkan gambar topologi tersebut, berikut file **topologi.sh**:
```
# Switch
uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &
uml_switch -unix switch3 > /dev/null < /dev/null &
uml_switch -unix switch4 > /dev/null < /dev/null &
uml_switch -unix switch5 > /dev/null < /dev/null &
uml_switch -unix switch0 > /dev/null < /dev/null &


# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.70.33 eth1=daemon,,,switch0 eth2=daemon,,,switch3 mem=96M &
xterm -T BATU -e linux ubd0=BATU,jarkom umid=BATU eth0=daemon,,,switch3 eth1=daemon,,,switch5 eth2=daemon,,,switch2 mem=96M &
xterm -T KEDIRI -e linux ubd0=KEDIRI,jarkom umid=KEDIRI eth0=daemon,,,switch1 eth1=daemon,,,switch4 eth2=daemon,,,switch0 mem=96M &

# Server
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=128M &
xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch1 mem=128M &
xterm -T PROBOLINGGO -e linux ubd0=PROBOLINGGO,jarkom umid=PROBOLINGGO eth0=daemon,,,switch1 mem=128M &

# Klien
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch5 mem=96M &
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch4 mem=96M &
```
### B. Subnetting VSLM
![vslm](https://user-images.githubusercontent.com/56763570/103144518-6b7df400-46df-11eb-9a2f-864e6e98da19.jpg)

![modul4-modul 5 (2)](https://user-images.githubusercontent.com/56763570/103144673-55256780-46e2-11eb-93a9-91fecd0c1e24.png)

Untuk router **SURABAYA**, **BATU**, dan **KEDIRI** di-uncomment dengan perintah ```net.ipv4.ip_forward=1``` . Dengan cara mengetikkan ```nano /etc/sysctl.conf``` kemudian melakukan edit, dan mengetikkan ```sysctl -p```.

Kemudian melakukan setting pada file ```/etc/network/interfaces``` pada setiap UML masing-masing sebagai berikut:
**SURABAYA (Sebagai Router)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.70.34
netmask 255.255.255.252
gateway 10.151.70.33

auto eth1
iface eth1 inet static
address 192.168.0.1
netmask 255.255.255.252

auto eth2
iface eth2 inet static
address 192.168.0.5
netmask 255.255.255.252
```
**KEDIRI (Sebagai Router dan DHCP Relay)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.2
netmask 255.255.255.252
gateway 192.168.0.1

auto eth1
iface eth1 inet static
address 192.168.0.9
netmask 255.255.255.248

auto eth2
iface eth2 inet static
address 192.168.1.1
netmask 255.255.255.0
```
**BATU (Sebagai Router dan DHCP Relay)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.6
netmask 255.255.255.252
gateway 192.168.0.5

auto eth1
iface eth1 inet static
address 10.151.71.65
netmask 255.255.255.248

auto eth2
iface eth2 inet static
address 192.168.2.1
netmask 255.255.255.0
```
**MALANG (Sebagai DNS Server)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.71.66
netmask 255.255.255.248
gateway 10.151.71.65
```
**MOJOKERTO (Sebagai DHCP Server)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.71.67
netmask 255.255.255.248
gateway 10.151.71.65
```
**MADIUN (Sebagai Web Server)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.10
netmask 255.255.255.248
gateway 192.168.0.9
```
**PROBOLINGGO (Sebagai Web Server)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.0.11
netmask 255.255.255.248
gateway 192.168.0.9
```
**GRESIK (Sebagai Client)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.2
netmask 255.255.255.0
gateway 192.168.1.1
```
**SIDOARJO (Sebagai Client)**
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.2.2
netmask 255.255.255.0
gateway 192.168.2.1
```
Kemudian melakukan restart networking-nya dengan perintah ```service networking restart```.

### C. Routing
Berikut routing berdasarkan perhitungan degan VLSM yang dijalankan di UML SURABAYA
```
ip route add 192.168.1.0/24 via 192.168.0.2
ip route add 192.168.2.0/24 via 192.168.0.6
ip route add 192.168.0.8/29 via 192.168.0.2
ip route add 10.151.71.64/29 via 192.168.0.6
```
Disimpan pada file bash misalkan dengan nama **route.sh** kemudian untuk menjalankannya dengan perintah ```source route.sh```.

### D. DHCP Server-Relay (IP GRESIK & SIDOARJO)
Pada **MOJOKERTO** menjadi DHCP Server dengan meng-instal  ```apt-get install isc-dhcp-server```, sedangkan **KEDIRI** dan **BATU** yang akan menjadi DHCP Relay, perlu diinstallkan dengan perintah ```apt-get install isc-dhcp-relay```.

Sedangkan untuk klien **GRESIK** dan **SIDOARJO** akan diedit yng awalnya static menjadi dhcp pada file ```/etc/network/interfaces``` menjadi sebagai berikut:
```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp
```
Kemudian pada **MOJOKERTO** (DHCP Server), diedit pada file ```/etc/default/isc-dhcp-server```, ditambahkan interface ```eth0``` untuk INTERFACESv4.

Kemudian diedit juga pada file ```/etc/dhcp/dhcpd.conf``` sebagai berikut:

```
subnet 10.151.71.64 netmask 255.255.255.248 {  
  option routers 10.151.71.65;
	option broadcast-address 10.151.71.71;
}
subnet 192.168.1.0 netmask 255.255.255.0 {
	range 192.168.1.2 192.168.1.213;
	option routers 192.168.1.1;
	option broadcast-address 192.168.1.255;
	option domain-name-servers 10.151.71.66; #malang
	option domain-name-servers 202.46.129.2;
	default-lease-time 600;
	max-lease-time 7200;
}
subnet 192.168.2.0 netmask 255.255.255.0 {
	range 192.168.2.2 192.168.2.203;
	option routers 192.168.2.1;
	option broadcast-address 192.168.2.255;
	option domain-name-servers 10.151.71.66; #malang
	option domain-name-servers 202.46.129.2;
	default-lease-time 600;
	max-lease-time 7200;
}
```
kemudian lakukan restart dengan ```service isc-dhcp-server restart``` untuk server (**MOJOKERTO**) dan ```service isc-dhcp-relay restart``` (**KEDIRI** dan **BATU**). selain itu lakukan perintah pada klien **GRESIK** dan **SIDOARJO**, ```service networking restart```. Dapat dicek dengan perintah ```ip a```.

### 1. SURABAYA Dapat Mengakses Keluar Tanpa MASQUERADE
Perintah iptables di **SURABAYA** sebagai berikut.
```
iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j SNAT --to-source 10.151.70.34
```
### 2. Akses SSH di luar topologi akan di-DROP ketika mengakses server yang memiliki IP DMZ (lakukan setting di SURABAYA)
Perintah iptables di **SURABAYA** sebagai berikut.
```
iptables -A FORWARD -d 10.151.71.88/29 -i eth0 -p tcp --dport 22 -j DROP
```
### 3. DHCP Server dan DNS Server maksimal menerima 3 koneksi ICMP secara bersamaan, selebihnya di-DROP 
Ditambahkan perintah iptables sebagai berikut di **MALANG (DNS Server)** dan **MOJOKERTO (DHCP Server)**.
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP 
```
### 4 SIDOARJO diberikan waktu akses untuk mengakses server MALANG:
**SIDOARJO = 07:00 - 17:00 (Senin - Jumat)**

Ditambahkan perintah iptables sebagai berikut di **MALANG**:
```
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 07:00 --timestop 17:00 --days Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 192.168.2.0/24 -j REJECT
```
### 5 GRESIK diberikan waktu akses untuk mengakses server MALANG:
**GRESIK = 17:00 - 07:00 (Setiap Hari)**

Ditambahkan perintah iptables sebagai berikut di **MALANG**:
```
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 07:00 --timestop 17:00 -j REJECT
```
### 6 SURABAYA disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada PROBOLINGGO port 80 dan MADIUN port 80
Ditambahkan perintah iptables sebagai berikut di **SURABAYA**:
```
iptables -A PREROUTING -t nat -d 10.151.71.66 -p tcp --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.0.10:80
iptables -A PREROUTING -t nat -d 10.151.71.66 -p tcp --dport 80 -j DNAT --to-destination 192.168.0.11:80
```

### 7 semua paket didrop oleh firewall (dalam topologi) tercatat dalam log pada setiap UML yang memiliki aturan drop
Ditambahkan perintah iptables sebagai berikut di **SURABAYA**:
```
iptables -N LOGGING 
iptables -A FORWARD -j LOGGING 
iptables -A LOGGING -m limit --limit 2/min -j LOG --log-prefix "IP Tables Packet Dropped: " --log-level 4 
iptables -A LOGGING -j DROP
```
Ditambahkan perintah iptables sebagai berikut di **MALANG** dan **MOJOKERTO**:
```
iptables -N LOGGING
iptables -A INPUT -j LOGGING
iptables -A OUTPUT -j LOGGING
iptables -A LOGGING -j LOG --log-prefix "IP Tables Packet Dropped: " --log-level 4
iptables -A LOGGING -j DROP
```

ini file lama
