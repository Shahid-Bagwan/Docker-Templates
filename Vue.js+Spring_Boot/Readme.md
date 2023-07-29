# Dockerizing Vue.js and Spring Boot Applications

This repository contains Dockerfiles and a Docker Compose file to help you containerize and deploy your Vue.js and Spring Boot applications. The Dockerfiles are used to build Docker images for each application, and the Docker Compose file is used to manage the containers and their interactions.

## Prerequisites

Make sure you have Docker and Docker Compose installed on your system before proceeding.

- [Docker Installation Guide](https://docs.docker.com/get-docker/)
- [Docker Compose Installation Guide](https://docs.docker.com/compose/install/)

## Directory Structure

The project directory structure is as follows:

```
/
|-- frontend/
|   |-- (Vue.js application source files)
|   |-- Dockerfile.vue
|
|-- backend/
|   |-- (Spring Boot application source files)
|   |-- Dockerfile.spring
|
|-- docker-compose.yml
|-- README.md (this file)
```

## Vue.js Application - Dockerfile.vue

The `Dockerfile.vue` is used to build an image for the Vue.js application. It uses the official Node.js image as the base image and then sets the working directory, installs Node.js dependencies, and copies the Vue.js app's source code. It then builds the Vue.js app for production and stages the built files into a new image with Nginx. The final image uses Nginx to serve the Vue.js build files.

```Dockerfile
# Use the official Node.js image as the base image for Vue.js app
FROM node:14-alpine as vue

# Set the working directory for the Vue.js app
WORKDIR /usr/src/app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install Node.js dependencies
RUN npm install

# Copy the rest of the app's source code to the container
COPY . .

# Build the Vue.js app for production
RUN npm run build

# Stage 2: Use Nginx to serve the Vue.js build files
FROM nginx:alpine

# Copy the Vue.js build files from the previous stage to Nginx's default directory
COPY --from=vue /usr/src/app/dist /usr/share/nginx/html

# Expose the port on which Nginx will run (80 by default)
EXPOSE 80

# Start Nginx server when the container is launched
CMD ["nginx", "-g", "daemon off;"]
```

## Spring Boot Application - Dockerfile.spring

The `Dockerfile.spring` is used to build an image for the Spring Boot application. It uses the official OpenJDK image as the base image and then sets the working directory, copies the compiled JAR file (assuming it's named `app.jar`) to the container, and exposes the port on which the Spring Boot application will run. The container then starts the Spring Boot app using the `java -jar` command.

```Dockerfile
# Use the official OpenJDK image as the base image for Spring Boot app
FROM openjdk:14-jdk-alpine

# Set the working directory for the Spring Boot app
WORKDIR /app

# Copy the compiled JAR file to the container
COPY target/app.jar app.jar

# Expose the port on which Spring Boot will run (8080 by default)
EXPOSE 8080

# Start the Spring Boot app
CMD ["java", "-jar", "app.jar"]
```

## Docker Compose - docker-compose.yml

The `docker-compose.yml` file defines two services, one for the frontend (Vue.js) and another for the backend (Spring Boot). It specifies how to build the images for each service and how to configure their interactions.

```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.vue
    ports:
      - 8081:80
    volumes:
      - ./frontend:/usr/src/app

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.spring
    ports:
      - 8080:8080
    volumes:
      - ./backend:/app
```

## Usage

1. Clone this repository to your local machine.

2. Place your Vue.js application source files inside the `frontend` directory.

3. Place your Spring Boot application's compiled JAR file inside the `backend` directory and name it `app.jar`.

4. Modify the `Dockerfile.vue` and `Dockerfile.spring` if necessary to meet your application's specific requirements.

5. Run the following command to build and start the containers:

   ```
   docker-compose up -d
   ```

6. Access your Vue.js application in the browser at `http://localhost:8081`.

7. Access your Spring Boot application at `http://localhost:8080`.

## Customization

Feel free to modify the Dockerfiles and Docker Compose file to suit your application's needs. You can also add additional services to the `docker-compose.yml` file if your application requires other components.

If you have any questions or need further assistance, please create an issue in this repository, and we will be happy to help!

Happy Dockerizing and Deploying! 🐳