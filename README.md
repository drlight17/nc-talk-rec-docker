# Short instruction
- Clone this repo.
- Copy env.example to .env
- Edit .env to your needs
- Use nginx reverse proxy
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
