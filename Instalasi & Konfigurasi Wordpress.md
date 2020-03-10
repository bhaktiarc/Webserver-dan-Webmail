# Instalasi dan Konfigurasi Wordpress

**1. Konfigurasi VirtualHost**

Virtualhost untuk wordpress pada alamat tiga.tujuhlangit.id 

Menggunakan named-header dengan alamat tiga.tujuhlangit.id

Direktori yang digunakan `/var/www/html/`

Lalu jalankan command berikut :

1. `a2ensite wordpress.conf`
2. `Service apache2 reload`

Buka wordpress.conf dengan command : `nano /etc/apache2/sites-available/wordpress.conf`

![Gambar konfig VH]()

**2. Konfigurasi Database**

