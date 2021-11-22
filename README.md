# website
Docker code to release the website and backend

Docker was already installed

# Installed Mysql


## Make sure docker can be used as normal user (procedure by SNAP)
```shell
sudo adduser $USER docker
```

## Install docker compose
```shell
sudo curl -L "https://github.com/docker/compose/releases/download/v2.1.1/docker-compose-linux-x86_64" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Stop the running nginx

```shell
service nginx stop
```

Create a network

```shell
docker network create web
```

All next steps are done as root so we have full access to the /var/www

Add the root id_rsa.pub to the github deploy keys of https://github.com/eureka-clusters/website

```shell
mkdir /var/www/portal
cd /var/www/portal
git clone git@github.com:eureka-clusters/website.git .

docker-compose up 
```

Create a database (mysql) as root

```shell
apt-get install mysql
```

Mysql is password less when logged in as root, so you can always use ```mysql``` as root to login

Create a user

```mysql
CREATE USER 'portal_user'@'172.%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON `portal_database`.* TO 'portal_user'@'172.%';
FLUSH PRIVILEGES;
```

Make sure that mysql is not bound to ```127.0.0.1``` only so comment ```bind_address=127.0.0.1``` in ```/etc/mysql/mysql.conf.d/mysqld.cnf```

Update ```/var/www/portal/conf/doctrine_orm.global.php``` and ```/var/www/portal/conf/oauth2.services.global.php``` so all correct information is in

doctrine_orm.global.php
```php
<?php use Doctrine\DBAL\Driver\PDO\MySQL\Driver;

return [
    'doctrine' => [
        'connection' => [
            'orm_default' => [
                'driverClass' => Driver::class,
                'params'      => [
                    'host'          => '172.17.0.1', #points to mysql database
                    'port'          => '3306',
                    'user'          => 'portal_user',
                    'password'      => 'password',
                    'dbname'        => 'portal_database',
                    'driverOptions' => [
                        PDO::MYSQL_ATTR_INIT_COMMAND => "SET NAMES 'UTF8'",
                    ]
                ],
            ],
        ],
    ],
];
```


