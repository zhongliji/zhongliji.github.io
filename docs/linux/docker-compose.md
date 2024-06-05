## docker-compose.yaml
```
version: '2'
networks:
  net1:
    driver: bridge
services:
  nginx:
    container_name: nginx
    image: nginx:li
    restart: always
    ports:
      - "80:80"
    volumes:
      - /root/web:/usr/share/nginx/html:ro
    networks:
      net1:
  mysql:
    container_name: mysql
    image: mysql
    restart: always
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=liyang
    volumes:
      - /root/mysql:/var/lib/mysql
    networks:
      net1:
```
## docker-compose deploy guacamole 
```
version: '2'
services:
  guacd:
    container_name: guacd
    image: guacamole/guacd
    restart: always
  mysql:
    container_name: mysql
    image: mysql
    restart: always
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=liyang
  guacamole:
    container_name: guacamole
    image: guacamole/guacamole
    restart: always
    ports:
      - "81:8080"
    links: 
      - guacd
      - mysql
    environment:  
      - MYSQL_HOSTNAME=mysql
      - MYSQL_PORT=3306
      - MYSQL_DATABASE=guacamole_db
      - MYSQL_USER=liyang
      - MYSQL_PASSWORD=liyang
      - GUACD_HOSTNAME=guacd
      - GUACD_PORT=4822
```
## config mysql database
```
docker cp initdb.sql mysql:/root/
docker exec -it mysql /bin/bash
mysql -u root -pliyang
create database guacamole_db;
mysql -u root -pliyang guacamole_db < initdb.sql
create user 'liyang'@'%' identified by 'liyang';
grant all on guacamole_db.* to 'liyang'@'%';
select user,host from mysql.user;
```
## compose cli
```
docker-compose up -d
docker-compose exec nginx sh
docker-compose exec mysql bash
docker-compose ps
docker-compose down
docker-compose stop
docker-compose start
docker-compose logs

docker-compose up -d --scale nginx=3
docker-compose exec --index=1 nginx sh
```
