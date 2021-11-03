# docker-nginx-certbot-route53

## How to use:
1. Create a `.env` file in the root directory of this project with the environment variables in `.sample-env`. This will include your domain that this app will listen for, the contact email to register with letsencrypt, and the AWS access key and secret for a IAM account with the permissions described here: https://certbot-dns-route53.readthedocs.io/en/stable/.
2. Edit the nginx configuration file at nginx/templates/app.conf.tmpl to setup static resources or proxying. NOTE: `${DOMAIN}` in the template will be substituted for the environment variable provided in `.env`.
3. Run the app with the normal docker-compose commands: 
```
$ docker-compose up -d
```

## Connecting with other docker-compose apps
If using nginx to proxy to other docker-compose apps, ensure they can connect on the same docker network by using the `docker-compose.override.yaml` network override when starting all apps.
1. Create the network external to docker compose:
```
$ docker network create nginx-shared
```
2. Configure nginx to proxy to other app:
Example:
Given some other compose app that listens on port 80 in the container
```
version: '3.9'
services:
  other:
    image: <some_image>
    ports:
     - "8080:80"
```
Add a block like the one below to have nginx proxy the subdomain `other.${DOMAIN}` to the other app:
```
server {
  server_name other.${DOMAIN};

  location / {
    proxy_pass         http://other:80;
    proxy_set_header   X-Forwarded-For $remote_addr;
    proxy_set_header   Host $http_host;
  }

  listen [::]:443 ssl;
  listen 443 ssl;
  ssl_certificate /etc/letsencrypt/live/${DOMAIN}/fullchain.pem;
  ssl_certificate_key /etc/letsencrypt/live/${DOMAIN}/privkey.pem;
  include /etc/letsencrypt/options-ssl-nginx.conf;
  ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}
```
3. Start the nginx server and other app(s) with the network override:
```
$ docker-compose -f docker-compose.yml -f compose-override/docker-compose.override.yml up -d
```
```
$ docker-compose -f docker-compose.yml -f path/to/compose-override/docker-compose.override.yml up -d
```