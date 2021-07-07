# Infra
The whole infra looks like this, and in this article I'll explain how to setup nginx using docker and access vue static files
![](/static_files/01_project.png)


# Vue settings
If you program has multiple route, need to configure index.js under router
![](/static_files/02_router_history.png)

Create the static files
> npm run build

![](/static_files/03_npm_static_folder.png)

These are the location for the static files that nginx will be using

# Nginx with docker

This is Nginx configuration file, can just copy it and remember to change "change_to_your_domain_or_ip" part, there must be try_files part, otherwise your router won't work under Nginx

```
location / {
            try_files $uri $uri/ /index.html;
```

## Nginx configuration file,
```
events{
}



http {

    include mime.types;

    gzip on;
    gzip_comp_level 3;
    gzip_types text/css;
    gzip_types text/javascript;


    server {
        listen 80;
        server_name change_to_your_domain_or_ip;
        root /usr/share/nginx/html;


        location / {
            try_files $uri $uri/ /index.html;
        }

        location ~*.(css|js|jpg|png)$ {
            access_log off;
            add_header Cache-Control public;
            add_header Pragma public;
            add_header Vary Accept-Encoding;
            expires 60m;
        }

    }
}
```


# Start Docker

Commands for starting docker

```
sudo docker container run -p 8091:80 \
 -v "/home/ops/ops_project/a03_ph_elk/frontend/dist:/usr/share/nginx/html:ro" \
 -v "/home/ops/ops_project/a03_ph_elk/docker_nginx_config/nging_docker_config.conf:/etc/nginx/nginx.conf:ro" \
 nginx:1.20-alpine
```
![](/static_files/04_start_docker.png)


Check that the docker is really running
>docker ps

Verify, and check that can really see the vue page
>http://{domain_name}:8091