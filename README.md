# Docker nginx V-host

## Server set
### Container
```bash
$ sudo docker run -itd -p 8002:80 --name serv-a nginx
$ sudo docker run -itd -p 8003:80 --name serv-a nginx
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

