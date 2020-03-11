# Instalasi dan Konfirgurasi Mailserver dan Webmail

### A. Instalasi Paket

-	mail-stack-delivery 
-	mailutils
-	postfix-mysql 
-	dovecot-mysql 
-	dovecot-lmtpd 
-	mysql-server
-	roundcube 
-	roundcube-mysql 
-	roundcube-plugins 
-	roundcube-plugins-extra
-	roundcube 
-	roundcube-mysql 
-	roundcube-plugins 
-	roundcube-plugins-extra

### B. Konfigurasi Hostname

Konfigurasi hostname adalah menambahkan fully qualified domain name (FQDN) agar dapat diakses, untuk melakukan konfigurasi dapat dilakukan pada file `/etc/host`

![Gambar]()

Kemudian lakukan reboot pada system dengan syntax `systemctl reboot` dan lakukan pengecekan terhadap hostname dengan syntax: `hostname --fqdn`

![Gambar]()

### C. Konfigurasi POSTFIX

Postfix adalah software Ubuntu yang bertugas sebagai pengirim email di mail server, dengan hanya melakukan instalasi kita sudah dapat melakukan pengiriman email tetapi hanya secara local dan sesame pengguna yang terdaftar di dalam os linux

Dalam proses instalasi kami memilih opsi sebagai berikut : 
-	General type of mail configuration: Internet Site
-	System mail name: tiga.tujuhlangit.id

Langkah selanjutnya kami melakukan konfigurasi lokasi email yang dimana yang akan menentukan lokasi penyimpanan email masuk dengan syntax :

Syntax: `postconf -e 'home_mailbox = Maildir/'&& export MAIL=~/Maildir `

### D. Konfigurasi MYSQL-SERVER dan POSTFIX Mapping

Penggunaan MYSQL-SERVER disini adalah menjadi acuan dari mail server untuk melakukan pengiriman dan penerimaan, datauser, dan direktori email melalui database dari pada hanya mengambil sumber dari sistem user linux itu sendiri yang tentu saja kurang efisien

**1. Membuat file script login otomatis**

File ini dibuat agar dalam melakukan konfigurasi lebih efisien, file tersebut akan berisi informasi-informasi login agar dapat masuk kedalam console mysql, file yang dibuat dengan nama `.my.cnf` dan isi file tersebut adalah sebagi berikut :

![Gambar]()

Sehingga ketika mengetikan syntax `mysql` akan langsung masuk ke dalam console mysql

![Gambar]()

**2. Membuat database**

Disini kami membuat 1 database yaitu postfix dan 3 tabel yaitu domain , alias, dan user 

![Gambar]()

Tahap selanjutnya kami lakukan pembuatan user dengan nama kelompok3 dan membuat user tersebut mendapatkan akses penuh terhadap database postfix, perintah dapat di lakukan dengan syntax :

Syntax: `GRANT SELECT ON postfix.* TO kelompok3@localhost IDENTIFIED BY 'Kelompok3';`

Syntax: `flush privileges;`

**3. Konfigurasi POSTFIX**

Dalam tahap ini terdapat di file yang dikonfigurasi terdapat pada direktori `/etc/postfix/` dengan nama file sebagai berikut: 
-	main.cf
-	master.cf.

**3.1 main.cf**

konfigurasi pada baris 20 – 25

![Gambar]()

Konfigurasi diatas adalah membuat postfix menggunakan tls beserta cache dari postfix

Konfigurasi pada baris 25 

![Gambar]()

Variable ini berfungsi untuk menambahkan domain dari tujuan email address
Konfigurasi pada bari 67

![Gambar]()

Konfigurasi ini adalah agar postfix menggunakan dovecot LMTP

Konfigurasi pada baris 68 – 73

![Gambar]()

Konfigurasi ini adalah konfigurasi protocol keamanan yang digunakan oleh postfix dimana mail server kita hanya akan menggunakan TLS dan tidak menggunakan SSLv2 dan SSLv3 yang keamananya sangat tidak kuat 

**3.2 master.cf**

pada file master.cf konfigurasi awal adalah menghilangkan tanda “#” pada baris 12, 17 – 21 ,25 – 27

![Gambar]()

**4. Menghubungkan postfix dan mysql-server dengan file map**

Terdapat 4 file yang akan di konfigurasi dan semuanya terdapat pada direktori `/etc/postfix` file-file berisi informasi-informasi alias,domain,dan user dari mail server dan melakukan query terhadap database, baik itu informasi login berupa username dan password, nama database, dan host databse , ketiga file adalah sebagai berikut:
-	mysql-virtual-mailbox-domains.cf
-	mysql-virtual-mailbox-maps.cf
-	mysql-virtual-alias-maps.cf
-	mysql-virtual-alias-maps-self.cf

kemudian file berikut di tambahkan ke dalam file konfigurasi postfix dengan syntax dibawah ini

jalankan satu-satu:

1.  `postconf -e virtual_alias_maps=mysql:/etc/postfix/mysql-virtual-alias-maps.cf`
2.	`postconf -e virtual_mailbox_maps=mysql:/etc/postfix/mysql-virtual-mailbox-maps.cf`
3.	`postconf -e virtual_mailbox_domains=mysql:/etc/postfix/mysql-virtual-mailbox-domains.cf`
4.	`postconf -e virtual_alias_maps=mysql:/etc/postfix/mysql-virtual-alias-maps.cf,mysql:/etc/postfix/mysql-virtual-alias-maps-self.cf`
5.	`chgrp postfix /etc/postfix/mysql-*.cf`
6.	`chmod 640 /etc/postfix/mysql-*.c`

**4.1 mysql-virtual-mailbox-domains.cf**

File ini berfungsi untuk mendapatkan domain email server yang ada di server, misalnya terdapat satu server tetapi akan menjalankan 2 domain email server dapat di lakukan pada table domain dan file ini yang akan menjadi sumber data dari postfix 

![Gambar]()

**4.2. mysql-virtual-mailbox-maps.cf**

file ini berfungsi untuk sumber data agar postfix dapat membuat direktori penyimpanan email terhadap user beserta domainya di dalam server, misalnya terdapat user dengan nama email address kelompok3@tiga.tujuhlangit.id maka akan direktori emailnya akan tersimpan pada direktori :

dir-nya: `/var/vmail/vhosts/tiga.tujuhlangit.id>/kelompok3`

![Gambar]()

**4.3. mysql-virtual-alias-maps.cf**

File ini berfungsi sebagai sumber data postfix untuk membuat sebuah postmaster dari mail server misalnya terdapat email info@tiga.tujuhlangit.id yang berfungsi sebagai email perusahaan kemudian email tersebut akan di forward ke email `kelompok3@tiga.tujuhlangit.id `, file ini juga dapat berfungsi sebagai anti spam yang dapat dilihat secara langsung atau dapat di filter secara manual tanpa menggunakan tambahan software, walaupun kurang efisien terhadap waktu tetapi akurat dalam melakukan filter terhadap email spam

![Gambar]()

**4.4. mysql-virtual-alias-maps-self.cf**

File ini menjadi sumber data dari postfix untuk melakukan mapping terhadap email client yang mengirimkan email ke emailnya sendiri, hal ini dilakukan akan client mail server dapat melihat lagi email yang dikirimnya pada folder sentmail atau lainya sebagainya yang berfungsi sebagai laporan email keluar 

![Gambar]()

**5. Konfigurasi Dovecot**

Dovecot adalah software yang berfungsi sebagai penyedia layanan imap dan pop3 sehingga email dapat diterima oleh mail server

**5.1. Setting permission terhadap direktori Vmail**

Syntax :
1.	`sudo groupadd -g 6004 vmail`
2.	`sudo useradd -g vmail -u 6004 vmail -d /var/vmail -m -s /sbin/nologin`
3.	`sudo mkdir -p /var/vmail/vhosts/dragon.lab`
4.	`sudo chown -R vmail:vmail /var/vmail`

**5.2.	Konfigurasi Dovecot**

Terdapat tiga file yang akan di konfigurasi terdapat pada file `/etc/dovecot/` yaitu dengan nama sebagai berikut :
-	10-auth.conf
-	dovecot-sql.conf.ext
-	99-mail-stack-delivery.conf

    **5.2.1. 10-auth.conf**

    ![Gambar]()

    **5.2.2. dovecot-sql.conf.ext**

    ![Gambar]()

    **5.2.3.  99-mail-stack-delivery.conf**

    File ini berisi bagaimana komunikasi keluar dan masuknya email yaitu port, protocol, keamanan, dan letak direktori dari email dari mail server itu sendiri, di bawah ini adalah konfigurasi protocol pop3 imap dan lokasi email 

    ![Gambar]()

    Konfigurasi di bawah ini adalah memberitahukan dovecot agar menerima dari service apa dan siapa user yang menjalankanya

    ![Gambar]()

    Konfigurasi dibawah ini adalah memberituhukan dovecot agar menjalankan imap dan pop3 pada port tertentu 

    ![Gambar]() 


**6. Uji konfigurasi mail server**

Kami melakukan uji coba mail server dengan kedua software yang telah di konfigurasikan  perintah tersebut dapat dilakukan dengan syntax :

1. `service postfix restart && service postfix status`
2. `service dovecot restart && service postfix status`

    **6.1.	POSTFIX**

    Apabila dilihat dari log file di bawah terterjadi error terhadap konfigurasi POSTFIX

    ![Gambar]()

    **6.2.	DOVECOT**

    Apabila dilihat dari log diibawah ini tidak terjadi permasalahan pada konfigurasi dovecot

    ![Gambar]()


### Instalasi Roundcube

Roundcube adalah interface terhadap pengguna ke mail server, sesuai dengan ketentuan maka halaman web dapat diakses melalui `webmail.tiga.tujuhlangit.id`

Pada tahap instalasi roundcube kami memilih beberapa opsi sebagai berikut :
-	Configure database for Roundcube with dbconfig-common? Yes
-	MySQL application password for roundcube: Kelompok3

Secara otomatis roundcube akan melakukan konfigurasi database di mysql server

![Gambar]()

![Gambar]()

![Gambar]()

**1. Konfigurasi Roundcube**

File konfigurasi Roundcube dapat diakses pada direktori  `/etc/roundcube/ config.inc.php`

File ini akan berisi informasi-informasi database dan smtp server

![Gambar]()

**2.	Konfigurasi VirtualHost**

File konfigurasi Roundcube dapat diakses pada direktori `/etc/apache2/conf-available/roundcube.conf`

File ini berfungsi sebagai konfigurasi web server agar mengakses roundcube dan konfigurasi SSL 

![Gambar]()

**3.	Menambahkan fitur pendaftaran pada roundcube**

Fitur ini merupakan penambahan didasari oleh inisiatif kami kelompok 3 agar pengguna dapat mendaftarkan email dengan domain `@tiga.tujuhlangit.id`

penambahan yang kami lakukan adalah sebagai berikut :

-	Penambahan tombol daftar pada file login.html
-	Pembuatan file `register.php` dan `reghandler.php` beserta dependencies-nya yang disimpan pada direktori `/var/lib/roundcube/register/`

### F. Memberikan SSL pada Webmail

Syntax: `sudo certbort --apache`

Kemudian ikuti apa yang diminta 

![Gambar]()

![Gambar]()

Menambahkan syntax pada file `roundcube.conf`

syntax: `nano /etc/apache2/conf-available/roundcube.conf`

Edit seperti dibawah ini:

            <VirtualHost webmail.tiga.tujuhlangit.id:80>

            ServerAdmin postmaster@tiga.tujuhlangit.id
            ServerName webmail.tiga.tujuhlangit.id
            ServerAlias webmail.tiga.tujuhlangit.id

            DocumentRoot /var/lib/roundcube

            # Force all http connections to be https
            RewriteEngine on
            RewriteCond %{HTTPS} !^on$ [NC]
            RewriteRule . https://%{HTTP_HOST}%{REQUEST_URI}

            </VirtualHost>

            <IfModule mod_ssl.c>
                    SSLStrictSNIVHostCheck off

                    <VirtualHost  webmail.tiga.tujuhlangit.id:443>
                        ServerAdmin postmaster@tiga.tujuhlangit.id
                        ServerName webmail.tiga.tujuhlangit.id
                        ServerAlias webmail.tiga.tujuhlangit.id

                        DocumentRoot /var/lib/roundcube

                        SSLEngine on
                        SSLCertificateFile /etc/letsencrypt/live/webmail.tiga.tujuhlangit.id/fullch$
                        SSLCertificateKeyFile /etc/letsencrypt/live/webmail.tiga.tujuhlangit.id/pri$

                        <FilesMatch "\.(cgi|shtml|phtml|php)$">
                                SSLOptions +StdEnvVars
                        </FilesMatch>
                    </VirtualHost>
            </IfModule>

            <Directory /var/lib/roundcube/>
            Options +FollowSymLinks
            # This is needed to parse /var/lib/roundcube/.htaccess. See its
            # content before setting AllowOverride to None.
            AllowOverride All
            <IfVersion >= 2.3>
                Require all granted
            </IfVersion>
            <IfVersion < 2.3>
                Order allow,deny
                Allow from all
            </IfVersion>
            </Directory>

            # Protecting basic directories:
            <Directory /var/lib/roundcube/config>
                    Options -FollowSymLinks
                    AllowOverride None
            </Directory>

            <Directory /var/lib/roundcube/temp>
                    Options -FollowSymLinks
                    AllowOverride None
                    <IfVersion >= 2.3>
                    Require all denied
                    </IfVersion>
                    <IfVersion < 2.3>
                    Order allow,deny
                    Deny from all
                    </IfVersion>
            </Directory>

            <Directory /var/lib/roundcube/logs>

![Gambar]()

## Hasil Akhir Webserver dan Webmail

**1.	Webserver	: www.tiga.tujuhlangit.id**

Kami melakukan beberapa modifikasi pada konfigurasi default wordpress dan php sehingga bisa menggunakan tema / upload tema selain yang disediakan oleh wordpress. Kemudian kami juga melakukan modofikasi pada tampilan sehingga menjadi sedemikian rupa.

![Gambar]()

**2.	Webmail 	: www.webmail.tiga.tujuhlangit.id**

Kami melakukan modifikasi pada interface webmail sehingga menjadi ada fitur daftar untuk membuat akun email. 

![Gambar]()

### Testing daftar webmail

![Gambar]()

Email address : bhaktiarc@tiga.tujuhlangit.id

Password	: Kelompok3

![Gambar]()

![Gambar]()

Login dengan akun yang telah dibuat

![Gambar]()

**Testing mengirim email dan menerima email**

![Gambar]()

![Gambar]()

Mengecek pada email tujuan

![Gambar]()






















