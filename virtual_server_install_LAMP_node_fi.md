<!-- F**K GitHub style
  type="text/css">
ol ol { list-style-type: lower-alpha; }
ol ol ol { list-style-type: lower-roman; }
</style -->

# LAMPin ja Node asennus Metropolian Educloud-virtuaalikoneille (CentOS

## Virtuaalikoneella olevien web-sivujen katsominen selaimella koulun verkon ulkopuolelta

1. Käytä [Metropolian VPN-etäyhteyttä](https://wiki.metropolia.fi/pages/viewpage.action?pageId=149652071)
2. Other options (e.g. during zoom session to reduce vpn bandwidth)
   1.  to test your app/webpages, create a [ssh tunnel and configure one of your browsers through it](https://tietohallinto.metropolia.fi/display/tietohallinto/SSH-tunnelointi).
       (use your metropolia username and password)\
       ``$ ssh -Nf -D 8888 <met-username>@shell.metropolia.fi``
   1.  For the terminal access to your server, use double ssh, first from your local machine to shell.metropolia.fi (with your        metropolia username/password):\
        ``$ ssh <your-metropolia-username>@shell.metropolia.fi``
       once connected to metropolia shell, you can connect to your server:\
       ``$ ssh <your-server-username>@<your-server-IP>``

## LAMP

1. Pyydä itsellesi virtuaalikonetta
   1.  Mene osoitteeseen [https://educloud.metropolia.fi/](https://educloud.metropolia.fi/)
   1.  Kirjaudu omatunnuksillasi
   1.  Valitse Services/Catalogs
   1. Valitse Centos 7 x64
      1.  jos haluat PHPn ja PhpMyAdminin valmiiksi asennettuna valitse 'Centos 7 x64 LAMP'
      1. Jos valmis LAMP ei toimi, poista virtuaalikone ja tee uusi virtuaalikone ilman LAMPia ja asenna Apachet ym. tämän ohjeen avulla itse
   1.  Valitse S-Small, Lease time 4 months
   1.  ([Tietohallinnon ohje](https://wiki.metropolia.fi/pages/viewpage.action?pageId=132852058))
2. Odota 10-15 minuuttia koneen valmistumista
   1.  Saat sähköpostiisi tiedot koneen ip-osoitteesta, salasanasta ym
3.  Mikäli käytössäsi on Windows, odottamisen aikana asenna itsellesi jonkinlainen terminaaliohjelmisto (CLI). Suositus on [Gitin mukana tuleva Git Bash](https://git-scm.com/downloads). Mac/Linux käyttäjillä terminaali löytyy vakiona (esim. Macillä se löytyy Applications/Utilities -kansiosta)
4. Käynnistä terminaali ja ota yhteys virtuaalikoneeseen (jos olet koulun verkossa mene suoraan kohtaan b)
   1.  Mikäli et ole koulun koneella tai eduroam-verkossa käytä [Metropolian VPN-etäyhteyttä](https://wiki.metropolia.fi/pages/viewpage.action?pageId=149652071)
   1.  Sitten yhteys virtuaalikoneeseen:\
        ``$ ssh root@IPosoiteJonkaSaitSähköpostissa``
   1.  Tunnus: root Salasana: ks. sähköposti
1.  Parannetaan tietoturvaa. Tehdään uusi tunnus, jotta vältetään
    tekemästä kaikki root-tunnuksella
   1.  Vaihda root-käyttäjän salasana komennolla:\
        ``$ passwd``
   1.  Tee itsellesi uusi käyttäjä:\
        ``$ useradd haluamasiNimi``\
        ``$ passwd haluamasiNimi``
   1.  Lisätään uudelle käyttäjälle sudo-oikeudet:\
        ``$ visudo``
   1.  Terminaaliin aukeaa tekstitiedosto. Etsi tämä rivi:
       ```apacheconf
        ## Allow root to run any commands anywhere
        root ALL=(ALL) ALL
        ```
   1.  Aktivoi kirjoitus painamalla ``i``-kirjainta. Kirjoita em rivin
        jälkeen
       ```apacheconf
        haluamasiNimi ALL=(ALL) ALL
        ```
   1.  Keskeytä editointi esc-näppäimellä. Tallenna muutokset ja sulje
        editori kirjoittamalla ``:wq``
   1.  Testaa, että pääset kirjautumaan uudella käyttäjällä:\
        ``$ logout``\
        ``$ ssh haluamasiNimi@IPosoiteJonkaSaitSähköpostissa``
   1.  Jos et pääse, kirjaudu uudelleen root-tunnuksella ja tee kohdat
        i-m. Jos pääset, mene kohtaan n.
   1.  Lisätään uudelle käyttäjälle ssh oikeudet\
        ``$ vi /etc/ssh/sshd_config``
   1.  Terminaaliin aukeaa tekstitiedosto. Kirjoita sen loppuun
       ```apacheconf
        AllowUsers haluamasiNimi
       ```
   1.  Tallenna ja lopeta editointi kuten edellä
   1.  Käynnistä ssh-palvelu uudestaan\
        ``$ sudo systemctl restart sshd.service``
   1.  Testaa, että pääset kirjautumaan uudella käyttäjällä:\
        ``$ logout``\
        ``$ ssh haluamasiNimi@IPosoiteJonkaSaitSähköpostissa``
   1.  Jos kirjautuminen onnistui uudella tunnuksella (erittäin
        tärkeää), postetaan root-tunnukselta ssh oikeudet\
        ``$ sudo vi /etc/ssh/sshd_config``
   1.  Etsi tekstitiedostosta kohta ``#PermitRootLogin``. Poista #-merkki
        ja kirjoita yes tilalle ``no``. Tallenna ja sulje, kuten edellä
2.  CentOS:n paketinhallinta (tällä asennetaan ja päivitetään ohjelmia)
    on nimeltään YUM. Sen avulla voi esim. hakea päivityksiä, joka on
    suotavaa esim. viikon välein. Päivityskomento on\
    ``$ sudo yum update``
3. Jos valitsin LAMP asennuksen kohdassa 1, siirry kohtaan 7. a).  Muuten asenna LAMP [tämän linkin ohjeilla](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-centos-7) ja sen jälkeen siirry kohtaan 7. d)
   1.  Aja komento \
       ``$ sudo vi /var/www/html/info.php``
   1.  Aktivoi editointi painamalla i. Lisää tämä teksti:
       ```php
       <?php
         phpinfo();
       ?>
       ```
   1.  Lopeta editointi ja tallenna. Paina ``esc`` ja kirjoita ``:wq``
   1.  Asennus testataan avaamalla selaimessa osoite\
        ``IPosoiteJonkaSaitSähköpostissa``
        sekä\
        ``IPosoiteJonkaSaitSähköpostissa/info.php``
   1.  Jos Step one kohdassa Testing 1 2 3 sivu ei tule näkyviin, vika
        on tod. näk palomuurissa. Lisätään tarvittavat portit
        palomuuriin.
        Portti 80 (alla oleva yhdelle riville)\
        ``$ sudo firewall-cmd --permanent --zone=public --add-service=http``\
        Portti 443 (alla oleva yhdelle riville)\
        ``$ sudo firewall-cmd --permanent --zone=public --add-service=https``\
        Käynnistetään palomuuri uudestaan\
        ``$ sudo firewall-cmd --reload``
   1.  Step three kohdassa puhutaan moduuleista. Mitään moduuleja ei
        tarvitse asentaa
4. Asennetaan phpMyAdmin (JOS VALITSIT LAMP ASENNUKSEN, HYPPÄÄ KOHTAAN 1.g)
   1.  Lisätään sopiva rpm paketti (Alla oleva komento yhdelle riville)\
        ``$ sudo rpm -iUvh http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm``
   1.  Asennetaan phpMyAdmin\
        ``$ sudo yum -y install phpmyadmin``
   1.  Muokataan asetukset, jotta miltä tahansa selaimelta pääsee
        phpMyAdminiin käsiksi (ei turvallisin vaihtoehto, mutta
        toistaiseksi näin)\
        ``$ sudo vi /etc/httpd/conf.d/phpMyAdmin.conf``
   1.  Muokkaa tiedostoa alla olevan mallin mukaan\
       <img src=phpmyadmin.png>
   1.  Tallenna muutokset kuten aiemmin
   1.  Käynnistä apache uudelleen:\
        ``$ sudo systemctl restart httpd``
   1.  Mene selaimella osoitteeseen\
        ``IPosoiteJonkaSaitSähköpostissa/phpmyadmin``
   1.  Kirjaudu sisään. Tunnus: root, Salasana: ks sähköposti
5. Tee phpMyAdminissa uusi käyttäjä, koska root:ia ei kannata käyttää web-sovelluksissa
   1.  Valitse Users välilehti
   1.  Täytä nimi ja salasana
   1.  Ruksaa kohta Create database with same name and grant all
        privileges.
   1.  Muita asetuksia ei tarvitse muuttaa.
   1.  Paina alareunasta Go

1. Rootin kotihakemisto on ``/var/www/html``. Tehdään omalle käyttäjälle public_html-hakemisto:
   1.  Aloita kohdasta '[Enable Apache Userdirs](https://www.unixmen.com/linux-basics-enable-apache-userdir-centos-7rhel-7/)'.
        Käytä sudoa ennen komentoja.
   1.  userdir.conf-tiedostosta vaihdetaan vain näitä kahta riviä:
       ```apacheconf
       UserDir enabled haluamasiNimi
       UserDir public_html
       ```
1. Linkin ohjeissa unixmenuser on sama kuin haluamasiNimi aikaisemmin
   1.  visit: ``http://<ip-address>/~<username>/``
1. Jos koodaat PHP:llä ja haluat muuttaa tallennettavien tiedostojen maksimikokoa (vakio 2MB)
   1.  Aja komento\
        ``$ sudo vi /etc/php.ini``
   1. Etsi rivi upload_max_filesize = 2M
      1.  haku tehdään merkillä /
      1. esim: /upload
   1.  Aseta haluamasi arvo esim ``upload_max_filesize = 64M``
   1.  Tallenna ja poistu editorista kuten aiemmin
   1.  Uudelleenkäynnistä Apache\
        ``$ sudo systemctl restart httpd``
2. Kun et enää tarvitse virtuaalikonetta poista se käytöstä educloud.metropolia.fi:ssä kohdassa ``lifecycle -> retire this vm``
   1.  Virtuaalikoneista ei oteta varmuuskopioita, joten tee tämä vasta
        sitten kun olet saanut tehtävästä/kurssista arvosanan

## Install and configure NodeJS

1.  Install node for Centos: [https://github.com/nodesource/distributions/blob/master/README.md#rpm](https://github.com/nodesource/distributions/blob/master/README.md#rpm)
    use the # No root privileges version, so something like:\
    ``$ curl -sL https://rpm.nodesource.com/setup_12.x | sudo bash -``\
    ``$ sudo yum install -y nodejs``
1. Configure Apache httpd server as a reverse proxy to node server:
   1.  create/edit an apache configuration file:\
        ``$ sudo vi /etc/httpd/conf.d/node.conf``
   1.  add the following content:
       ```apacheconf
       <VirtualHost *:80>
         ProxyPreserveHost On
         ProxyPass /app/ http://127.0.0.1:3000/
         ProxyPassReverse /app/ http//:127.0.0.1:3000/
       </VirtualHost>
       ```
1.   save and restart apache server\
        ``$ sudo systemctl restart httpd``
1.  give permission to apache server to visit URL\
        ``$ sudo setsebool -P httpd_can_network_connect 1``
2. Install and run your node application:
   1.  make sure you are in your home folder:\
        ``$ cd``
   1.  clone your app (choose HTTPS)\
        ``$ git clone https://gitlab.metropolia.fi/<your-repo>``
   1.  go to the cloned repo\
        ``$ cd <your-repo>``
   1.  eventually, check that you are in the right branche (checkout if
        not)
   1.  install dependencies\
        ``$ npm i``
   1.  create/edit .env file with your db credentials (you set in LAMP point 9.)
       ```apacheconf
        DB_HOST=127.0.0.1
        DB_USER=<your-db-user>
        DB_PASS=<your-db-user_password>
        DB_NAME=<your-db-name>
        ```
1. make sure that you deployed your database with its tables [https://github.com/ilkkamtk/wop-starters/blob/week2-1/tables.txt](https://github.com/ilkkamtk/wop-starters/blob/week2-1/tables.txt)
   1.  run your application\
        ``$ node app.js``
   1.  test, open a browser and visit ``http://<your-server-ip>/app/cat``
   1.  if you want to let your app running forever, run it as a
        background task (note the ampstamp & at the end)\
        ``$ node app.js &``
   1.  and if you want to stop your background app:\
        ``$ pkill node``

## Extra and resources
- [Unix/linux command](https://centoshelp.org/resources/commands/linux-system-commands/)
- [vim commands](https://vim.rtorr.com/) (works with ``vi`` and ``visudo``)
- [autocomplete](https://www.cyberciti.biz/faq/fedora-redhat-scientific-linuxenable-bash-completion/) (make typing command faster using tab key)
- [sudo autocomplete](http://www.webupd8.org/2010/03/how-to-autocomplete-commands-preceded.html)  (so tab key works when you type ``sudo ...``)
- [installing extra packages](https://wiki.centos.org/AdditionalResources/Repositories)
