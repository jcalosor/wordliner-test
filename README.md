# Wordliner Test

This is the CRUD application generated using symfony containerized via docker, I chose to containerize this crud app 
since I've been using docker for local env development, and I don't have PHP installed in my local machine, 
this allows me to have a portable setup for application development.

### Pre-requisite
In order to execute this application the following are needed:
1. Docker 27.^
2. Docker-Compose 1.26^

_Disclaimer:_ 
This can still run without docker, but you will need to have the following:
1. PHP 8.2
2. MySQL 8
3. Nginx
4. Symfony CLI

## Installation
This assumes that you have docker installed in your machine, and you have already cloned the CRUD app.
1. cd /path/to/app
2. `docker-compose build`
3. `docker-compose up -d`
4. `docker-compose exec -u 1000 fpm bash`
5. copy this value to your .env file `DATABASE_URL="mysql://root:root@db/wordliner"`
5. `composer install`

## View the CRUD App
1. Open the browser and go to http://localhost:10302/product

## Additional Details
I have used the symfony cli in creating the CRUD app, the whole process is pretty much straight forward,
the app is already been setup so please do not need to execute these steps, this assumes that docker has already been setup.
1. Create the docker config for symfony `docker-compose.yml` and dependencies in the `docker` directory.
2. Create the database `php bin/console doctrine:database:create`
3. Then I exported it as `wordliner_db.sql` placed it in my docker/build directory, 
this is for the sake of easily having the app running so I can have the tables fully populated on the get go,
but this is not a practice for production purposes.
4. `php bin/console make:entity Product`
5. `php bin/console make:crud Product`


Finish, thank you for the opportunity.