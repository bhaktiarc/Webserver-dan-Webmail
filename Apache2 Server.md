# Apache2
**Apache adalah server web yang dapat dijalankan di banyak sistem operasi (Unix, BSD, Linux, Microsoft Windows dan Novell Netware serta platform lainnya) yang berguna untuk melayani dan memfungsikan situs web. Protokol yang digunakan untuk melayani fasilitas web/www ini menggunakan HTTP**


**1. Intalasi Apache2**

Command : `apt-get install apache2`
    
**2. Buat VirtualHost File**

Untuk Webserver :
Kita copy konfigurasi default dan kita edit :

Command : `cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/wordpress.conf`

Kemudian buka file dan edit seperti dibawah ini :
  
Command : `nano /etc/apache2/sites-available/wordpress.conf` 
  
Isinya seperti ini :
  
      <VirtualHost tiga.tujuhlangit.id:80>
            ServerName tiga.tujuhlangit.id
            ServerAdmin kelompok3@tiga.tujuhlangit.id
            DocumentRoot /var/www/html
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>

![Gambar webserver](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/apache%20wordpress.PNG)

Untuk Webmail :
Kita copy konfigurasi default dan kita edit :

Command : `cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/webmail.tiga.tujuhlangit.id.conf`
  
Kemudian buka file dan edit seperti dibawah ini :

Command : `nano /etc/apache2/sites-available/webmail.tiga.tujuhlangit.id.conf`
  
      <VirtualHost webmail.tiga.tujuhlangit.id:80>
            ServerName webmail.tiga.tujuhlangit.id
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www
            ErrorLog ${APACHE_LOG_DIR}/error.log
            CustomLog ${APACHE_LOG_DIR}/access.log combined
      </VirtualHost>

![Gambar webmail](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/apache%20webmail.PNG)

**3. Enable Virtual dan Restart Apache2**
   
Enable :
   
Command : `a2ensite wordpress.conf`

Command : `a2ensite webmail.tiga.tujuhlangit.id`

![Gambar enable](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/enable%20virtual.PNG)
   
Restart :

Command : `service apache2 restart` 
    
**4. Konfigurasi Local Host File**
  
Command : `nano /etc/hosts`
  
Isinya :

      128.199.137.147 tiga.tujuhlangit.id
      128.199.137.147 webmail.tiga.tujuhlangit.id
      127.0.1.1 tiga.tujuhlangit.id

      The following lines are desirable for IPv6 capable hosts
      ::1 ip6-localhost ip6-loopback
      fe00::0 ip6-localnet
      ff00::0 ip6-mcastprefix
      ff02::1 ip6-allnodes
      ff02::2 ip6-allrouters
      ff02::3 ip6-allhosts

![Gambar local host file](https://github.com/bhaktiarc/projek-adm-sistem-server/blob/master/img/Konf%20Local%20Hosts.PNG)





