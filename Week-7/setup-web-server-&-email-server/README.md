# SETUP WEB SERVER DAN EMAIL SERVER

## NTP Client

1. Lakukan instalasi paket layanan sinkronisasi waktu
   ```bash
   apt install systemd-timesyncd
   ```
2. Pastikan konﬁgurasi timezone ke Asia/Jakarta
   ```bash
   sudo timedatectl set-timezone Asia/Jakarta
   ```
3. Melakukan konﬁgurasi Real Time Clock (RTC) untuk merefer ke UTC (Coordinated Universal Time)
   ```bash
   sudo timedatectl set-local-rtc false
   ```
4. Mengaktifkan NTP Client untuk sinkronisasi waktu
   ```bash
   sudo timedatectl set-ntp true
   ```
5. Menyunting ﬁle timesyncd.conf untuk mengarah ke NTP server terdekat untuk mendapatkan waktu
   delay terpendek. Biasanya setiap organisasi atau negara mempunyai NTP Server sendiri
   ```bash
   sudo nano /etc/systemd/timesyncd.conf
   ```
   ```bash
   #Ubahlah line 16
   [Time]
   NTP=0.id.pool.ntp.org
   ```
6. Restart layanan sinkronisasi waktu dan pastikan layanan berjalan dengan benar
   ```bash
   sudo systemctl restart systemd-timesyncd
   sudo systemctl status systemd-timesyncd
   ```
7. Lakukan pengecekan kesesuaian tanggal system dengan perintah
   ```bash
   timedatectl
   ```
   ![Timedatectl](assets/timedatectl.png)

## Apache 2 + PHP-FM

1. Install Apache2
   ```bash
   sudo apt -y install apache2
   ```
2. Mengkonﬁgurasi Apache2

   ```bash
   vi /etc/apache2/conf-enabled/security.conf
    # line 12 : change
    ServerTokens Prod
   ```

   ```bash
   vi /etc/apache2/mods-enabled/dir.conf
    # add ﬁle name that it can access only with directory's name
    DirectoryIndex index.html index.htm
   ```

   ```bash
   vi /etc/apache2/apache2.conf
    # line 70 : add to specify server name
    ServerName www. kelompok11.com
   ```

   ```bash
   vi /etc/apache2/sites-enabled/000-default.conf
    # line 11 : change to webmaster's email
    ServerAdmin webmaster@kelompok11.com
    systemctl reload apache2
   ```

3. Melakukan test ke web browser

   ![Test apache](assets/tes-apache.jpeg)

4. Install PHP 8.2

   ```bash
   apt -y install php8.2 php8.2-mbstring php-pear
   ```

   ```bash
   php -v
    PHP 8.2.7 (cli) (built: Jun 9 2023 19:37:27) (NTS)
    Copyright (c) The PHP Group
    Zend Engine v4.2.7, Copyright (c) Zend Technologies
    with Zend OPcache v8.2.7, Copyright (c), by Zend Technologies
   ```

   ```bash
   # verify installation to create a test script
   echo '<?php echo `php -i`."\n"; ?>' > php_test.php
   ```

   ```bash
   php php_test.php | head
    phpinfo()
    PHP Version => 8.2.7

    System => Linux dlp.srv.world 6.1.0-9-amd64 #1 SMP PREEMPT_DYNAMIC Debian 6.1.27-1
    (2023-05-08) x86_64
    Build Date => Jun 9 2023 19:37:27
    Build System => Linux
    Server API => Command Line Interface
    Virtual Directory Support => disabled
    Conﬁguration File (php.ini) Path => /etc/php/8.2/cli
    Loaded Conﬁguration File => /etc/php/8.2/cli/php.ini
   ```

5. Install PHP-FM

   ```bash
   sudo apt -y install php-fpm
   ```

6. Mengkonﬁgurasi PHP-FM pada ﬁle konﬁgurasi Apache

   ```bash
   vi /etc/apache2/sites-available/default-ssl.conf
    # add into <VirtualHost> - </VirtualHost>
        <FilesMatch \.php$>
        SetHandler "proxy:unix:/var/run/php/php8.2-fpm.sock|fcgi://localhost/"
        </FilesMatch>
    </VirtualHost>
   ```

   ```bash
   a2enmod proxy_fcgi setenvif
    Considering dependency proxy for proxy_fcgi:
    Enabling module proxy.
    Enabling module proxy_fcgi.
    Module setenvif already enabled
    To activate the new conﬁguration, you need to run:
    systemctl restart apache2
   ```

   ```bash
   a2enconf php8.2-fpm
    Enabling conf php8.1-fpm.
    To activate the new conﬁguration, you need to run:
    systemctl reload apache2
   ```

   ```bash
   systemctl restart php8.2-fpm apache2
   ```

7. Melakukan test validasi terhadap PHP-FM dengan membuat ﬁle info.php di root document
   ```bash
   echo '<?php phpinfo(); ?>' > /var/www/html/info.php
   ```
8. Lalu lakukan test di browser

   ![Test php](assets/tes-php.jpeg)

## Database System : MariaDB

1. Lakukan nstalasi Maria DB 10.11

   ```bash
   apt -y install mariadb-server
   ```

   ```bash
   vi /etc/mysql/mariadb.conf.d/50-server.cnf
    # line 95 : conﬁrm default charset
    # if use 4 bytes UTF-8, specify [utf8mb4]
    character-set-server = utf8mb4
    collation-server = utf8mb4_general_ci
   ```

   ```bash
   systemctl restart mariadb
   ```

2. Inisial Konﬁgurasi dan testing database MariaDB Server
   ```bash
   mysql_secure_installation
   ```

## Email System

### POSTFIX : SMTP Server (TCP 25)

1. install postﬁx

   ```bash
   apt -y install postﬁx sasl2-bin
   # on this example, proceed to select [No Conﬁguration]
   # because conﬁgure all manually
   +--------+ Postﬁx Conﬁguration +--------+
   | General type of mail conﬁguration:    |
   |                                       |
   | No conﬁguration                       |
   | Internet Site                         |
   | Internet with smarthost               |
   | Satellite system                      |
   | Local only                            |
   |                                       |
   |                                       |
   |        <Ok>        <Cancel>           |
   |                                       |
   +---------------------------------------+
   ```

   ```bash
   cp /usr/share/postﬁx/main.cf.dist /etc/postﬁx/main.cf
   ```

   ```bash
   vi /etc/postﬁx/main.cf
    # line 82 : uncomment
    mail_owner = postﬁx
    # line 98 : uncomment and specify hostname
    myhostname = mail.kelompok11.local
    # line 106 : uncomment and specify domainname
    mydomain = kelompok11.local
    # line 127 : uncomment
    myorigin = $mydomain
    # line 141 : uncomment
    inet_interfaces = all
    # line 189 : uncomment
    mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
    # line 232 : uncomment
    local_recipient_maps = unix:passwd.byname $alias_maps
    # line 277 : uncomment
    mynetworks_style = subnet
    # line 294 : add your local network
    mynetworks = 127.0.0.0/8, 10.0.0.0/24, 192.168.0.0/16
    # line 416 : uncomment
    alias_maps = hash:/etc/aliases
    # line 427 : uncomment
    alias_database = hash:/etc/aliases
    # line 449 : uncomment
    home_mailbox = Maildir/
    # line 585: comment out and add
    #smtpd_banner = $myhostname ESMTP $mail_name (Debian/GNU)
    smtpd_banner = $myhostname ESMTP
    # line 659 : add
    sendmail_path = /usr/sbin/postﬁx
    # line 664 : add
    newaliases_path = /usr/bin/newaliases
    # line 669 : add
    mailq_path = /usr/bin/mailq
    # line 675 : add
    setgid_group = postdrop
    # line 679 : comment out
    #html_directory =
    # line 683 : comment out
    #manpage_directory =
    # line 688 : comment out
    #sample_directory =
    # line 692 : comment out
    #readme_directory =
    # line 692 : if also listen IPv6, change to [all]
    inet_protocols = ipv4
    # add follows to the end
    # disable SMTP VRFY command
    disable_vrfy_command = yes
    # require HELO command to sender hosts
    smtpd_helo_required = yes
    # limit an email size
    # example below means 10M bytes limit
    message_size_limit = 10240000
    # SMTP-Auth settings
    smtpd_sasl_type = dovecot
    smtpd_sasl_path = private/auth
    smtpd_sasl_auth_enable = yes
    smtpd_sasl_security_options = noanonymous
    smtpd_sasl_local_domain = $myhostname
    smtpd_recipient_restrictions = permit_mynetworks, permit_auth_destination,
    permit_sasl_authenticated, reject
   ```

   ```bash
   newaliases
   ```

   ```bash
   systemctl restart postﬁx
   ```

2. Menambahkan konﬁgurasi anti spam

   ```bash
   vi /etc/postﬁx/main.cf
    # add to the end
    # reject unknown clients that forward lookup and reverse lookup of their hostnames on DNS do
    not match
    smtpd_client_restrictions = permit_mynetworks, reject_unknown_client_hostname, permit
    # rejects senders that domain name set in FROM are not registered in DNS or
    # not registered with FQDN
    smtpd_sender_restrictions = permit_mynetworks, reject_unknown_sender_domain,
    reject_non_fqdn_sender
    # reject hosts that domain name set in FROM are not registered in DNS or
    # not registered with FQDN when your SMTP server receives HELO command
    smtpd_helo_restrictions = permit_mynetworks, reject_unknown_hostname,
    reject_non_fqdn_hostname, reject_invalid_hostname, permit
   ```

   ```bash
    systemctl restart postﬁx
   ```

### DOVECOT : IMAP4 (TCP 143) and POP3 (TCP110) Server

1. Lakukan instalasi Dovecot Server

   ```bash
   apt -y install dovecot-core dovecot-pop3d dovecot-imapd
   ```

   ```bash
   vi /etc/dovecot/dovecot.conf
    # line 30 : uncomment
    listen = *, ::
   ```

   ```bash
   vi /etc/dovecot/conf.d/10-auth.conf
    # line 10 : uncomment and change (allow plain text auth)
    disable_plaintext_auth = no
    # line 100 : add
    auth_mechanisms = plain login
   ```

   ```bash
   vi /etc/dovecot/conf.d/10-mail.conf
    # line 30 : change to Maildir
    mail_location = maildir:~/Maildir
   ```

   ```bash
   vi /etc/dovecot/conf.d/10-master.conf
    # line 107-109 : uncomment and add
    # Postﬁx smtp-auth
    unix_listener /var/spool/postﬁx/private/auth {
     mode = 0666
     user = postﬁx
     group = postﬁx
    }
   ```

   ```bash
   systemctl restart dovecot
   ```

### FINAL CHECK untuk semua SERVICES :

Akan terlihat hasilnyaseperti dibawah, dengan status Server (LISTEN) : MariaDB(MySQL), IMAP, POP3, DNS(domain), IMAPS, POP3S, SSH, Postﬁx (SMTP)

![final cek](assets/final-cek.png)

Melakukan Cek terhadap Layanan Posﬁx

```bash
telnet mail.kelompok11.local 25
```

![mail cek](assets/telnet.png)
