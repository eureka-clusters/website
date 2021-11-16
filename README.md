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