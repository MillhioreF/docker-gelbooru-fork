# docker-gelbooru-fork

The easiest way to deploy a container with Gelbooru-fork! Now you can privately host all your prictures using all the power of the boorus: tag support, user support, made for huge libraries. DON'T USE THIS CONTAINER FOR PUBLICLY HOSTING BOORUS UNLESS YOU KNOW WHAT YOU'RE DOING, AS I CAN'T GUARANTEE SECURITY!

## Fastest way to deploy

Install and run docker-toolbox with the instructions on this page, it might take a few minutes: https://docs.docker.com/toolbox/toolbox_install_windows/

Then run the following commands (if you get a permissions error, use something like `cd C:/betterfolder` to navigate out of the default folder):

```
git clone https://github.com/evertonstz/docker-gelbooru-fork.git --recurse-submodules
```
```
docker build -t gelbooru-img docker-gelbooru-fork
```
```
docker run -p 44555:80 --name=gelbooru -dP gelbooru-img
```
remove the cloned repository if you wish.

Runing the command above will deploy a container with the name "gelbooru". It's not yet ready to use, you'll need to set some user preferences on MySQL manually:

Enter your container's bash:
```
docker exec -it gelbooru /bin/bash
```
Make a mysql user called "gelbooru" with password "gelbooru": *
```
mysql -u root -e "CREATE USER 'gelbooru'@'localhost' IDENTIFIED BY 'gelbooru';"
```
Give necessary permissions to the new user:
```
mysql -u root -e "GRANT ALL PRIVILEGES ON * . * TO 'gelbooru'@'localhost';"
```
Creteate the database "gelbooru1" with the user "gelbooru":
```
mysql -u gelbooru -p"gelbooru" -e "create database gelbooru1"
```
Use "exit" to close the container's bash shell.

After that you'll need to go to the following link and sign up with your ADMINISTRATOR info in your web browser: http://192.168.99.100:44555/install/index.php
The IP address for your server is displayed at the very top of the terminal window, if it doesn't match the one here edit the URL so it does.

Wait for the message, and go to Gelbooru main page to test things: http://192.168.99.100:44555
You should now be able to put whatever you want inside gelbooru, in my tests the first image that you upload will have a broken thumbnail, but for the rest of the images it'll work fine.

The administrator(s) page is located at: http://192.168.99.100:44555/admin/index.php

*in case you decide to change the credentials, you'll need to also change the gelbooru config file to match, the file is located at /var/www/html/config.php

## Changing settings file

Edit `\docker-gelbooru-fork\gelbooru-fork\config.php`, then move config.php to the main Docker Toolbox folder and run:
```
docker cp config.php gelbooru:/var/www/html/config.php
```

## Turning off and on the booru

Even when the terminal is closed the booru is still running, until you enter:
```
docker-machine stop
``` 
Booru is now off. To turn it back on:
```
docker-machine start
```
```
docker ps -a
```
Find the container with the image `gelbooru-img` and enter its ID into the next command, e.g.
```
docker start 6c29f96abe8f
```
And you're good to go. The local IP address may change supposedly but I haven't had this issue.

## Paths inside the container you might want to know:

You'll probably need to backup you images and database from time to time, the method is with you (I mounth them locally and backup using a software called borg :> ):

image files:
```
/var/www/html/images
```
thumbnail files:
```
/var/www/html/thumbnails
```
mysql database files:
```
/var/lib/mysql
```

The following foiles you'll only need a backup if made any customization in them, if your installation is default backingup them is redundant, as the Dockerfile can regenerate tem:
apache2 config file:
```
/etc/apache2/sites-enabled/000-default.conf
```
gelbooru config file:
```
/var/www/html/config.php
```
php config file:
```
/etc/php/7.0/apache2/php.ini
```
