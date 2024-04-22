# SETUP DNS SERVER

### Langkah 1: Instalasi BIND9

1. Buka terminal.
2. Update paket sistem:
   ```bash
   sudo apt update
   sudo apt upgrade
   ```
3. Install BIND9:
   ```bash
   sudo apt install bind9 bind9-dnsutils bind9-doc
   ```

### Langkah 2: Konfigurasi BIND9

1. Edit file `/etc/bind/named.conf` :

   ```bash
   sudo nano /etc/bind/named.conf
   ```

   ```bash
    acl internals { 127.0.0.0/8; 10.0.0.0/16; };

    include "/etc/bind/named.conf.options";
    controls {
            inet 127.0.0.1 port 953 allow { 127.0.0.1; };
    };

    include "/etc/bind/named.conf.local";
    include "/etc/bind/named.conf.default-zones";
   ```

2. Edit file `/etc/bind/named.conf.options` :

   ```bash
   sudo nano /etc/bind/named.conf.options
   ```

   ```bash
    forwarders {
            202.9.85.3;
            202.9.85.4;
    };

    auth-nxdomain no;
    dnssec-validation auto;

    listen-on-v6 { none; };

    listen-on { 127.0.0.1; 10.0.2.15; };

    allow-transfer { none; };

    allow-query { internals; };

    allow-recursion { internals; };

    version none;
   ```

3. Edit file `/etc/bind/named.conf.local` :

   ```bash
   sudo nano /etc/bind/named.conf.local
   ```

   ```bash
    zone "kelompok6.local" {
            type master;
            file "/var/lib/bind/db.kelompok6.local";
    };

    zone "2.0.10.in-addr.arpa" {
            type master;
            file "/var/lib/bind/db.kelompok6.local.inv";
    };
   ```

4. Edit file `/var/lib/bind/db.kelompok6.local` :

   ```bash
   sudo nano /var/lib/bind/db.kelompok6.local
   ```

   ```bash
   $TTL    3600
   @       IN      SOA     ns.kelompok6.local. root.kelompok6.local. (
                     2024031901            ; Serial
                           3600            ; Refresh [1h]
                            600            ; Retry   [10m]
                          86400            ; Expire  [1d]
                            600 )          ; Negative Cache TTL [1h]
   ;
   @       IN      NS      ns.kelompok6.local.
   @       IN      MX      10 ns.kelompok6.local.

   ns      IN      A       10.0.2.15

   www     IN      CNAME   ns
   mail    IN      CNAME   ns
   ```

5. Edit file `/var/lib/bind/db.kelompok6.local.inv` :

   ```bash
   sudo nano /var/lib/bind/db.kelompok6.local.inv
   ```

   ```bash
   $TTL    3600
   @       IN      SOA     ns.kelompok6.local. root.kelompok6.local. (
                     2024031902           ; Serial
                           3600           ; Refresh [1h]
                            600           ; Retry   [10m]
                          86400           ; Expire  [1d]
                            600 )         ; Negative Cache TTL [1h]
   ;
   @       IN      NS      ns.kelompok6.local.

   15      IN      PTR     ns.kelompok6.local.
   ```

6. Edit file `/etc/resolv.conf` :

   ```bash
   sudo nano /etc/resolv.conf
   ```

   ```bash
   search kelompok6.local
   nameserver 10.0.2.15
   ```

### Langkah 4: Periksa Konfigurasi dan Restart BIND9

1. Periksa kesalahan konfigurasi:

   ```bash
   sudo named-checkconf /etc/bind/named.conf
   sudo named-checkzone kelompok6.local /var/lib/bind/db.kelompok6.local
   sudo named-checkzone 0.0.10.inaddr-arpa /var/lib/bind/db.kelompok6.local.inv
   ```

   ![Check](assets/ss-check.png)

2. Restart BIND9:

   ```bash
   sudo systemctl restart bind9
   ```

3. Periksa status BIND9 untuk memastikan tidak ada kesalahan:

   ```bash
   sudo systemctl status bind9
   ```

   ![Status](assets/ss-status.png)

4. Uji setup DNS

   ```bash
   dig kelompok6.local
   dig
   ```

   ![Status](assets/ss-dig.png)
