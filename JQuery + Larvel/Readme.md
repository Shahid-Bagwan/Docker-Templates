# Dockerizing jQuery and Laravel Applications

This repository contains Dockerfiles and a Docker Compose file to help you containerize and deploy your jQuery and Laravel applications. The Dockerfiles are used to build Docker images for each application, and the Docker Compose file is used to manage the containers and their interactions.

## Prerequisites

Make sure you have Docker and Docker Compose installed on your system before proceeding.

- [Docker Installation Guide](https://docs.docker.com/get-docker/)
- [Docker Compose Installation Guide](https://docs.docker.com/compose/install/)

## Directory Structure

The project directory structure is as follows:

```
/
|-- frontend/
|   |-- jquery-files/ (jQuery application source files)
|   |-- Dockerfile.jquery
|
|-- backend/
|   |-- laravel-files/ (Laravel application source files)
|   |-- Dockerfile.laravel
|
|-- docker-compose.yml
|-- README.md (this file)
```

## jQuery Application - Dockerfile.jquery

The `Dockerfile.jquery` is used to build an image for the jQuery application. It uses the official Nginx image as the base image and then copies the jQuery files to Nginx's default directory. The container runs an Nginx server to serve the jQuery application.

```Dockerfile
# Use the official Nginx image as the base image for jQuery app
FROM nginx:alpine

# Copy the jQuery files to Nginx's default directory
COPY ./jquery-files/ ./nginx/html

# Expose the port on which Nginx will run (80 by default)
EXPOSE 80

# Start Nginx server when the container is launched
CMD ["nginx", "-g", "daemon off;"]
```

## Laravel Application - Dockerfile.laravel

The `Dockerfile.laravel` is used to build an image for the Laravel application. It uses the official PHP image as the base image, installs necessary PHP extensions and dependencies, copies the Laravel files to the container, installs Composer, and runs Laravel's dependencies installation.

```Dockerfile
# Use the official PHP image as the base image for Laravel app
FROM php:7.4-fpm

# Set the working directory for the Laravel app
WORKDIR ./var/www

# Install PHP extensions and other dependencies
RUN apt-get update && \
    apt-get install -y libpng-dev libjpeg-dev libfreetype6-dev zip unzip && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd pdo pdo_mysql

# Copy the Laravel files to the container
COPY ./laravel-files/ .

# Install Composer and run Laravel dependencies installation
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    composer install

# Expose the port on which PHP-FPM will run (9000 by default)
EXPOSE 9000

# Start PHP-FPM when the container is launched
CMD ["php-fpm"]
```

## Docker Compose - docker-compose.yml

The `docker-compose.yml` file defines two services, one for the frontend (jQuery) and another for the backend (Laravel). It specifies how to build the images for each service and how to configure their interactions.

```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.jquery
    ports:
      - 8080:80
    volumes:
      - ./frontend/jquery-files:./nginx/html

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.laravel
    ports:
      - 9000:9000
    volumes:
      - ./backend/laravel-files:./var/www
```

## Usage

`` Make changes according to your project. otherwise error may occur ``

1. Clone this repository to your local machine.

2. Place your jQuery application source files inside the `frontend/jquery-files` directory.

3. Place your Laravel application source files inside the `backend/laravel-files` directory.

4. Remove the name of the language from dockerfile. just keep : Dockerfile.

5. Modify the `Dockerfile.jquery` and `Dockerfile.laravel` (there is no file named .(language name) its just for refrence) if necessary to meet your application's specific requirements.

6. Run the following command to build and start the containers:

   ```
   docker-compose up -d
   ```

7. Access your jQuery application in the browser at `http://localhost:8080`.

8. Access your Laravel application in the browser at `http://localhost:9000`.

## Customization

Feel free to modify the Dockerfiles and Docker Compose file to suit your application's needs. You can also add additional services to the `docker-compose.yml` file if your application requires other components.

If you have any questions or need further assistance, please create an issue in this repository, and we will be happy to help!

Happy Dockerizing and Deploying! 🐳