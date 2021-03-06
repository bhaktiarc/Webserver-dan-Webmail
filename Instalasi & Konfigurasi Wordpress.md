# Instalasi dan Konfigurasi Wordpress

**1. Konfigurasi VirtualHost**

Virtualhost untuk wordpress pada alamat tiga.tujuhlangit.id 

Menggunakan named-header dengan alamat tiga.tujuhlangit.id

Direktori yang digunakan `/var/www/html/`

Lalu jalankan command berikut :

1. `a2ensite wordpress.conf`
2. `service apache2 reload`

Buka wordpress.conf dengan command : `nano /etc/apache2/sites-available/wordpress.conf`

![Gambar konfig VH](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/konf%20virtualhost.PNG)

**2. Konfigurasi Database**

![Gambar show db](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/show%20database.PNG)

![Gambar grant db](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/grant%20db.PNG)

Aplikasi web wordpress yang akan di install akan menggunakan databases wordpress yang dimana user ‘kelompok3’ mendapatkan akses penuh terhadap database wordpress

**3. Pengunduhan Wordpress dan konfigurasi wp-config.php**

Langkah-langkah dan command yang digunakan :

1.	`wget -c http://wordpress.org/latest.tar.gz`

    gunakan perintah wget untuk mengunduh file-file yang dibutuhkan oleh wordpress

2.	`tar -xzvf latest.tar.gz`

    perintah diatas untuk melakukan proses ekstrasi file wordpress

3.	`rsync -av wordpress/* /var/www/html/`

    perintah diatas untuk menyalin seluruh file yang ada di direktori wordpress ke direktori document root virtualhost wordpress.conf 

4.	`chown -R www-data:www-data /var/www/html/`

5.	`chmod -R 755 /var/www/html/`

    perintah diatas untuk melakukan permission pada direktori /var/www/html/

6.	`mv wp-config-sample.php wp-config.php`

**4. Konfigurasi wp-config.php**

Konfigurasi pada file wp-config.php adalah mengisi informasi – informasi database agar aplikasi wordpress dapat terhubung ke dalam database , berikut ini adalah isi file wp-config.php 

![Gambar konfig wp-config](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/konfig%20wp-config.PNG)

**5. Instalasi Wordpress**

1. Mengakses domain melalui `tiga.tujuhlangit.id/wp-admin/install.php`

![Gambar install wordpress](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/install%20wordpress.PNG)

2. Masukan informasi login sebagai admin wordpress  

![Gambar install wordpress2](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/install%20wordpress2.PNG)

3. Login sebagai admin wordpress
   
![Gambar login wordpress](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/login%20wordpress.PNG)

**6. Instalasi SSL pada webserver**

SSL yang digunakan adalah letsencrypt sesuai dengan ketentuan pada soal 

1.	Instalasi paket 

- Certbort

2.	Instalasi SSL 

Command : `certbot --apache -d tiga.tujuhlangit.id`

![Gambar instalasi ssl](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/install%20ssl.PNG)


## Pengujian

![Gambar pengujian1](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/pengujian1.PNG)

![Gambar pengujian2](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/pengujian2.PNG)

![Gambar pengujian3](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/pengujian3.PNG)







