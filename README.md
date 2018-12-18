
> Source Code

* [http://jp2.php.net/distributions/php-7.2.13.tar.gz](http://jp2.php.net/distributions/php-7.2.13.tar.gz)
* [http://oss.tinywan.com/uploads/php-7.2.13.tar.gz](http://oss.tinywan.com/uploads/php-7.2.13.tar.gz)
* [https://github.com/phpredis/phpredis/archive/4.2.0.tar.gz](https://github.com/phpredis/phpredis/archive/4.2.0.tar.gz)

> Build Docker Images
```
$ docker build --no-cache -t tinywan/lnmp-php7.2.13:latest .
```

> Login Docker Hub
```
$ docker login --username=tinywan --password=tinywan123
```

> Image Tag 
```
$ docker tag e96dbe0fbbfb tinywan/lnmp-php7.2.13:latest
```

> Image Push 
```
$ docker push tinywan/lnmp-php7.2.13:latest
```

> Cat Images
```
$ docker images
REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
tinywan/lnmp-php7.2.13   latest              e96dbe0fbbfb        43 minutes ago      789MB
```

> Run Container
```
$ docker run -it -d --name lnmp-php7.2 tinywan/lnmp-php7.2.13 /bin/bash
Unable to find image 'tinywan/lnmp-php7.2.13:latest' locally
latest: Pulling from tinywan/lnmp-php7.2.13
32802c0cfa4d: Pull complete 
da1315cffa03: Pull complete 
fa83472a3562: Pull complete 
f85999a86bef: Pull complete 
dae3c45336e0: Pull complete 
0475a69be0d4: Pull complete 
0c44d9a882de: Pull complete 
b36d85e8d77a: Pull complete 
Digest: sha256:bbb4e99dad77cd884ca56b1f1c3f33f2a15f3e2360abe364f8d2a9557fd45e16
Status: Downloaded newer image for tinywan/lnmp-php7.2.13:latest
f31bb449fd8bed26232d0da52911859f229d87dacc1a4b603b55f77bbed40cde
```

> Cat Running Container
```
$ docker ps
CONTAINER ID        IMAGE                    COMMAND             CREATED               PORTS               NAMES
f31bb449fd8b        tinywan/lnmp-php7.2.13   "/bin/bash"         12 seconds ago        9000/tcp            lnmp-php7.2
```
> Input Container
```
$ docker attach f31bb449fd8b
root@f31bb449fd8b:/# cat /etc/issue
Ubuntu 18.04.1 LTS \n \l
```

> PHP Version
```
# /usr/local/php/bin/php -v
PHP 7.3.0 (cli) (built: Dec 18 2018 11:05:01) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.0-dev, Copyright (c) 1998-2018 Zend Technologies
```

> PHP Module
```
# php -m
[PHP Modules]
bcmath
bz2
calendar
Core
ctype
curl
date
dom
exif
fileinfo
filter
ftp
gd
gettext
hash
iconv
imap
json
libxml
mbstring
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
Reflection
session
SimpleXML
soap
sockets
SPL
sqlite3
standard
sysvsem
sysvshm
tokenizer
xml
xmlreader
xmlrpc
xmlwriter
xsl
zip
zlib

[Zend Modules]
```
> PHP Configure
```
/usr/local/php/etc/
```
