# SISTEM PERTAHANAN JARINGAN - HARDENING SERVER KELOMPOK 9
Disini, kami akan membuat Web Server lalu melakukan penyerangan Brute-Force menggunakan tools hydra serta melakukan Hardening. Anggota Kelompok kami terdiri dari: <br>
1.  Muhammad Hasbi Ramadhan - 23.83.1018. <br>
2. Reihan Chafaz Abimanyu - 23.83.1019. <br>
3. Roval Aprilian Sandi - 23.83.1051. <br>
4. Muhammad Zidan Maulana - 23.83.1022. <br>
<br>
Untuk spesifikasi server yang kami gunakkan, kaami memberi alokasi RAM sebesar 4GB, dengan Core processor sebanyak 5, dan storage yang digunakan sebesar 50GB. <br>

## Daftar Isi
<a href="#web">1. Membuat Web Server</a> <br>
<a href="#bfs">2. Brute-Force before secure </a> <br>
<a href="#har">3.Hardening/Mitigasi Server dari serangan</a> <br>
<a href="#bas">4. Brute-force aftter Secure</a> <br>


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

<h2 id="bfs">2. Menyerang Server Before Secure Menggunakan Hydra</h2>

1. Lakukan serangan menggunakan tools hydra, pastikan kamu sudah memiliki passlist.txt. Jika serangan berhasil, outpunya akan sesuai dengan gambar yang dibawah.
```
hydra -l <username server kamu> -P passlist.txt ssh://<alamat ip web server kamu>
```
![image](https://github.com/user-attachments/assets/9ba09a30-4823-4d7b-9364-13a4bb2137a8)

<h2 id="har">3. Hardening</h2>

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
    AllowOverride All
    AuthType Basic
    AuthName "Restricted Content"
    AuthUserFile /etc/apache2/.htpasswd
    Require valid-user
</Directory>
```
![image](https://github.com/user-attachments/assets/feedb86a-e571-4d8f-9168-ad3fe04adfc9)

4. Buat file ```.htaccess``` dalam direktori private
```
sudo nano /var/www/html/private/.htaccess
```
lalu tambahkan
```
AuthType Basic
AuthName "Restricted Area"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user

<Files "info.html">
    Require all granted
</Files>
```
![image](https://github.com/user-attachments/assets/d8af8662-8eda-4bb7-990c-ccb2ebcd18e3)

5. Restart Apache
```
sudo systemctl restart apache2
```
![image](https://github.com/user-attachments/assets/6d9abaf0-8d44-42d4-b3c9-e15266bfde17)

6. Ketik ```<alamat ip kamu>/private``` pada browser kamu. <br>
![image](https://github.com/user-attachments/assets/5a8de559-4345-4140-889d-0fe9f6436fc5)
Jika Username dan Password yang dimasukkan sesuai, kamu dapat masuk ke direktori /private. <br>
![image](https://github.com/user-attachments/assets/fbe82690-1f0f-423f-b76b-c6326613417b)
Jika gagal, maka page yang muncul adalah seperti ini. <br>
![image](https://github.com/user-attachments/assets/a48185e8-7a67-4e8e-9da7-87589a67df78)

### 2. Hide Status Apache dan Port

1. Buka File konfigurasi Apache
```
sudo nano /etc/apache2/conf-available/security.conf
```
lalu tambahkan
```
ServerTokens Prod
ServerSignature Off
```
![image](https://github.com/user-attachments/assets/8d4d727a-5907-465d-9879-1a163f3d0e8a)


2. Restart Apache dan buka kembali direktori /private pada browser
```
sudo systemctl restart apache2
```
![image](https://github.com/user-attachments/assets/3734a955-3754-4f9d-aca7-ab4b9b8940ae)

### 3. Nonaktifkan Login Root

1. Masuk ke konfigurasi SSH
```
sudo nano /etc/ssh/sshd_config
```
Lalu ubah bagian PermitRootLogin menjadi ```PermitRootLogin no```, Lalu restart SSH. <br>
![image](https://github.com/user-attachments/assets/7aea9b09-e413-4215-8c86-1db4486ce478)

### 4. Rate-Limiting

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
[sshd]
enabled = true
port = 22
maxretry = 3
bantime = 600
findtime = 600
```
![image](https://github.com/user-attachments/assets/7eb8e069-77e0-4edb-b201-486607e7a7c9)

<h2 id="bas">4. Brute-force after Hardening</h2>

1. Lakukan serangan brute-force menggunakan hydra, jika server kamu berhasil untuk di Hardening, maka Hydra tidak akan bisa menyerang server kamu.
```
hydra -l <username kamu> -P passlist.txt ssh://192.168.100.72
```
![image](https://github.com/user-attachments/assets/688d013b-c45f-4d30-ae92-ddf05082198c)

