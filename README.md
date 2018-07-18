# JWDMC PHP 5.6 and Mysql 5.5 Docker Environment

## Starting and Stopping Docker Containers
Start with `docker-compose up -d`

Stop with `docker-compose stop`

## Overview
This is a simple Docker enviroment intended for local development with Apache, PHP 5.6 and Mysql 5.5. 

This pulls in an [extended version](https://hub.docker.com/r/omurphy/jwdmc/) of the official Docker php:5.6-apache image with much needed PHP extensions, such as mcrypt, opcache, pdo, pdo_mysql and mysqli. The PHP Apache container is mapped to port 8080 and will be accessible from localhost:8080. Application code should be placed in the application/ directory.

It also pulls in the official mysql:5.5 docker image. The Mysql container's port is mapped to localhost:3306, so that you can connect to it with a GUI like Sequel Pro. A mysql database and user are also created based on variables in the docker-compose.yml file. The mysql root password is also set based on the docker-compose.yml file.   

## Important Notes

### Use db instead of localhost
To connect to mysql from your application code, you will need to connect to it with db as the host. For instance, in Wordpress, you would put the following in your WP Config:
```
define('DB_HOST', 'db');
```

### Giving the newly created mysql user all privileges
You will need to change the permissions of the mysql user that we created in our docker compose file. Follow the below steps to do so:

1. `docker exec -it ##ContainerID## mysql -p -u root`
2. enter password that was set in the docker-compose.yml file
3. `GRANT ALL PRIVILEGES ON *.* TO 'homestead'@'%';`
4. Note that `*.*` grants permissions to all databases to the homestead user. To only grant permission to the homestead database, instead use the following: `homestead.*`
5. `FLUSH PRIVILEGES;` 

### Importing a database
```
docker exec -i ##ContainerID## mysql -uhomestead -psecret homestead < db-to-import.sql
```

### Exporting a database
```
docker exec -i ##ContainerID## mysqldump -psecret -uhomestead homestead > db-exported.sql
```

#### Note on importing and exporting
**It is insecure to expose the mysql user password on the command line**, but it's not an issue here since this is only meant for local development. See more info about connecting to mysql without using a password on the command line from [here](https://stackoverflow.com/questions/9293042/how-to-perform-a-mysqldump-without-a-password-prompt) which is important for production.

