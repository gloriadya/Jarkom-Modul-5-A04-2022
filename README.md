# Jarkom-Modul-5-A04-2022

**Nama Anggota Kelompok**

- Samuel (5025201187)
- Mohamad Kholid Bughowi (5025201253)
- Gloria Dyah Pramesti (5025201033)

## Konfigurasi

### Strix
```bash
auto eth0
iface eth0 inet static
	address 192.168.122.11
	netmask 255.255.255.0
        gateway 192.168.122.1
        up echo nameserver 192.168.122.1 > /etc/resolv.conf

auto eth1
iface eth1 inet static
address 10.1.0.109
netmask 255.255.255.252

auto eth2
iface eth2 inet static
address 10.1.0.105
netmask 255.255.255.252
```

### Ostania
```bash
auto eth0
iface eth0 inet static
address 10.1.0.110
netmask 255.255.255.252
gateway 10.1.0.109
up echo nameserver 192.168.122.1 > /etc/resolv.conf

auto eth1
iface eth1 inet static
address 10.1.2.1
netmask 255.255.254.0

auto eth2
iface eth2 inet static
address 10.1.0.121
netmask 255.255.255.248

auto eth3
iface eth3 inet static
address 10.1.1.1
netmask 255.255.255.0
```

### Westalis
```bash
auto eth0
iface eth0 inet static
address 10.1.0.106
netmask 255.255.255.252
gateway 10.1.0.105
up echo nameserver 192.168.122.1 > /etc/resolv.conf

auto eth1
iface eth1 inet static
address 10.1.4.1
netmask 255.255.252.0

auto eth2
iface eth2 inet static
address 10.1.0.129
netmask 255.255.255.128

auto eth3
iface eth3 inet static
address 10.1.0.113
netmask 255.255.255.248
```

### Wise
```bash
auto eth0
iface eth0 inet static
address 10.1.0.115
netmask 255.255.255.248
gateway 10.1.0.113
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Eden
```bash
auto eth0
iface eth0 inet static
address 10.1.0.114
netmask 255.255.255.248
gateway 10.1.0.113
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Garden
```bash
auto eth0
iface eth0 inet static
address 10.1.0.122
netmask 255.255.255.248
gateway 10.1.0.121
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### SSS
```bash
auto eth0
iface eth0 inet static
address 10.1.0.123
netmask 255.255.255.248
gateway 10.1.0.121
up echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Semua Client
```bash
auto eth0
iface eth0 inet dhcp
```



## Routing

### Strix
```bash
route add -net 10.1.0.112 netmask 255.255.255.248 gw 10.1.0.106
route add -net 10.1.0.128 netmask 255.255.255.128 gw 10.1.0.106
route add -net 10.1.4.0 netmask 255.255.252.0 gw 10.1.0.106

route add -net 10.1.0.120 netmask 255.255.255.248 gw 10.1.0.110
route add -net 10.1.1.0 netmask 255.255.255.0 gw 10.1.0.110
route add -net 10.1.2.0 netmask 255.255.254.0 gw 10.1.0.110
```

## Setting DHCP Server pada Wise

### 1. Install dependensi
```bash
apt-get update
apt-get install isc-dhcp-server -y
apt-get install netcat -y
```

### 2. Konfigurasi dhcp nya 
```bash
echo 'INTERFACES="eth0"' >> /etc/default/isc-dhcp-server
```

### 3. Konfigurasi untuk client
```bash
echo '
# blackbell
subnet 10.1.2.0 netmask 255.255.254.0 {
    range 10.1.2.2 10.1.3.254;
    option routers 10.1.2.1;
    option broadcast-address 10.1.3.255;
    option domain-name-servers 10.1.0.114;
    default-lease-time 360;
    max-lease-time 7200;
}

# Briar
subnet 10.1.1.0 netmask 255.255.255.0 {
    range 10.1.1.2 10.1.1.254;
    option routers 10.1.1.1;
    option broadcast-address 10.1.1.255;
    option domain-name-servers 10.1.0.114;
    max-lease-time 7200;
}

# Forger 
subnet 10.1.0.128 netmask 255.255.255.128 {
    range 10.1.0.130 10.1.0.254;
    option routers 10.1.0.129;
    option broadcast-address 10.1.0.255;
    option domain-name-servers 10.1.0.114;
    default-lease-time 360;
    max-lease-time 7200;
}

# Desmon
subnet 10.1.4.0 netmask 255.255.252.0 {
    range 10.1.4.2 10.1.7.254;
    option routers 10.1.4.1;
    option broadcast-address 10.1.7.255;
    option domain-name-servers 10.1.0.114;
    default-lease-time 360;
    max-lease-time 7200;
}
' > /etc/dhcp/dhcpd.conf
```

### 4. Restart dhcp
```bash
service isc-dhcp-server start
```

## Setting DHCP Relay (Westalis, Ostania, Strix)

### 1. Install dependensi
```bash
apt-get update
apt-get install isc-dhcp-relay -y
apt-get install netcat -y
```

### 2. Konfigurasi dhcp relay pada Westalis dan Ostania
```bash
echo '
SERVERS="10.1.0.115"
INTERFACES="eth0 eth1 eth2 eth3"
OPTIONS=""
' > /etc/default/isc-dhcp-relay
```

## Setting DNS Pada Eden

### 1. Install dependensi
```bash
apt-get update
apt-get install bind9 -y
service bind9 start
```

### 2. Konfigurasi kan dns
```bash
echo 'options {
        directory "/var/cache/bind";

        forwarders{
                10.1.0.106;
        };

        //dnssec-validation auto;
        allow-query{any;};

        auth-nxdomain no;     # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options
```

### 3. Restart dhcp
```bash
service bind9 restart
```


## Soal 1
Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.

### Penjelasan
Karena tidak boleh `MASQUERADE`, berarti gunakan `SNAT` untuk custom routes.

Jalankan pada `Strix`
```bash
iptables -t nat -A POSTROUTING -s 10.1.0.0/21 -o eth0 -j SNAT --to-source 192.168.122.11
```


## Soal 2
Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan.

### Penjelasan
Buat firewall pada `Strix` yang mengarah kepada IP dari `WISE`

Jalankan pada `Strix`
```bash
iptables -A FORWARD -p tcp -d 10.1.0.115 -i eth0 -j DROP 
iptables -A FORWARD -p udp -d 10.1.0.115 -i eth0 -j DROP
```
