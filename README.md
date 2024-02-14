# Docker nginx V-host

## 💡V-host - nginx image
### Container
```bash
$ sudo docker run -itd -p 8001:80 --name lb nginx
$ sudo docker run -itd -p 8002:80 --name serv-a nginx
$ sudo docker run -itd -p 8003:80 --name serv-b nginx
```
### lb
- config/default.conf
```conf
upstream serv {
        server serv-a:80;
        server serv-b:80;
}
server {
        listen 80;

        location /
        {
                proxy_pass http://serv;
        }
}
```
### serv-a
- html set
```bash
$ docker cp serv-a/index.html serv-a:/usr/share/nginx/html/
```
### serv-b
- html set
```bash
$ docker cp serv-b/index.html serv-b:/usr/share/nginx/html/
```

## 💡V-host - Dockerfile
### Dockerfile
- lb
```
FROM nginx
COPY config/default.conf /etc/nginx/conf.d/
```
- serv-a, serv-b
```
FROM nginx
COPY index.html /usr/share/nginx/html
```

### Image build
- 생성하는 이미지의 Dockerfile 위치에서 명령어 실행
```bash
$ docker build -t n-s-1:0.1.0 -f Dockerfile .
$ docker build -t n-s-2:0.1.0 -f Dockerfile .
$ docker build -t lb:0.1.0 -f Dockerfile .
```

### Container
```bash
$ docker run --name serv-a -d -p 9001:80 n-s-1:0.1.0
$ docker run --name serv-b -d -p 9002:80 n-s-2:0.1.0
$ docker run --name lb -d -p 8001:80 lb:0.1.0
```

## ✅Network Connect
### Create
```bash
$ docker network create DockerfileNet
```

### Connect
```bash
$ docker network connect DockerfileNet serv-a
$ docker network connect DockerfileNet serv-b
$ docker network connect DockerfileNet lb
```

### Connection check
```bash
$ docker network inspect DockerfileNet
```

## 📢실행결과
- lb(localhost:8001)을 호출할 때 마다 서로 다른 서버 이미지에 연결되는 것을 확인
![image](https://github.com/choi3179/docker-nginx-vhost/assets/66417882/63d9836c-840a-412c-a3cb-4a09a69677f1)


