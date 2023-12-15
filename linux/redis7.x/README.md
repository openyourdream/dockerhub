## docker部署redis说明

（使用时跳过此过程）

```shell
# 拉取基础镜像
docker pull redis:7.2.3

# 标记镜像
docker tag redis:7.2.3 registry.cn-hangzhou.aliyuncs.com/openyourdream/redis:7.2.3

# 推送镜像
docker push registry.cn-hangzhou.aliyuncs.com/openyourdream/redis:7.2.3
```

### 部署脚本

docker-compose.yml文件

```shell
version: '3'
services:
  redis:
    image: 'registry.cn-hangzhou.aliyuncs.com/openyourdream/redis:7.2.3'
    restart: unless-stopped
    privileged: true
    # 启动redis服务并添加密码为：openyourDream-123456,默认不开启redis-aof方式持久化配置
    command: redis-server /etc/redis/redis.conf --requirepass openyourDream-123456 --appendonly no
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - './config/redis.conf:/etc/redis/redis.conf'
      # - '/data/docker-volumes/redis:/data'
    ports:
      - '16379:6379'
    # network_mode: host
    container_name: redis
```

### 操作命令

```shell
# 启动
docker-compose up -d

# 停止命令
docker-compose down
```

