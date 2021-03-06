## BIND9
**BIND9 atau Berkeley Internet Name Domain Versi 9 adalah salah satu software yang biasa digunakan untuk membuat, membangun dan mengatur sebuah DNS (Domain Name Server) pada sistem operasi Linux.** 

**1. Install BIND9**

Command : `apt-get install bind9`.

**2. Edit file pada named.conf**

Command : `nano /etc/bind/named.conf`.

![Gambar edit named conf](https://github.com/bhaktiarc/projek-webserver/blob/master/img/Edit%20file%20pada%20named%20conf.PNG)

**3. Konfigurasi pada file named.conf.local**

Command : `nano /etc/bind/named.conf.local`.
        
        zone "tiga.tujuhlangit.id" {
            type master;
            file "/etc/bind/db.tiga.tujuhlangit.id";
        };

        zone "137.199.128.in-addr.arpa" {
            type master;
            file "/etc/bind/db.137.199.128.in-addr.arpa";
        }; 
        
![Gambar edit named conf local](https://github.com/bhaktiarc/projek-webserver/blob/master/img/conf%20named%20conf%20local.PNG)

**4. Konfigurasi pada file name.conf.options**

Command : `nano /etc/bind/named.conf.options`.
  
      acl "trusted" {
            128.199.137.147; # ns1
      };
      options {
            directory "/var/cache/bind";
            recursion yes;                    # enables resursive queries
            allow-recursion { trusted; };     # allows recursive queries from "trus$
            listen-on { 128.199.137.147; };   # ns1 private IP address - listen on $
            allow-transfer { none; };         # disable zone transfers by default
            // If there is a firewall between you and nameservers you want
            // to talk to, you may need to fix the firewall to allow multiple
            // ports to talk.  See http://www.kb.cert.org/vuls/id/800113
            // If your ISP provided one or more IP addresses for stable
            // nameservers, you probably want to use them as forwarders.
            // Uncomment the following block, and insert the addresses replacing
            // the all-0's placeholder.
            forwarders {
               8.8.8.8;
               8.8.8.4;
            };
            //=====================================================================$
            // If BIND logs error messages about the root key being expired,

![Gambar edit named conf options](https://github.com/bhaktiarc/projek-webserver/blob/master/img/konf%20named%20conf%20options.PNG)

**5. Konfigurasi db**

Terdapat 2 db yaitu fordward zone dan reserve zone

**Fordward Zone**
   
Copy dan edit file db.local : `cp /etc/bind/db.local /etc/bind/db.tiga.tujuhlangit.id`

Kemudian edit isinya : `nano /etc/bind/db.tiga.tujuhlangit.id`
   
Edit seperti ini:

        ; BIND data file for local loopback interface
        ;
        $TTL    604800
        @       IN      SOA     tiga.tujuhlangit.id. root.tiga.tujuhlangit.id. (
                                    2         ; Serial
                                604800         ; Refresh
                                86400         ; Retry
                                2419200         ; Expire
                                604800 )       ; Negative Cache TTL
        ;
        ;name servers - NS records
                IN      NS      ns1.tiga.tujuhlangit.id.
        ;name servers - A record
        tiga.tujuhlangit.id.    IN      A       128.199.137.147
        ;other
        tiga.tujuhlangit.id.            IN      A       128.199.137.147
        ns1             IN      A       128.199.137.147
        webmail         IN      A       128.199.137.147
        @               IN      MX 10   tiga.tujuhlangit.id.

![Gambar fordward zone](https://github.com/bhaktiarc/projek-webserver/blob/master/img/fordward%20zone.PNG)

**Reserve Zone**

Command : `cp /etc/bind/db.127 /etc/bind/db.137.199.in-addr.arpa`

Kemudian edit isinya : `nano /etc/bind/db.137.199.128.in-addr.arpa`

Edit seperti ini :

        ; BIND reverse data file for local loopback interface
        ;
        $TTL    604800
        @       IN      SOA     tiga.tujuhlangit.id. root.tiga.tujuhlangit.id. (
                                    1         ; Serial
                                604800         ; Refresh
                                86400         ; Retry
                                2419200         ; Expire
                                604800 )       ; Negative Cache TTL
        ;name server - NS records
                IN      NS      ns1.tiga.tujuhlangit.id.
        ;PTR Records
        137.147 IN      PTR     ns1.tiga.tujuhlangit.id.
        137.147 IN      PTR     webmail.tiga.tujuhlangit.id.

![Gambar reserve zone](https://github.com/bhaktiarc/projek-webserver/blob/master/img/reserve%20zone.PNG)

**6. Konfigurasi file resolv.conf**

Command : `nano /etc/resolv.conf`.

Isinya :
    nameserver 128.199.137.147
    nameserver 67.207.67.2
    nameserver 67.207.67.3

![Gambar resolv conf](https://github.com/bhaktiarc/projek-webserver/blob/master/img/resolv%20conf.PNG)

**7. Restart BIND9**

Command : `service bind9 restart`.

**8. Cek dengan nslookup dan ping ke domain**

Command :
1. `nslookup tiga.tujuhlangit.id`.

![Gambar ns1](https://github.com/bhaktiarc/projek-webserver/blob/master/img/nslookup%20www.PNG)

2. `nslookup 128.199.137.147`.

![Gambar ns2](https://github.com/bhaktiarc/projek-webserver/blob/master/img/nslookup%20ip.PNG)

3. `ping tiga.tujuhlangit.id`.

![Gambar ping](https://github.com/bhaktiarc/projek-webserver/blob/master/img/ping%20www.PNG)
    


   
