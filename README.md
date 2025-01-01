# SISTEM PERTAHANAN JARINGAN - HARDENING SERVER KELOMPOK 9
Disini, kami akan membuat Web Server lalu melakukan penyerangan Brute-Force menggunakan tools hydra serta melakukan Hardening. Anggota Kelompok kami terdiri dari: <br>
1.  Muhammad Hasbi Ramadhan - 23.83.1018. <br>
2. Reihan Chafaz Abimanyu - 23.83.1019. <br>
3. Roval Aprilian Sandi - 23.83.1051. <br>
4. Muhammad Zidan Maulana - 23.83.1022. <br>
<br>
Untuk spesifikasi server yang kami gunakkan, kaami memberi alokasi RAM sebesar 4GB, dengan Core processor sebanyak 4, dan storage yang digunakan sebesar 50GB. <br>

## Daftar Isi
<a href="#web">1. Membuat Web Server</a> <br>
<a href="#har">2.Hardening/Mitigasi Server dari serangan</a> <br>
<a href="#bas">3. Brute-force aftter Secure</a> <br>


<h2 id="web">1. Membuat WEB Server</h2>

1. Install SSH Terlebih dahulu <br> <br>
![image](https://github.com/user-attachments/assets/afa8bc76-ab40-49e5-9119-898e6e6c0207)
```
sudo apt install openssh-server
```
2. Akses Apache melalui browser, pastikan Apache telah berjalan.
![image](https://github.com/user-attachments/assets/ba18f0d0-dd7c-49a8-a1b1-e42621414e1d)

3. Konfigurasi Web Server Apache dengan masuk ke
```
sudo nano /var/www/html/index.html
```
Untuk konfigurasinya sebagai berikut
```
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to My Web Server!</title>
</head>
<body>
    <h1>Hello, World! This is my first web server.</h1>
</body>
</html>
```
![image](https://github.com/user-attachments/assets/b89ddd12-183b-4172-9831-867250974392)

4. Restart Apache untuk melihat perubahan pada Web Server
```
sudo systemctl restart apache2
```
![image](https://github.com/user-attachments/assets/ce3f332a-4f47-490f-ad48-53c308a239cb)

5. Buat Direktori yang tidak aman untuk melakkukan simulasi serangan, lalu edit direktori tersebut
```
sudo mkdir /var/www/html/private
sudo nano /var/www/html/private/info.html
```
Lalu, kamu dapat menambahkan:
```
<h1>Confidential Data</h1>
<p>Do not share this information!</p>
<img src="https://i.pinimg.com/originals/ed/52/fa/ed52faa044f95e85bf946efa2d116b7f.png" alt="Maxwell Cat">
```
![image](https://github.com/user-attachments/assets/27bffa7d-6769-4aca-ae54-8a661b9b3578)

6. Akses file tersebut melalui browser, masukkan link ```<alamat ip kamu>/private/info.html```. <br>
![image](https://github.com/user-attachments/assets/c60413fc-d2f4-4045-9f2f-f08826155615)


<h2 id="har">2. Hardening</h2>

### 1. Proteksi Direktori /private 

1. Buat file Password. Untuk yang bagian admin, dapat kamu ubah sesuai dengan username kamu.
```
sudo htpasswd -c /etc/apache2/.htpasswd admin
```
![image](https://github.com/user-attachments/assets/3c2db5ba-badf-42df-8fc6-9f98c0875fab)

2. Verifikasi File Password
```
cat /etc/apache2/.htpasswd
```
![image](https://github.com/user-attachments/assets/e95d4777-be5b-410e-8ffb-284129e7dec8)

3. Edit File konfigurasi Apache
```
sudo nano /etc/apache2/apache2.conf
```
lalu tambahkan teks berikut:
```
<Directory /var/www/html/private>
    AuthType Basic
    AuthName "Restricted Content"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```
![image](https://github.com/user-attachments/assets/feedb86a-e571-4d8f-9168-ad3fe04adfc9)


4. Restart Apache
```
sudo systemctl restart apache2
```
![image](https://github.com/user-attachments/assets/6d9abaf0-8d44-42d4-b3c9-e15266bfde17)

5. Ketik ```<alamat ip kamu>/private``` pada browser kamu. <br>
![image](https://github.com/user-attachments/assets/5a8de559-4345-4140-889d-0fe9f6436fc5)
Jika Username dan Password yang dimasukkan sesuai, kamu dapat masuk ke direktori /private. <br>
![image](https://github.com/user-attachments/assets/fbe82690-1f0f-423f-b76b-c6326613417b) <br>
Jika gagal, maka page yang muncul adalah seperti ini. <br>
![image](https://github.com/user-attachments/assets/a48185e8-7a67-4e8e-9da7-87589a67df78)

6. Sembunyikan status port dan versi Apache2. Buka File konfigurasi Apache
```
sudo nano /etc/apache2/conf-available/security.conf
```
lalu tambahkan
```
ServerTokens Prod
ServerSignature Off
```
![image](https://github.com/user-attachments/assets/8d4d727a-5907-465d-9879-1a163f3d0e8a)


8. Restart Apache dan buka kembali direktori /private pada browser
```
sudo systemctl restart apache2
```
![image](https://github.com/user-attachments/assets/3734a955-3754-4f9d-aca7-ab4b9b8940ae)

### 2. Menyembunyikan Direktori
1. Masuk ke konfigurasi Apache2
```
sudo nano /etc/apache2/apache2.conf
```
lalu tambahkan
```
<Directory /var/www/html>
    Options -Indexes
</Directory>
```
![image](https://github.com/user-attachments/assets/d43f59c3-6c7a-4e98-9a46-44e7fecc9d17)

2. Restart Apache2
```
systemctl restart apache2
```

3. Cek direktori ```<ip kamu>/private``` pada browser. Semisal penyerang berhasil masuk, penyerang tidak akan bisa melihat isi dari direktori server tersebut. <br>
![image](https://github.com/user-attachments/assets/ebfcbe5b-ca30-48b4-9ee8-bdf850ed68e5)

### 3. Batasi ukuran Request HTTP

1. Edit File konfigurasi apache2
```
sudo nano /etc/apache2/apache2.conf
```
Tambahkan baris ini untuk me-limit body request menjadi 5 mb.
```
LimitRequestBody 5242880
```
![image](https://github.com/user-attachments/assets/465d15af-8716-4f7b-b1f5-aba163498fce)


### 4. Memblokir IP Address yang mencoba untuk login berulang

1. Install tools Fail2Ban
```
sudo apt install fail2ban -y
```
![image](https://github.com/user-attachments/assets/21537aaa-af76-41a3-8926-d6ded02cef3c)

2. Masuk ke file konfigurasi Fail2Ban
```
nano /etc/fail2ban/jail.local
```
lalu masukkan tetks berikut
```
[apache-auth]
enabled = true
port = 80,443
filter = apache-auth
logpath = /var/log/apache2/error.log
maxretry = 3
bantime = 600
```
![image](https://github.com/user-attachments/assets/7e405397-90b7-466d-8ecd-d87ef7e68a87)

3. Restart Fail2Ban
```
systemctl restart fail2ban
```

### 5. Rate Limiting Untuk Membatasi Request

1. Aktifkan module rate limit
```
sudo a2enmod ratelimit
```

2. Ubah konfigurasi pada Apache2
```
sudo nano /etc/apache2/apache2.conf
```
Lalu sesuaikan baris berikut:
```
<Directory /var/www/html>
    SetOutputFilter RATE_LIMIT
    SetEnv rate-limit 100
</Directory>

```
![image](https://github.com/user-attachments/assets/a869a11b-e541-4661-b613-80e7fe866c9e)

3. Restart Apache
```
sudo systemctl restart apache2
```

### 6. Mod Evasive

1. Install Mod Evasive
```
sudo apt install libapache2-mod-evasive -y
```
![image](https://github.com/user-attachments/assets/300b268b-35b5-4274-8afd-4565a30cb830)

2. Konfigurasi file mod evasive
```
sudo nano /etc/apache2/mods-available/evasive.conf
```
Sesuaikan konfigurasi menjadi seperti ini:
```
<IfModule mod_evasive20.c>
    DOSHashTableSize 3097
    DOSPageCount 5
    DOSSiteCount 50
    DOSPageInterval 1
    DOSSiteInterval 1
    DOSBlockingPeriod 300
    DOSEmailNotify admin@example.com
    DOSLogDir "/var/log/mod_evasive"
</IfModule>
```
![image](https://github.com/user-attachments/assets/d0ac14a9-a1ab-4154-8424-2535e2026ca7)


3. Buat direktori log
```
sudo mkdir /var/log/mod_evasive
sudo chmod 777 /var/log/mod_evasive
```
![image](https://github.com/user-attachments/assets/6dbd8776-9509-4530-9862-26079dca8aeb)

4. Aktifkan modul dan restart Apache2
```
sudo a2enmod evasive
sudo systemctl restart apache2
```
![image](https://github.com/user-attachments/assets/169127e1-320e-4ada-bf06-9828638f542f)



<h2 id="bas">3. Brute-force after Hardening</h2>

1. Lakukan serangan brute-force menggunakan hydra, jika server kamu berhasil untuk di Hardening, maka Hydra tidak akan menampilkan username dan password yang konkrit.
```
hydra -L /home/hasbi/wordlist/userlist.txt -P /home/hasbi/Desktop/passlist.txt 192.168.100.95 http-get /private
```
![image](https://github.com/user-attachments/assets/55967404-0304-4be3-bb16-d4a684a40e2c) <br>
![image](https://github.com/user-attachments/assets/bdc49258-25ec-4f0d-b8a0-c01dae333037)



