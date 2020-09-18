# 使用教程

## debian or ubuntu
```shell
apt-get install curl -y
bash <(curl -s -L https://git.io/ssrmu.sh)
```

## centos7

```shell
yum install curl -y
bash <(curl -s -L https://git.io/ssrmu.sh)
```

## 系统优化脚本

```shell
bash <(curl -s -L https://git.io/optimize.sh)
```

## 2020.09.17 支持docker

```shell
curl -sSL https://get.docker.com/ | sh
service docker restart
systemctl enable docker
```

api：

```shell
docker run -d --name=ssrmu -e DNS_1=1.0.0.1  -e DNS_2=8.8.8.8 -e SPEEDTEST=0 -e MU_SUFFIX=microsoft.com -e NODE_ID=节点ID -e API_INTERFACE=modwebapi -e WEBAPI_URL=需要对接的地址 -e WEBAPI_TOKEN=前端设置的token --network=host --log-opt max-size=50m --log-opt max-file=3 --restart=always marisn/ssrmu
```

sql:

```shell
docker run -d --name=ssrmu -e DNS_1=1.1.1.1  -e DNS_2=8.8.8.8 -e SPEEDTEST=48 -e MU_SUFFIX=microsoft.com -e NODE_ID=节点ID -e API_INTERFACE=glzjinmod -e MYSQL_HOST=前端ip -e MYSQL_USER=数据库用户 -e MYSQL_DB=数据库 -e MYSQL_PASS=密码 --network=host --log-opt max-size=50m --log-opt max-file=3 --restart=always marisn/ssrmu
```

```shell
docker stop $(docker ps -a -q)
docker rm $(docker ps -a -q)
docker restart $(docker ps -a -q)
```




