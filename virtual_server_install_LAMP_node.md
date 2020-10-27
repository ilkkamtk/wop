# Metropolia Educloud virtual computer (CentOS server)

## Working from outside metropolia network

1. Configure [Metropolia VPN](https://wiki.metropolia.fi/display/itservices/Install+and+Use+VPN+Utility+Program+Installation+on+Your+Own+Computer)
2. Other options (e.g. during zoom session to reduce vpn bandwidth)
   1.  to test your app/webpages, create a [ssh tunnel and configure one of your browsers through it](https://tietohallinto.metropolia.fi/display/itservices/SSH+Tunnelling).
       (use your metropolia username and password)\
       ``$ ssh -Nf -D 8888 <your-metropolia-username>@shell.metropolia.fi``
   1.  For the terminal access to your server, use double ssh, first from your local machine to shell.metropolia.fi (with your        metropolia username/password):\
        ``$ ssh <your-metropolia-username>@shell.metropolia.fi``
       once connected to metropolia shell, you can connect to your server:\
       ``$ ssh <your-server-username>@<your-server-IP>``

## LAMP (well, P is optional)

1. Order your virtual server
   1.  visit [https://educloud.metropolia.fi/](https://educloud.metropolia.fi/)
   1.  login with your metropolia credentials
   1.  navigate to Services/Catalogs
   1.  choose Centos 7 x64 - Basic Install with IP address setup done
   1.  Lease time 4 months, size doesn't matter much (medium or large will have more cpu/memory)
   1.  ([Helpdesk page](https://tietohallinto.metropolia.fi/display/itservices/Educational+educloud+virtual+services))
2. Wait for 10-15 minutes for the server to be created
   1.  You will receive an email with the IP address of the server and the root password (root is default administrator on any linux/unix operating system)
3. Open a terminal (CLI). On windows computer, install [bash shell](https://docs.microsoft.com/en-us/windows/wsl/install-win10) or [Git Bash](https://gitforwindows.org/). Mac/Linux already have a terminal to start with (e.g. on Mac under Applications/Utilities, on gnome/mate linux desktop with ``ALT + CTRL + T`` shortcut)
4. From terminal, use ssh protocol to connect to your virtual server
   1.  If you are not in metropolia network, use VPN or double ssh (check first section)
   1.  Run the following command:\
        ``$ ssh root@IPaddress``
   1.  substitute IPaddress with the address of your server (you got in the mail), root is the username (keep it as it is). After pressing enter, it will ask for passowrd (copy/paste from the mail). Note, blind typing password (the cursor will not move nor show stars nor nothing while you type); it a security measure: if anyone look over your shoulders, they cannot guess the lenght of your password.
1.  After login: first security measures, change root password and create yourself a user account: avoid
to do everything as root! If someone crack your root account, s/he can destroy the full server.
If s/he “only” crack your user account, s/he will be sandboxed (and can do less damages).
   1.  Change root password:\
        ``# passwd``
   1.  Create user account:\
        ``# useradd wantedUsername``\
        ``# passwd wantedUsername``
   1.  Give that user sudo (super user do) priviledges:\
        ``# visudo``
   1.  Navigate to the line that shows:
       ```apache
        ## Allow root to run any commands anywhere
        root ALL=(ALL) ALL
        ```
        To move in the text, use arrow keys or ``hjkl`` keys (e.g. to move 92 lines down, press ``92j``).
   1.  Add a new line and enter in insert mode by typying ``o``-key (notice the INSERT on the last line).
        Type
       ```apache
        wantedUsername ALL=(ALL) ALL
        ```
   1.  To escape from insert mode, type ``esc``-key. Save and quit the editor by typing ``:wq``
   1.  Give your user the permission to ssh to the server by editing ssh deamon configuration:\
        ``# vi /etc/ssh/sshd_config``
   1.  Navigate to the end of the file (e.g. with ``G``-key (captital g)) and add the following line:\
       ```apache
        AllowUsers wantedUsername
       ```
   1.  Escape, save and quit.
   1.  Restart the ssh deamon (service)\
        ``$ sudo systemctl restart sshd``
   1.  Test that you can login with the new account:\
        ``# logout``\
        (alternative to logout is to use ``CTRL+D`` shortcut).\
        ``$ ssh wantedUsername@IPaddress``
   1.  If and only if the login is successful with your new account, disable the root login\
        ``$ sudo vi /etc/ssh/sshd_config``
   1.  Search the line ``#PermitRootLogin`` (to search with vi, type ``/``-key, then your search pattern and enter). Once found, remove the #-mark (e.g. use ``x``-key to delete the character under the cursor).
        And replace yes with ``no``.\
        Escape, save and quit.
   1. Restart the ssh deamon again:\
        ``$ sudo systemctl restart sshd``
2.  The CentOS package manager (to install/update applications and operating system)
    is YUM. For example, to maintain the operating system with the latest bug and security fixes, run the following command about every weeks:\
    ``$ sudo yum update``
3.  Install apache web server:\
    ``$ sudo yum install httpd``
    1.  start it:\\
        ``$ sudo systemctl start httpd``
    2.  make sure that the web server will always start (e.g. if server reboot):\
        ``$ sudo systemctl enable httpd``
4. Open the firewall to allow web traffic through http (port 80) and https (port 443):\
        ``$ sudo firewall-cmd --permanent --zone=public --add-service=http --add-service=https``\
        And reload the firewall\
        ``$ sudo firewall-cmd --reload``
5. In your browser, open the URL: ``http://<ip-address>/`` (substitute with your IP address). You should see your Apache web server welcome page.

1. The root of your web server is on following path: ``/var/www/html``; but it require root privileges to add/edit/delete files. to create a public_html-folder for your user:
   1.  Follow these steps '[Enable Apache Userdirs](https://www.unixmen.com/linux-basics-enable-apache-userdir-centos-7rhel-7/)'.
        Substitue the "unixmenuser" with your username "wantedUsername".
   1.  visit: ``http://<ip-address>/~<wantedUsername>/``

1. Install MariaDB server
   1. (In progres....)

## Install and configure NodeJS

1.  Install node for Centos: [https://github.com/nodesource/distributions/blob/master/README.md#rpm](https://github.com/nodesource/distributions/blob/master/README.md#rpm)
    use the # No root privileges version, so something like:\
    ``$ curl -sL https://rpm.nodesource.com/setup_12.x | sudo bash -``\
    ``$ sudo yum install -y nodejs``
1. Configure Apache httpd server as a reverse proxy to node server:
   1.  create/edit an apache configuration file:\
        ``$ sudo vi /etc/httpd/conf.d/node.conf``
   1.  add the following content:
       ```apache
       <VirtualHost *:80>
         ProxyPreserveHost On
         ProxyPass /app http://127.0.0.1:3000
         ProxyPassReverse /app http//:127.0.0.1:3000
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
   1.  run your application\
        ``$ node app.js``
   1.  test, open a browser and visit ``http://<ip-address>/app/``
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
