# Short deploy instruction
- Clone this repo.
- Copy env.example to .env
- Edit .env to your needs
- Use nginx reverse proxy
- Open TURN 3478/tcp and 3478/udp ports in firewall for external access
- Run `docker-compose up -d`
---
Add in nginx.conf in http section:
```bash
http {
#...
  keepalive_timeout 604800;
  proxy_connect_timeout 604800;
  proxy_send_timeout 604800;
  proxy_read_timeout 604800;
#...
}

```
Add in site conf file:
```bash
#...
map $http_upgrade $connection_upgrade {
    default upgrade;
    ''      close;
}
#...
upstream signaling {
  server 127.0.0.1:8888;
}
#...
server {
#...
    location /standalone-signaling/ {
        proxy_pass http://signaling/;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
    location /standalone-signaling/spreed {
        proxy_pass http://signaling/spreed;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
#...
}
#...
```
- Fill Talk (Spreed) preferences with the addresses and secrets from .env:
![image](https://github.com/drlight17/nc-talk-rec-docker/assets/37434652/2e6f863b-f5d4-4988-86cc-e287a13e8370)
![image](https://github.com/drlight17/nc-talk-rec-docker/assets/37434652/b3a1a441-3f48-4510-8827-5b3c576c1a58)

