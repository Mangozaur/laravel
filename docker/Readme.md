## Devel docker image

**Project setup**

Set up variables in .env file. 

Place to /etc/hosts (replace $APP_HOST with value from .env): 
```
127.0.0.1 $APP_HOST.dev static.$APP_HOST.dev
```

### First run
All commands are expecting to start from this (./docker) directory as root directory:

**Build images**: 
```
docker-compose build
docker-compose up --no-start
```

**Install nodejs modules**: 
```
docker-compose run --rm --entrypoint npm node install
```

**Apply security fixes to modules (if you want)**: 
```
docker-compose run --rm --entrypoint npm node audit fix
```

**Config Docker**
Add path to the app folder to the Docker permissions first. Open "Settings" -> "Resources" -> "File sharing", add path to the app directory. Restart Docker.

**Start containers**:
```
docker-compose up -d
```

**Create new Laravel project**
```
docker-compose exec -w /local/www app sh -c 'rm -rf public && composer create-project laravel/laravel .'
```

**Prepare .env**
Set up correct values in `/www/.env` file.

DB_CONNECTION=mysql
DB_HOST=*${IMAGE_NAME}*-mysql
DB_PORT=3306
DB_DATABASE=App
DB_USERNAME=root
DB_PASSWORD=root

*${IMAGE_NAME}* is the same name as in docker/.env

```
docker-compose exec -w /local/www app sh -c 'composer require "laravel/ui:>=2.0.0" && composer require "barryvdh/laravel-debugbar:>=3.1" --dev'
```

**Run initial migrations**: 
```
docker-compose exec -w /local/www app php artisan migrate:refresh --seed
```

**HTTPS**:

App is generating self-signed SSL certificate. You can do any of these steps:
1. Add CA cert docker/ssl/CA/ca.pem as thrusted to your system/browser
2. Open app page in a browser, oped details, select certificate and set it as thrusted
3. Replace CA cert in docker/ssl/CA to your own and rebuild Nginx image

## Usage
All commands are expecting to start from this (./docker) directory as root directory:

**Run detached**: docker-compose up -d

**Stop**: docker-compose stop

**Start again**: docker-compose start

## Email testing
SMTP service available on host mail:25 in the container.

Web UI available on 127.0.0.1:1080

## Troubleshooting

If you have problems with node container start - remove www/node_modules directory if exists and follow "Install node modules" step.

CORS: if you need add domain to CORS rules for static.* domain, add it to $AccessControlAllowOrigin map in docker/nginx/conf.d/static.conf and restart Nginx container. 