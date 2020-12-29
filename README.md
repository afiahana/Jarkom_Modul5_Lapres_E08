# Jarkom_Modul5_Lapres_E08

# Persiapan
## A. Membuat topologi jaringan sesuai soal
- Buat file dengan nama ```topologi.sh```
- ![foto 1](img5/A.jpg)

## B. Melakukan Subnetting dengan metode VLSM
- Pembagian Subnet dengan metode VLSM
- ![foto 1](img5/B1.jpg)
- IP Tree
- ![foto 1](img5/B2.jpg)

## C. Melakukan Routing
Melakukan Routing pada UML Surabaya
- ![foto 1](img5/C.jpg)

## D. Memberikan ip pada subnet SIDOARJO dan GRESIK secara dinamis menggunakan bantuan DHCP SERVER serta setting DHCP RELAY pada router yang menghubungkannya
- Pada UML Surabaya, Batu, dan Kediri -> Buka file ```nano /etc/sysctl.conf```
- Ubah agar menjadi seperti gambar di bawah ini
- ![foto 1](img5/D1.jpg)
- Jalankan ```sysctl -p```
- Install DHCP RELAY ```apt-get install isc-dhcp-relay -y```
- Buka file ```nano /etc/default/isc-dhcp-relay```
- Ubah agar menjadi seperti gambar di bawah ini
- ![foto 1](img5/D3.jpg)

- Pada UML Mojokerto, Install DHCP ```apt-get install isc-dhcp-server -y```
- Buka file ```nano /etc/default/isc-dhcp-server```
- Tambahkan ```INTERFACES="eth0"```
- Buka file ```nano /etc/dhcp/dhcpd.conf```
- Ubah agar menjadi seperti gambar di bawah ini
- ![foto 1](img5/D2.jpg)

# Soal
## 1
- Pada UML Surabaya, jalankan ```iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j SNAT --to-source 10.151.70.38```
- Cek dengan ```ping its.ac.id``-> Jika bisa maka nomor 1 berhasil

## 2
- Pada UML Surabaya, jalankan ```iptables -A FORWARD -d 10.151.71.72/29 -i eth0 -p tcp --dport 22 -j DROP```
- Install netcat di UML Mojokerto dan UML Malang ```apt-get install netcat```
- Pada putty, jalankan ```nc 10.151.77.131 22```
- Pada UML Mojokerto dan Malang, jalankan ```nc -l -p <nomor_port>```
- ![foto 1](img5/2.jpg)

## 3
- Pada UML Mojokerto dan UML Malang, jalankan ```iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP```
- Melakukan ping ke IP MALANG atau IP MOJOKERTO di 4 UML
- Dinyatakan berhasil jika di UML ke 4 tidak bisa melakukan ping
- ![foto 1](img5/3.jpg)

## 4
- Pada UML Malang, jalankan
```
iptables -A INPUT -s 192.168.2.0/24 -m time --weekdays Sat,Sun -j DROP
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT 
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 17:01 --timestop 00:00 --weekdays Mon,Tue,Wed,Thu,Fri -j DROP
iptables -A INPUT -s 192.168.2.0/24 -m time --timestart 00:01 --timestop 06:59 --weekdays Mon,Tue,Wed,Thu,Fri -j DROP
```
- Pada UML Malang, jalankan ```date --set="2020/12/26 06:00"```
- ![foto 1](img5/4.jpg)

## 5
- Pada UML Malang, jalankan
```
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 17:00 --timestop 00:00 -j ACCEPT 
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 00:00 --timestop 08:00 -j ACCEPT
iptables -A INPUT -s 192.168.1.0/24 -m time --timestart 08:01 --timestop 16:59 -j DROP
```
- Pada UML Malang, jalankan ```date --set="2020/12/27 09:00"```
- ![foto 1](img5/5.jpg)

## 6
- Pada UML Surabaya, jalankan
```
iptables -t nat -A PREROUTING -p tcp -d 10.151.71.74 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.0.11:80 #Probolinggo
iptables -t nat -A PREROUTING -p tcp -d 10.151.71.74 -j DNAT --to-destination 192.168.0.10:80 #Madiun
iptables -t nat -A POSTROUTING -p tcp -d 192.168.0.11 --dport 80 -j SNAT --to-source 10.151.71.74:80 #Probolinggo
iptables -t nat -A POSTROUTING -p tcp -d 192.168.0.10 --dport 80 -j SNAT --to-source 10.151.71.74:80 #Madiun
```
- Install netcat pada UML Malang, UML Probolinggo, UML Madiun, UML Surabaya, dan UML Gresik (Client) ```apt-get install netcat```
- Pada UML Madiun, jalankan ```nc -l -p 80```
- Pada UML Probolinggo, jalankan ```nc -l -p 80```
- Pada UML Client (Gresik), jalankan ```nc ipMalang 80```
- Ketik bebas apapun di UML Gresik nanti muncul di UML Madiun/ Probolinggo
- ![foto 1](img5/6.jpg)

## 7
- Pada UML Surabaya, jalankan
```
iptables -A FORWARD -d 10.151.71.72/29 -i eth0 -p tcp --dport 22 -j LOG --log-prefix "FORWARD TCP-DROPPED: "
iptables -A FORWARD -d 10.151.71.72/29 -i eth0 -p tcp --dport 22 -j DROP
```
- Pada UML Malang dan UML Mojokerto, jalankan
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOG --log-prefix "Connection Limit --> DROPPED : "
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
- Cara testing
```
di UML --> nc -l -p <nomor_port>
di WSL --> nc <ip_malang/mojo> <nomor_port>
```
- Hasil akhir
- ![foto 1](img5/7A.jpg)
- ![foto 1](img5/7B.jpg)
