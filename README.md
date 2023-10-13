# Jarkom-Modul-2-B14-2023

## Kontributor

| Nama Kontributor | NRP |
|------------------|-----|
| Gabrielle Immanuel Osvaldo Kurniawan | 5025211135 |
| Muhammad Arkan Karindra Darvesh | 5025211236 |



## Soal 1 
> Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut


### Script
**server selain DNS Master**
```
ping google.com -c 5
```

### Result



## Soal 2 
> Buatlah website utama dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.
Config


### Script

Pada node DNS Master, kita perlu melakukan setup terlebih dahulu sebagai berikut 

**Yudhistira**
```

```

**Arjuna**

Jangan lupa untuk setup nameserver terlebih dahulu yang diarahkan ke `IP Node yudhistira`
```
ping arjuna.b14.com -c 5
ping www.arjuna.b14.com -c 5
```

### Result


## Soal 3
> Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.


### Script

Pada node DNS Master, kita perlu melakukan setup terlebih dahulu sebagai berikut 

**Yudhistira**
```

```

**Abimanyu**

Jangan lupa untuk setup nameserver terlebih dahulu yang diarahkan ke `IP Node yudhistira`
```
ping abimanyu.b14.com -c 5
ping www.abimanyu.b14.com -c 5
```

### Result


## Soal 4
> Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.


### Script

**Yudhistira**

```

```

**Abimanyu**
```
ping parikesit.abimanyu.b14.com -c 5
```

### Result



## Soal 5
> Buat juga reverse domain untuk domain utama.


### Script
Untuk melakukan reverse domain. Kita perlu untuk mengetahui `IP` dari `Abimanyu`. Karena `IP Abimanyu` 

**Yudhistira**
```

```

**Abimanyu / Client yang lain**

Sebelum mengakses, jangan lupa untuk mengembalikan `nameserver` ke DNS Master
```
host -t PTR 
```

### Result



## Soal 6
> Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.


### Script
**Yudhistira**

Pada `DNS Master` diperlukan setup `also-notify` dan `allow-transfer` agar memberikan izin kepada `IP` yang dituju. 
```

```

**Werkudara (DNS Slave)**
```

```

**Abimanyu**
```
ping abimanyu.b14.com -c 5
ping www.abimanyu.b14.com -c 5
```

### Result



## Soal 7
> Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda


### Script
**Yudhistira**


```

```

**Werkudara**

Pada `DNS Slave` Kita perlu untuk mengarahkan `zone` ke `DNS Master` agar authoritative tadi dapat jalan. Kita juga perlu mengaktifkan ``allow-query { any; };`` pada `DNS Slave`

```

```

### Result


## Soal 8
> Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.



```
```

### Script
**Werkudara**
```

```
### Result

## Soal 9
> Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker



### Script
**Arjuna Load Balancer**

Disini kita perlu untuk membuat load balancer nya sebagai berikut 
```

```

jangan lupa untuk melakukan `symlink` dengan menjalankan perintah berikut

```

```

agar port tidak bertabrakan dengan `default` yang ada saat kita melakukan installasi pada `nginx`, maka kita harus menghapus file `default` tersebut

```
rm /etc/nginx/sites-enabled/default
```

lalu restart 
```
service nginx restart
```

**PrabuKusuma, Abimanyu,, Wisanggeni**

Jalankan perintah `shell` berikut pada masing-masing `worker`
```

```

**Client (Sadewa / Nakula)**
```

```

### Result



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

**PrabuKusuma, Abimanyu, Wisanggeni**

Membuat set sesuai `worker` ip masing-masing server
```

```
### Result



**Load Balancing**


## Soal 11
> Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy
