# MySQL testing and tunnel to mysql.metropolia.fi

## Creating database to mysql.metropolia.fi
1. Login to https://amme.metropolia.fi/mysql with your metropolia account
1. Set password for your database. Don't use the same password as your Oma account's password because the database password will be written in plain text to configuration files.

## Connecting to graphical UI
1. Login to https://users.metropolia.fi/phpMyAdmin/ with your Oma username and **database** password (the one you did in amme.metrpolia.fi/mysql)

## Connecting code on localhost to mysql.metropolia.fi
1. Use [vpn](https://wiki.metropolia.fi/pages/viewpage.action?pageId=149652071#VPN-et%C3%A4yhteydet-VPN-apuohjelmanasennusjak%C3%A4ytt%C3%B6omissatietokoneissa)
   * this is not possible when using Zoom
1. Use ssh tunnel
   * Open terminal or powershell
   * run `sudo ssh -Ngf -L 3306:mysql.metropolia.fi:3306 username@shell.metropolia.fi`
      * replace `username` with your Oma username
   * you will be prompted for sudo password (your computer's admin password)
   * second you will be prompted for shell.metropolia.fi password (Oma password)
   
## Connect to mysql.metropolia.fi with express
1. Use [this repo](https://github.com/ilkkamtk/mysql-example) as an example
