# nginx

```bash



docker run -d --restart=unless-stopped -p 8221:80 \
-v /home/cnhis/webconfig/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /home/cnhis/webconfig/nginx/html:/home/webconfig \
-v /home/cnhis/webconfig/nginx/log:/var/log/nginx \
--name insunginx nginx
```

