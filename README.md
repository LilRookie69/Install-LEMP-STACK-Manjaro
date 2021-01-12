# How to install LEMP Stack and Valet Linux on Manjaro

Current OS : Manjaro Linux 20.2.1 Nibia  
Kernel : 5.4.85-1

Hope it can be usefull

## Install Nginx 

```shell
sudo pacman -S nginx
```
### enable and start ngix services

```shell
sudo systemctl enable nginx
sudo systemctl start nginx
```

## Install php php-fpm
```shell
sudo pacman -S php php-fpm
```

### enable and start php-fpm services

```shell
sudo systemctl enable php-fpm
sudo systemctl start php-fpm
```

## Install Database MariaDB

```shell
sudo pacman -Syu mariadb mariadb-clients libmariadbclient
```

### Settings MariaDB
```shell
sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

### enable and start MariaDB services

```shell
sudo systemctl enable mariadb
sudo systemctl start mariadb
```

### Continue the installation with secure install

```shell
sudo mysql_secure_installation
```

### Creating new user for phpmyadmin
#### Run the code to login the Root Account
```shell
sudo mysql -u root -p
```
#### Create the user
```shell
CREATE USER 'user'@'%' IDENTIFIED BY 'password';
```
#### Change the user privileges
```shell
GRANT ALL PRIVILEGES ON *.* TO 'user'@'%';
```
#### Flush the privileges
```shell
FLUSH PRIVILEGES;
```

## Install phpmyadmin
```shell
sudo pacman -S phpmyadmin
```

### Create tmp files for phpmyadmin
```shell
sudo mkdir /etc/webapps/phpmyadmin/tmp
```

#### Change the permission
```shell
sudo chmod -R 755 /etc/webapps/phpmyadmin/tmp
```

### Add Blobfish and Tmp directory
```shell
sudo nano /etc/webapps/phpmyadmin/config.inc.php
```

#### Add this to config.inc.php
change the 'foo' to 32 character random string  
ex : $cfg['blowfish_secret'] = 'GcKd*meXi+PXM4[Z7iEm/5+K=DJEB-[B';
```shell
$cfg['TempDir'] = '/tmp';
$cfg['blowfish_secret'] = 'foo';
```

### Import this to mysql databases
>https://github.com/phpmyadmin/phpmyadmin/blob/master/sql/create_tables.sql

or get from my raw [backup](https://github.com/LilRookie69/Install-LEMP-STACK-Manjaro/blob/main/rawbackup.md)

### Add Symlink to workdirectory of your nginx
```shell
sudo ln -s /usr/share/webapps/phpMyAdmin/ /usr/share/nginx/html/phpmyadmin
```

## Install Composer
[Composer Documentation](https://getcomposer.org/download/)

### Change composer directory path
simply add it to .bashrc / .zshrc
```shell
export PATH="$PATH:$HOME/.config/composer/vendor/bin"
export CGR_BASE_DIR="$HOME/.config/composer/"
export CGR_BIN_DIR="$HOME/.config/composer/vendor/bin"
```

## Install Laravel Valet
```shell
composer global require cpriego/valet-linux
```
### Continue the installation with
```shell
valet install
```

### Add some alias if you want
if you want it you can add it to your .bash / .zsh
```shell
alias park="valet park && valet link"
alias forget="valet forget `$pwd` && valet unlink"
alias open="valet open"
```

## Enable extension on php.ini files
```shell
sudo nano /etc/php/php.ini
```

### Uncomment those lines
```shell
extension=bz2
extension=curl
extension=gd
extension=mysqli
extension=odbc
extension=pdo_mysql
extension=pdo_odbc
extension=zip
```

## Add localhost to your Nginx Server Block
```shell
sudo nano /etc/nginx/sites-available/localhost.conf
```

### And paste this config
```shell
server{
    listen 80;
    listen [::]:80;

    server_name localhost;

    root /usr/share/nginx/html;
    index index.php index.html index.htm;

    location / {
	autoindex on;
	autoindex_exact_size off;
        index index.php index.htm index.html;
    }

    location ~ \.php$ {
	    include /etc/nginx/fastcgi.conf;
        fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock;
    }
}
```

### Create symlink to sites-enabled
```shell
sudo ln -s /etc/nginx/sites-available/localhost.conf /etc/nginx/sites-enabled/localhost.conf
```
if you want to disable it just simply unlink it with
```shell
sudo unlink /etc/nginx/sites-enabled/localhost.conf
```

## Add user to http and give permission
Add your current user
```shell
sudo usermod -aG http foo
```
change the 'foo' to username  
ex:
```shell
sudo usermod -aG http rdanang
```

### change the permission
```shell
sudo chown foo:http -R bar
```
change the 'foo' to username and the 'bar' to the website folder  
ex:
```shell
sudo chown rdanang:http -R /usr/share/nginx/html
```

### change the permisson to 664 / 755
```shell
sudo chmod -R ug+rw foldername
```
ex:
```shell
sudo chmod -R ug+rw /usr/share/nginx/html
```

## Reboot your computer
```shell
sudo systemctl reboot
```
