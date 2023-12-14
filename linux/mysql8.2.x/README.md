## docker部署mysql说明

（使用时跳过此过程）

```shell
# 拉取基础镜像
docker pull mysql:8.2

# 标记镜像
docker tag mysql:8.2 registry.cn-hangzhou.aliyuncs.com/openyourdream/mysql:8.2.0

# 推送镜像
docker push registry.cn-hangzhou.aliyuncs.com/openyourdream/mysql:8.2.0
```

### 部署脚本

docker-compose.yml文件

```shell
version: '3'
services:
  mysql:
    image: 'registry.cn-hangzhou.aliyuncs.com/openyourdream/mysql:8.2.0'
    restart: unless-stopped
    privileged: true
    command:
      - '--lower_case_table_names=1'
      - '--max_connections=1000'
      - '--character_set_server=utf8'
      - '--character_set_filesystem=utf8'
      - '--collation-server=utf8_general_ci'
      - '--init-connect="SET NAMES utf8"'
      - '--init_connect="SET collation_connection = utf8_general_ci"'
      - '--skip-character-set-client-handshake'
      - '--sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_ENGINE_SUBSTITUTION'
    volumes:
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
      # - './config/my.cnf:/etc/my.cnf'
      - './dbsql:/docker-entrypoint-initdb.d'
      # - '/data/docker-volumes/mysql:/var/lib/mysql'
    ports:
      - '13306:3306'
    environment:
      MYSQL_ROOT_PASSWORD: openYourDream-123456
    # network_mode: host
    container_name: mysql
```

### 操作命令

```shell
# 启动
docker-compose up -d

# 停止命令
docker-compose down
```

