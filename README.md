# Jarkom-Modul-2-B14-2023

## Kontributor

| Nama Kontributor | NRP |
|------------------|-----|
| Gabrielle Immanuel Osvaldo Kurniawan | 5025211135 |
| Muhammad Arkan Karindra Darvesh | 5025211236 |


## Topologi
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/Topologi.png?raw=true)

## Configure 
- **Router**
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 192.185.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 192.185.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 192.185.3.1
    netmask 255.255.255.0
```
- **Yudhistira**
```
auto eth0
iface eth0 inet static
    address 192.185.2.2
    netmask 255.255.255.0
    gateway 192.185.2.1
```
- **Werkudara**
  ```
  auto eth0
  iface eth0 inet static
      address 192.185.2.3
      netmask 255.255.255.0
    gateway 192.185.2.1
  ```
- **Nakula**
  ```
  auto eth0
  iface eth0 inet static
      address 192.185.1.2
      netmask 255.255.255.0
    gateway 192.185.1.1
  ```
- **Sadewa**
    ```
    auto eth0
  iface eth0 inet static
      address 192.185.1.3
      netmask 255.255.255.0
      gateway 192.185.1.1
  ```
- **Arjuna**
  ```
    auto eth0
  iface eth0 inet static
      address 192.185.1.4
      netmask 255.255.255.0
      gateway 192.185.1.1
  ```
- **Prabukusuma**
  ```
  auto eth0
  iface eth0 inet static
      address 192.185.3.2
      netmask 255.255.255.0
    gateway 192.185.3.1
  ```
- **Abimanyu**
    ```
    auto eth0
  iface eth0 inet static
      address 192.185.3.3
      netmask 255.255.255.0
    gateway 192.185.3.1
  ```
- **Wisanggeni**
  ```
  auto eth0
  iface eth0 inet static
          address 192.185.3.4
          netmask 255.255.255.0
          gateway 192.185.3.1
  ```
- **IP Setiap Server**
  ```
  (Switch 2)
  Router	          : 192.185.2.1 
  Yudhistira    : 192.185.2.2
  Werkudara   : 192.185.2.3
  (Switch 1)
  Router	    : 192.185.1.1 
  Nakula	    : 192.185.1.2 
  Sadewa	    : 192.185.1.3
  Arjuna            : 192.185.1.4
  (Switch 3)
  Router        : 192.185.3.1 
  Prabukusuma  	: 192.185.3.2
  Abimanyu	 : 192.185.3.3
  Wisanggeni  : 192.185.3.4
  ```

### Sebelum memulai 
setiap node, kita inisiasi pada Router di file `start.sh` menggunakan `sh` atau `bash`
```
    ip a
    iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.185.0.0/16
    echo 'nameserver 192.168.122.1' > /etc/resolv.conf
```
Set Up dengan run file `install.sh` masing-masing server.
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
  apt-get update
  apt-get install bind9 -y      
```

## Soal 1 
> Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut


### Script
**server selain DNS Master**
Jalankan file `Test_1.sh`
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
ping google.com -c 5
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no1.png?raw=true)


## Soal 2 
> Buatlah website utama dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.
Config


### Script

Pada node DNS Master, kita perlu melakukan setup terlebih dahulu sebagai berikut 

**Yudhistira**
```
echo 'zone "arjuna.b14.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.b14.com";
        allow-transfer { 192.185.1.4; }; // IP Arjuna
};' > /etc/bind9/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/arjuna.b14.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.b14.com. root.arjuna.b14.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.b14.com.
@       IN      A       192.185.1.2     ; IP Yudhistira
www     IN      CNAME   arjuna.b14.com.' > /etc/bind/jarkom/arjuna.b14.com

service bind9 restart
```
Setelah itu tinggal melakukan tes di server lain, `bash` file `Test_2.sh`
**Nakula**
Jangan lupa untuk setup nameserver terlebih dahulu yang diarahkan ke `IP Node yudhistira`
```
ping arjuna.b14.com -c 5
ping www.arjuna.b14.com -c 5
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no2.png?raw=true)

## Soal 3
> Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.


### Script

Pada node DNS Master, kita perlu melakukan setup terlebih dahulu sebagai berikut 

**Yudhistira**
```
echo 'zone "arjuna.b14.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.b14.com";
        allow-transfer { 192.185.1.4; }; // IP Arjuna
};

zone "abimanyu.b14.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.b14.com";
        allow-transfer { 192.185.3.3; }; // IP Abimanyu
};' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.b14.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.b14.com. root.abimanyu.b14.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.b14.com.
@       IN      A       192.185.2.2     ; IP Yudhistira
www     IN      CNAME   abimanyu.b14.com.' > /etc/bind/jarkom/abimanyu.b14.com

service bind9 restart
```

**Nakula**
bash Test_3
Jangan lupa untuk setup nameserver terlebih dahulu yang diarahkan ke `IP Node yudhistira`
```
ping abimanyu.b14.com -c 5
ping www.abimanyu.b14.com -c 5
```


### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no3.png?raw=true)

## Soal 4
> Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.


### Script

**Yudhistira**
Cukup menambahkan ``parikesit IN    A       192.185.3.3     ; IP Abimanyu' > /etc/bind/jarkom/abimanyu.b14.com`` saja pada DNS Master.

```
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.b14.com. root.abimanyu.b14.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.b14.com.
@       IN      A       192.185.2.2     ; IP Yudhistira
www   b14N      CNAME   abimanyu.b14.com.
parikesit IN    A       192.185.3.3     ; IP Abimanyu' > /etc/bindb14rkom/abimanyu.b14.com

service bind9 restart
```

**Nakula**
Bash Test_4
```
ping parikesit.abimanyu.b14.com -c 5
```

### Result

![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no4.png?raw=true)

## Soal 5
> Buat juga reverse domain untuk domain utama.


### Script
Untuk melakukan reverse domain. Kita perlu untuk mengetahui `IP` dari `Abimanyu`. Karena `IP Abimanyu` 

**Yudhistira**
```
echo 'zone "3.185.192.in-addr.arpa" {
        type master;
        file "/etc/bind/jarkom/3.185.192.in-addr.arpa";
};' > /etc/bind/named.conf.local

cp /etc/bind/db.local /etc/bind/jarkom/3.185.192.in-addr.arpa

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.b14.com. root.abimanyu.b14.com. (
                        2003101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.185.192.in-addr.arpa. IN      NS      abimanyu.b14.com.
3                       IN      PTR     abimanyu.b14.com.
5                       IN      PTR     arjuna.b14.com.' > /etc/bind/jarkom/3.185.192.in-addr.arpa

service bind9 restart
```

**Abimanyu / Client yang lain**

Sebelum mengakses, jangan lupa untuk mengembalikan `nameserver` ke DNS Master
```
host -t PTR 192.185.3.3
```

### Result

![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no5.png?raw=true)

## Soal 6
> Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.


### Script
**Yudhistira**

Pada `DNS Master` diperlukan setup `also-notify` dan `allow-transfer` agar memberikan izin kepada `IP` yang dituju. -`masterslave.sh`
```
echo 'zone "arjuna.b14.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.b14.com";
        allow-transfer { 192.185.2.3; }; // IP Werkudara
};

zone "abimanyu.b14.com" {
        type master;
        notify yes;
        also-notify { 192.185.2.3; }; // IP Werkudara
        allow-transfer { 192.185.2.3; }; // IP Werkudara
        file "/etc/bind/jarkom/abimanyu.b14.com";
};

zone "3.185.192.in-addr.arpa" {
        type master;
        file "/etc/bind/jarkom/3.185.192.in-addr.arpa";
};' > /etc/bind/named.conf.local


service bind9 restart
```

**Werkudara (DNS Slave)**
	-`configslave.sh`
```
echo 'zone "abimanyu.b14.com" {
    type slave;
    masters { 192.185.1.2; }; // Masukan IP Yudhistira
    file "/var/lib/bind/abimanyu.b14.com";
};' >> /etc/bind/named.conf.local

service bind9 restart
```
**Yudhistira**
	-`stopservice.sh`
```
service bind9 stop
```
**Nakula**
	-`Test_6.sh`
```
ping abimanyu.b14.com -c 5
ping www.abimanyu.b14.com -c 5
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no6.png?raw=true)


## Soal 7
> Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda


### Script
**Yudhistira**
-`abianyubaratayuda.sh`
```
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.b14.com. root.abimanyu.b14.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      abimanyu.b14.com.
@       IN      A       192.185.2.2     ; IP Yudhistira
www     IN      CNAME   abimanyu.b14.com.
parikesit IN    A       192.185.3.3     ; IP Abimanyu
ns1     IN      A       192.185.2.3     ; IP Werkudara
baratayuda IN   NS      ns1' > /etc/bind/jarkom/abimanyu.b14.com

echo "options {
    directory \"/var/cache/bind\";

    // If there is a firewall between you and nameservers you want
    // to talk to, you may need to fix the firewall to allow multiple
    // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

    // If your ISP provided one or more IP addresses for stable
    // nameservers, you probably want to use them as forwarders.
    // Uncomment the following block, and insert the addresses replacing
    // the all-0's placeholder.

    // forwarders {
    //      0.0.0.0;
    // };

    //========================================================================
    // If BIND logs error messages about the root key being expired,
    // you will need to update your keys.  See https://www.isc.org/bind-keys
    //========================================================================
    //dnssec-validation auto;

    allow-query { any; };
    auth-nxdomain no;
    listen-on-v6 { any; };
};" > /etc/bind/named.conf.options

service bind9 restart
```

**Werkudara**
-`baratayuda.sh`
Pada `DNS Slave` Kita perlu untuk mengarahkan `zone` ke `DNS Master` agar authoritative tadi dapat jalan. Kita juga perlu mengaktifkan ``allow-query { any; };`` pada `DNS Slave`
```
echo "options {
    directory \"/var/cache/bind\";

    // If there is a firewall between you and nameservers you want
    // to talk to, you may need to fix the firewall to allow multiple
    // ports to talk.  See http://www.kb.cert.org/vuls/id/800113

    // If your ISP provided one or more IP addresses for stable
    // nameservers, you probably want to use them as forwarders.
    // Uncomment the following block, and insert the addresses replacing
    // the all-0's placeholder.

    // forwarders {
    //      0.0.0.0;
    // };

    //========================================================================
    // If BIND logs error messages about the root key being expired,
    // you will need to update your keys.  See https://www.isc.org/bind-keys
    //========================================================================
    //dnssec-validation auto;

    allow-query { any; };
    auth-nxdomain no;
    listen-on-v6 { any; };
};" > /etc/bind/named.conf.options


echo 'zone "baratayuda.abimanyu.b14.com" {
        type master;
        file "/etc/bind/baratayuda/baratayuda.abimanyu.b14.com";
};' >> /etc/bind/named.conf.local

mkdir /etc/bind/baratayuda

cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.b14.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.b14.com. root.baratayuda.abimanyu.b14.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.b14.com.
@       IN      A       192.185.3.3     ; IP Abimanyu
www     IN      CNAME   baratayuda.abimanyu.b14.com.' > /etc/bind/baratayuda/baratayuda.abimanyu.b14.com

service bind9 restart
```

**Nakula**
	-`Test_7.sh`
```
ping baratayuda.abimanyu.b14.com -c 5
ping www.baratayuda.abimanyu.b14.com -c 5
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no7.png?raw=true)

## Soal 8
> Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.


### Script
**Werkudara**
-`rjp.sh`
```
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.b14.com. root.baratayuda.abimanyu.b14.com. (
                        2023101001      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      baratayuda.abimanyu.b14.com.
@               IN      A       192.185.3.3     ; IP Abimanyu
www             IN      CNAME   baratayuda.abimanyu.b14.com.
rjp             IN      A       192.185.3.3     ; IP Abimanyu
www.rjp         IN      CNAME   rjp.baratayuda.abimanyu.b14.com.' > /etc/bind/baratayuda/baratayuda.abimanyu.b14.com

service bind9 restart
```

**Nakula**
	-`Test_8.sh`
```
ping rjp.baratayuda.abimanyu.b14.com -c 5
ping www.rjp.baratayuda.abimanyu.b14.com -c 5
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no8.png?raw=true)

## Soal 9
> Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker


### Script
**Arjuna Load Balancer**
	-`setLB.sh`
Disini kita perlu untuk membuat load balancer nya sebagai berikut 
```
echo 'upstream backend {
  server 192.185.3.2; # IP PrabuKusuma
  server 192.185.3.3; # IP Abimanyu
  server 192.185.3.4; # IP Wisanggeni
}

server {
  listen 80;
  server_name arjuna.b14.com www.arjuna.b14.com;

  location / {
    proxy_pass http://backend;
  }
}
' > /etc/nginx/sites-available/jarkom
ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

service nginx restart
```
**Prabukusuma, abimanyu, wisangeni**
	-`setworker.sh`
```
mkdir -p /var/www/jarkom
echo '<?php
echo "Halo kamu berada di Prabukusuma";
?>'> /var/www/jarkom/index.php

echo 'server {
        listen 80;

        root /var/www/jarkom;
        index index.php index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }
}' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

rm /etc/nginx/sites-enabled/default

service php7.0-fpm start
service php7.0-fpm restart
service nginx restart
```
Kita jalankan di client server (nakula/sadewa)
**Nakula**
	-`Test_9.sh`
```
lynx http://192.185.3.2
lynx http://192.185.3.3
lynx http://192.185.3.4
```


### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no9.png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no9_1.png?raw=true)

## Soal 10
> Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh (Prabakusuma:8001, Abimanyu:8002, Wisanggeni:8003)

Melakukan penyesuaian port pada server-server tersebut sesuai dengan petunjuk yang telah diberikan terlebi dahulu. Karena telah berhasil melakukan deployment pada [nomor 9](#soal-9). Hanya perlu mengubah masing-masing port pada worker menuju port yang telah ditentukan yaitu `Prabakusuma:8001, Abimanyu:8002, Wisanggeni:8003`. Kita juga perlu mengubah port `load-balancing` dengan menambahkan `:800X` pada masing-masing server

### Script

**Arjuna sebagai Load Balancer**
```
upstream backend {
  server 192.185.; # IP PrabuKusuma
  server 192.185.; # IP Abimanyu
  server 192.185.; # IP Wisanggeni
}
```
-`roundrobin.sh`
```
echo "
# Default menggunakan Round Robin
upstream myweb {
server 192.185.3.2:8001; #IP Prabukusuma
server 192.185.3.3: 8002; #IP Abimanyu
server 192.185.3.4:8003; #IP Wisanggeni
}
server{
listen 80;
server_name arjuna.b14.com www.arjuna.b14.com;
location / {
proxy_pass http: //myweb;
}
}' > /etc/nginx/sites-available/lb-jarkom
ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled

service nginx restart
```

**PrabuKusuma, Abimanyu, Wisanggeni**
-`roundrobin.sh`
Membuat set sesuai `worker` ip masing-masing server
```
mkdir -p /var/www/jarkom
echo '<?php
echo "Halo kamu berada di Prabukusuma";
?>'> /var/www/jarkom/index.php

echo 'server {
        listen 800X;

        root /var/www/jarkom;
        index index.php index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }

        location ~ /\.ht {
                deny all;
        }
}' > /etc/nginx/sites-available/jarkom

ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled/jarkom

rm /etc/nginx/sites-enabled/default

service php7.0-fpm start
service php7.0-fpm restart
service nginx restart
```
**Nakula**
	-`Test_10.sh`
```
lynx http://192.185.3.2:8001
lynx http://192.185.3.3:8002
lynx http://192.185.3.4:8003
```
 
### Result

![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no10%20(1).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no10%20(2).png?raw=true)
**Load Balancing**


## Soal 11
> Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

### Script
**Abimanyu**
`apache.sh`
```
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/abimanyu.b14.com.conf

rm /etc/apache2/sites-available/000-default.conf

echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/abimanyu.b14

  ServerName abimanyu.b14.com
  ServerAlias www.abimanyu.b14.com

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.b14.com.conf

a2ensite abimanyu.b14.com.conf

service apache2 restart
```

**Client (Nakula/Sadewa)**
`Test11.sh`
```
lynx abimanyu.b14.com
lynx abimanyu.b14.com/home.html
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no11.png?raw=true)


## Soal 12
> Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

Disini kita menggunakana bantuan  `Directory` yang melakukan rewrite Indexes. Agar dapat melakukan `Alias`
```
<Directory /var/www/abimanyu.b14/index.php/home>
  Options +Indexes
</Directory>

Alias "/home" "/var/www/abimanyu.b14/index.php/home"
```

### Script
**Abimanyu**
`apache.sh`
```
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/abimanyu.b14
  ServerName abimanyu.b14.com
  ServerAlias www.abimanyu.b14.com

  <Directory /var/www/abimanyu.b14/index.php/home>
          Options +Indexes
  </Directory>

  Alias "/home" "/var/www/abimanyu.b14/index.php/home"

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/abimanyu.b14.com.conf

service apache2 restart
```

**Client (Nakula/Sadewa)**
`Test_12.sh`
```
lynx abimanyu.b14.com/home
curl abimanyu.b14.com/home
```

### Result

![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no12.png?raw=true)

## Soal 13
> Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy


### Script
**Abimanyu**
`savedocroot.sh`
```
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.b14
  ServerName parikesit.abimanyu.b14.com
  ServerAlias www.parikesit.abimanyu.b14.com

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.b14.com.conf

a2ensite parikesit.abimanyu.b14.com.conf

service apache2 restart
```

**Client (Nakula/Sadewa)**
`Test_13.sh`
```
lynx parikesit.abimanyu.b14.com
curl parikesit.abimanyu.b14.com
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no13.png?raw=true)


## Soal 14
> Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden)

Kita mau mengizinkan `public` agar dapat melakukan directory `listing` kita menggunakan `Options +Indexes`. Sedangkan agar suatu folder tidak dapat di akses, kita dapat menggunakan `Option -Indexes`.

### Script
**Abimanyu**
`publicsecret.sh`
```
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.b14
  ServerName parikesit.abimanyu.b14.com
  ServerAlias www.parikesit.abimanyu.b14.com

  <Directory /var/www/parikesit.abimanyu.b14/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.b14/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.b14/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.b14/secret"

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.b14.com.conf

service apache2 restart
```

**Client (Nakula/Sadewa)**
`Test_14.sh`
```
lynx parikesit.abimanyu.b14.com/public
lynx parikesit.abimanyu.b14.com/secret
```

### Result

![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no14%20(1).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no14%20(2).png?raw=true)

## Soal 15
> Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

### Script
**Abimanyu**
`error.sh`
```
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.b14
  ServerName parikesit.abimanyu.b14.com
  ServerAlias www.parikesit.abimanyu.b14.com

  <Directory /var/www/parikesit.abimanyu.b14/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.b14/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.b14/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.b14/secret"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.b14.com.conf

service apache2 restart
```

**Client (Nakula/Sadewa)**
Test_15.sh
```
lynx parikesit.abimanyu.b14.com/testerror
lynx parikesit.abimanyu.b14.com/secret
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no15.png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no15%20(1).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no15%20(2).png?raw=true)


## Soal 16
> Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi www.parikesit.abimanyu.yyy.com/js 

Disini kita hanya perlu menggunakan ``Alias "/js" "/var/www/parikesit.abimanyu.b14/public/js"`` untuk mengubah  virtual host agar file tersebut menjadi lebih singkat. Disini kami juga menggunakan `ServerName` dan `ServerAlias` agar virtual host dapat berjalan.

### Script
**Abimanyu**
`js.sh`
```
echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.b14
  ServerName parikesit.abimanyu.b14.com
  ServerAlias www.parikesit.abimanyu.b14.com

  <Directory /var/www/parikesit.abimanyu.b14/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.b14/secret>
          Options -Indexes
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.b14/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.b14/secret"
  Alias "/js" "/var/www/parikesit.abimanyu.b14/public/js"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.b14.com.conf
```

**Client (NakulaSadewa)**
`Test_16.sh`
```
lynx parikesit.abimanyu.b14.com/js
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no16.png?raw=true)


## Soal 17
> Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

Untuk  melakukan kustomisasi pada port tertentu. Kita hanya perlu mengubah file `ports.conf` dengan menambahkan `Listen 14000` dan `Listen 14400`. Kita juga perlu mengubah `<VirtualHost *:14000 *:14400>`

### Script
**Abimanyu**
`port.sh`
```
echo -e '<VirtualHost *:14000 *:14400>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.b14
  ServerName rjp.baratayuda.abimanyu.b14.com
  ServerAlias www.rjp.baratayuda.abimanyu.b14.com

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.b14.com.conf

echo -e '# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 14000
Listen 14400

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet' > /etc/apache2/ports.conf

a2ensite rjp.baratayuda.abimanyu.b14.com.conf

service apache2 restart
```

**Client (Nakula/Sadewa)**
`Test_17.sh`
```
lynx rjp.baratayuda.abimanyu.b14.com:14000
lynx rjp.baratayuda.abimanyu.b14.com:14400
```

### Result
**Port 14000 atau 14400**
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no17%20(1).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no17%20(2).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no17,%20(1).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no17,%20(2).png?raw=true)

## Soal 18
> Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

Untuk melakukan autentikasi pada sebuah server, diperlukan `AuthType` dan `Require Valid-User`. Lalu untuk AuthUserFile sendiri adalah tempat yang ingin kita gunakan untuk melakukan `write`. Sedangkan untuk `AuthName` adalah content-type Autentikasi pada apache2

### Script
**Abimanyu**
`autenticate.sh`
```
echo -e '<VirtualHost *:14000 *:14400>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/rjp.baratayuda.abimanyu.b14
  ServerName rjp.baratayuda.abimanyu.b14.com
  ServerAlias www.rjp.baratayuda.abimanyu.b14.com

  <Directory /var/www/rjp.baratayuda.abimanyu.b14>
          AuthType Basic
          AuthName "Restricted Content"
          AuthUserFile /etc/apache2/.htpasswd
          Require valid-user
  </Directory>

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.b14.com.conf

a2ensite rjp.baratayuda.abimanyu.b14.com.conf

service apache2 restart

//Tambahkan autentikasi dengan menggunakan command `htpasswd`. Lalu untuk `-c` itu adalah `created` dan `-b` yang merupakan `bcrypt` agar password yang kita isi akan dilakukan hash terlebih dahulu sebelum disimpan.

htpasswd -c -b /etc/apache2/.htpasswd Wayang baratayudab14
```

**Client (Nakula/Sadewa)**
`Test_18.sh`
```
lynx rjp.baratayuda.abimanyu.b14.com:14000
lynx rjp.baratayuda.abimanyu.b14.com:14400
```

### Result

![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no18.png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no18(1).png?raw=true)


## Soal 19
> Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

Kita perlu menggunakan file `Redirect` yang akan mengarahkan kepada file yang kita inginkan. Disini saya memasukkan ke dalam file konfigurasi `000-default.conf` karena merupakan default dari suatu service apache.

### Script
**Abimanyu**
`redirect.sh`
```
echo -e '<VirtualHost *:80>
    ServerAdmin webmaster@abimanyu.b14.com
    DocumentRoot /var/www/html

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    Redirect / http://www.abimanyu.b14.com/
</VirtualHost>' > /etc/apache2/sites-available/000-default.conf

apache2ctl configtest
service apache2 restart
```

**Client (Nakula/Sadewa)**
`Test_19.sh`
```
lynx 192.185.3.3
```

### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no19%20(1).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no19(2).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no19(3).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no19%20(4).png?raw=true)


## Soal 20
> Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.


### Script
**Abimanyu**
`last.sh`
Jangan lupa untuk menjalankan perintah berikut agar dapat melakukan `rewrite modul`
```
a2enmod rewrite

//Lalu jalankan perintah tersebut untuk melakukan `rewrite` terhdap directory parikesit.abimanyu.b14

echo 'RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/(.*)(abimanyu)(.*\.(png|jpg))
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule abimanyu http://parikesit.abimanyu.b14.com/public/images/abimanyu.png$1 [L,R=301]' > /var/www/parikesit.abimanyu.b14/.htaccess

//Ubah konfigurasi dan gunakan `AllowOverride All` untuk mengkonfigurasi nya dengan `.htaccess` sebelumnya.

echo -e '<VirtualHost *:80>
  ServerAdmin webmaster@localhost
  DocumentRoot /var/www/parikesit.abimanyu.b14

  ServerName parikesit.abimanyu.b14.com
  ServerAlias www.parikesit.abimanyu.b14.com

  <Directory /var/www/parikesit.abimanyu.b14/public>
          Options +Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.b14/secret>
          Options -Indexes
  </Directory>

  <Directory /var/www/parikesit.abimanyu.b14>
          Options +FollowSymLinks -Multiviews
          AllowOverride All
  </Directory>

  Alias "/public" "/var/www/parikesit.abimanyu.b14/public"
  Alias "/secret" "/var/www/parikesit.abimanyu.b14/secret"
  Alias "/js" "/var/www/parikesit.abimanyu.b14/public/js"

  ErrorDocument 404 /error/404.html
  ErrorDocument 403 /error/403.html

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>' > /etc/apache2/sites-available/parikesit.abimanyu.b14.com.conf

service apache2 restart
```

**Client (Nakula/Sadewa)**
`Test_20.sh`
```
lynx parikesit.abimanyu.b14.com/public/images/not-abimanyu.png
lynx parikesit.abimanyu.b14.com/public/images/abimanyu-student.jpg
lynx parikesit.abimanyu.b14.com/public/images/abimanyu.png
lynx parikesit.abimanyu.b14.com/public/images/notabimanyujustmuseum.177013
```
### Result
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no20%20(1).png?raw=true)
![alt text](https://github.com/Arkandrvesh/Jarkom-Modul-2-B14-2023/blob/main/img/no20%20(2).png?raw=true)
