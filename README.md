# SISTEM PERTAHANAN JARINGAN - HARDENING SERVER KELOMPOK 9
Disini, kami akan membuat Web Server lalu melakukan penyerangan Brute-Force menggunakan tools hydra serta melakukan Hardening. Anggota Kelompok kami terdiri dari: <br>
1.  Muhammad Hasbi Ramadhan - 23.83.1018. <br>
2. Reihan Chafaz Abimanyu - 23.83.1019. <br>
3. Roval Aprilian Sandi - 23.83.1051. <br>
4. Muhammad Zidan Maulana - 23.83.1022. <br>

## Membuat Web Server
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

