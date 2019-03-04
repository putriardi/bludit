# Aplikasi Web "Bludit" test
Bludit merupakan sebuah CMS (Content Management System) yang digunakan untuk membuat blog pribadi dalam waktu singkat, bebas biaya dan open source. Bludit menggunakan flat-files untuk menyimpan kiriman dan halaman, tanpa perlu menginstall atau konfigurasi basisdata. Bludit mendukung kode Markdown dan HTML untuk konten kiriman dan halaman yang akan diposting. 
# Instalasi 
**Kebutuhan Sistem :**
-   Ubuntu Server 18.04
-   Web Server Apache version 2.4 atau lebih
-   PHP 7.2
-   php7.2-common, php7.2-mbstring, php7.2-xmlrpc, php7.2-soap, php7.2-gd, php7.2-xml, php7.2-cli, php7.2-curl
-   Virtual Box
-   Non-root user dengan pengaturan sudo privileges
-   IP address statik

**Proses Instalasi :**
1. Install Virtual Machine (Virtual Box) dan Ubuntu Server di laptop.
2. Buka Virtual Machine dan Klik New pada virtual machine lalu tambahkan ubuntu server.
   Klik Start dan jalankan Ubuntu server lalu add instalasi ubuntu server.
   Klik Setting > Network > Advanced > Port Forwarding lalu atur port yang digunakan seperti gambar dibawah ini :
   
3. Login kedalam server menggunakan SSH. Untuk pengguna windows 10 bisa menggunakan aplikasi bash on ubuntu 

  ```
  $ ssh putri@localhost -p 2222
  ```
 4. Install Apache2 HTTP pada Ubuntu server
  ```
  $ sudo apt update
  $ sudo apt install apache2
  ```
   Setelah install Apache2, perintah dibawah ini dapat digunakan untuk memberhentikan, memulai dan mengaktifkan Apache2 pada server
  ```
  $ sudo systemctl stop apache2.service
  $ sudo systemctl start apache2.service
  $ sudo systemctl enable apache2.service
  ```
   Untuk memastikan Apache2 telah berhasil di install, bukalah http://localhost:8888 . JIka berhasil akan menampilkan seperti gambar dibawah ini
   
5. Install PHP 7.2 dan modul lain yang diperlukan
  ```
  $ sudo apt-get install software-properties-common
  $ sudo add-apt-repository ppa:ondrej/php
  $ sudo apt update
  $ sudo apt install php7.2 libapache2-mod-php7.2 php7.2-common php7.2-mbstring php7.2-xmlrpc php7.2-soap php7.2-gd php7.2-xml php7.2-cli php7.2-curl php7.2-zip
  $ sudo apt-get install nano wget unzip git curl
  ```
   Setelah menginstall PHP 7.2, bukalah file konfigurasi php untuk Apache2.
  ```
  $ sudo nano /etc/php/7.2/apache2/php.ini
  ```
     Ubah pengaturan seperti dibawah ini dan simpan
  ```
  file_uploads = On
  allow_url_fopen = On
  memory_limit = 256M
  upload_max_filesize = 100M
  max_execution_time = 360
  date.timezone = America/Chicago
```
   Untuk memastikan PHP 7.2 telah terkoneksi dengan Apache2, buatlah phpinfo.php  
  ```
  $sudo nano /var/www/html/phpinfo.php
  ```
   Lalu tuliskan seperti dibawah ini, lalu simpan
  ```
  <?php phpinfo( ); ?>
  ```
6. Lakukan update dan restart Apache2
  ```
  $ sudo apt-get update -y
	$ sudo apt-get upgrade -y
  $ sudo systemctl restart apache2.service
  ```
   Kemudian bukalah http://localhost:8888/phpinfo.php maka akan muncul gambar seperti dibawah ini :
  
7. Download dan ekstrak instalasi Bludit dan pindahkan ke root directory
  ```
  $ cd /tmp && wget https://df6m0u2ovo2fu.cloudfront.net/builds/bludit-2-3-3.zip
  $ unzip bludit-2-3-3.zip
  $ sudo mv bludit-2-3-3 /var/www/html/bludit
  ```
8. Lalu ubah permission untuk Bludit agar dapat diakses
  ```
  $ sudo chown -R www-data:www-data /var/www/html/bludit/
  $ sudo chmod -R 755 /var/www/html/bludit/ 
  ```
9. Ubah konfigurasi Apache2 agar terkoneksi dengan Bludit dan simpan file
  ```
  $ sudo nano /etc/apache2/sites-available/bludit.conf
  ```
  ```
  <VirtualHost *:80>
     ServerAdmin admin@example.com
     DocumentRoot /var/www/html/bludit/
     ServerName example.com
     ServerAlias www.example.com

     <Directory /var/www/html/bludit/>
          Options FollowSymlinks
          AllowOverride All
          Require all granted
     </Directory>

     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log combined
    
     <Directory /var/www/html/bludit/>
            RewriteEngine on
            RewriteBase /
            RewriteCond %{REQUEST_FILENAME} !-f
            RewriteRule ^(.*) index.php [PT,L]
    </Directory>
  </VirtualHost>
  ```
10. Aktifkan Bludit Site dan Rewrite Module
  ```
  $ sudo a2ensite bludit.conf
  $ sudo a2enmod rewrite
  $ sudo a2enmod proxy proxy_fcgi rewrite
  $ sudo systemctl restart apache2.service
  ```
11. Buka http://localhost:8888 untuk menginstall bludit
