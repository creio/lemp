# DOCKER LEMP
- Nginx
- PHP 7.2-fpm
- MySQL
- PHPMyAdmin
- Maildev

## Arch

```sh
pacman -S docker-compose
gpasswd -a user docker
newgrp docker
newgrp users
# reboot
```

## Quickstart

- Install and launch Docker
- `cp .env.dist .env`
- `docker-compose up`

| Service        | Path                    |
| -------------- | ----------------------- |
| Website        | `http://localhost:8080` |
| PhpMyAdmin     | `http://localhost:8081` |
| Mail catcher   | `http://localhost:8082` |
| Logs           | `log/`                  |
| mysql multidb  | `mysql/init/`           |

## Docker images

https://github.com/atillay/docker-images/tree/master/lemp

## Use a virtual host

```sh
# add myhost
nano /etc/hosts

127.0.0.1 localhost.localdomain localhost
::1       localhost.localdomain localhost
127.0.1.1 fluxbb.localdomain fluxbb
127.0.1.2 locdev.localdomain locdev
```

- Change the server name in `docker/nginx/locdev.conf#L2` to `locdev`
- Modify `.env` and set `SERVER_PORT=80`
- Run `$ docker-compose up`
- If it fails make sure no service like Apache is running on port 80

## About MySQL credentials

If you change mysql credentials in .env you have to re-create mysql container:
- Database will be deleted, make a dump with PhpMyAdmin
- Remove mariadb folder : `$ rm -rf docker/mariadb`
- Run : `docker-compose up`
- Re-import your database on PhpMyAdmin

```sh
docker-compose up -d
docker-compose up -d --build

docker-compose up --force-recreate --build
docker image prune -f

docker-compose -h
docker-compose ps
docker-compose down

docker ps -a                   # Lists containers (and tells you which images they are spun from)

docker rm <container_id>       # Removes a container
docker rm $(docker ps -a -q)   # delete all stopped containers with
docker kill $(docker ps -q)    # kill all running containers with

docker images                  # Lists images

docker rmi <image_id>          # Removes an image
docker rmi -f <image_id>       # Forces removal
docker rmi $(docker images -q) # delete all images with

docker network ls
docker network prune
```

## Permissions

```sh
chmod -R 755 www/fluxbb
chmod -R 777 www/fluxbb/cache
chmod -R 777 www/fluxbb/img/avatars
```

## Дамп базы

```sh
docker-compose exec mysql bash
docker-compose exec mysql sh -c 'mysqldump -u<username> -p<password> <database> > ~/dump_`date +%y_%m_%d`.sql'
docker-compose exec mysql sh -c 'mysql -hDB_HOST -uDB_USER -pDB_PASSWORD DB_NAME < ~/dump_`date +%y_%m_%d`.sql'

docker-compose exec mysql sh -c 'mysqldump -uuser -ppass dbflux > /mnt/dump_`date +%y_%m_%d`.sql'
docker-compose exec mysql sh -c 'mysqldump -uuser -ppass dbflux | gzip > /mnt/dump_`date +%y_%m_%d`.gz'
docker cp 72ca2488b353:/dump_`date +%y_%m_%d`.gz dump_`date +%y_%m_%d`.gz
```

```sh
mysqldump -uDB_USER -pDB_PASSWORD DB_NAME > ~/dump_`date +%y_%m_%d`.sql
ssh user@remotehost mysqldump -uDB_USER -pDB_PASSWORD DB_NAME > ~/dump_`date +%y_%m_%d`.sql

# Дамп базы вместе с хранимыми процедурами
mysqldump --routines -uDB_USER -pDB_PASSWORD DB_NAME > ~/dump_`date +%y_%m_%d`.sql

# Дамп схемы базы (БЕЗ ДАННЫХ!)
mysqldump -uDB_USER -pDB_PASSWORD DB_NAME -d > file.sql

# Загрузить данные из дампа
mysql -uDB_USER -pDB_PASSWORD DB_NAME < file.sql

# Сжатая
mysqldump -uDB_USER -pDB_PASSWORD DB_NAME | gzip > ~/dump_`date +%y_%m_%d`.gz
gunzip < ~/file.gz | mysql -uDB_USER -pDB_PASSWORD DB_NAME
```

## Gen domain

[eschalot](https://github.com/creio/eschalot)

## Tor permission

```sh
chmod 700 /var/lib/tor/hidden_service
# add private_key
```
