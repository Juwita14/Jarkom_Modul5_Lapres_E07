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

## C. Routing
Berikut routing berdasarkan perhitungan degan VLSM
```
ip route add 192.168.1.0/24 via 192.168.0.2
ip route add 192.168.2.0/24 via 192.168.0.6
ip route add 192.168.0.8/29 via 192.168.0.2
ip route add 10.151.71.64/29 via 192.168.0.6
```
Disimpan pada file bash misalkan dengan nama **route.sh** kemudian untuk menjalankannya dengan perintah ```source route.sh```.

## D. DHCP Server-Relay (IP GRESIK & SIDOARJO)
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






Kemudian melakukan perintah pada klien **GRESIK** dan **SIDOARJO**, ```service networking restart```. Dapat dicek dengan perintah ```ifconfig```.
