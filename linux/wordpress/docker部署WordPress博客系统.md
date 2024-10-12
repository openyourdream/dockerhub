# docker部署WordPress博客系统



docker-compose.yml

```shell
version: '3'
services:
  mysql:
    image: 'mysql:latest'
    restart: always
    privileged: true
    tty: true
    volumes:
      - '/etc/localtime:/etc/localtime:ro'
      - './dbsql:/docker-entrypoint-initdb.d'
    ports:
      - '3306:3306'
    networks:
      - filink-net
    environment:
      MYSQL_ROOT_PASSWORD: TebNmEddBf
      MYSQL_DATABASE: wordpress
    container_name: mysql
  wordpress:
    image: 'wordpress:latest'
    restart: always
    privileged: true
    tty: true
    volumes:
      - '/etc/localtime:/etc/localtime:ro'
    ports:
      - '8080:80'
    networks:
      - filink-net
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: TebNmEddBf
    container_name: wordpress
networks:
  filink-net: null
```



init.sql

```sql
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
ALTER USER 'root'@'%' IDENTIFIED BY 'TebNmEddBf';
flush privileges;
```



构建命令

```shell
docker pull mysql:latest
docker pull wordpress:latest

chmod -R  755 dbsql/

docker-compose up -d mysql
docker logs -f mysql


docker-compose up -d wordpress
docker logs -f wordpress

admin
xXmoZTvQ$10IW7$PXr
```



电商

Kadence主题，Print Shop模板 中国



