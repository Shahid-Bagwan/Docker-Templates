# Dockerizing Angular and Django Applications

This repository contains Dockerfiles and a Docker Compose file to help you containerize and deploy your Angular and Django applications. The Dockerfiles are used to build Docker images for each application, and the Docker Compose file is used to manage the containers and their interactions.

## Prerequisites

Make sure you have Docker and Docker Compose installed on your system before proceeding.

- [Docker Installation Guide](https://docs.docker.com/get-docker/)
- [Docker Compose Installation Guide](https://docs.docker.com/compose/install/)

## Directory Structure

The project directory structure is as follows:

```
/
|-- frontend/
|   |-- (Angular application source files)
|   |-- Dockerfile.angular
|
|-- backend/
|   |-- (Django application source files)
|   |-- Dockerfile.django
|
|-- docker-compose.yml
|-- README.md (this file)
```

## Angular Application - Dockerfile.angular

The `Dockerfile.angular` is used to build an image for the Angular application. It uses the official Node.js image as the base image and then copies the Angular app's source code, installs the Node.js dependencies, and builds the Angular app for production. Finally, it uses Nginx to serve the Angular build files.

```Dockerfile
# Use the official Node.js image as the base image for Angular app
FROM node:14-alpine as angular

# Set the working directory for the Angular app
WORKDIR ./app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install Node.js dependencies
RUN npm install

# Copy the rest of the app's source code to the container
COPY . .

# Build the Angular app for production, (Use the build command)
RUN npm run build --prod

# Stage 2: Use Nginx to serve the Angular build files
FROM nginx:alpine

# Copy the Angular build files from the previous stage to Nginx's default directory
COPY --from=angular ./app/dist ./nginx/html

# Expose the port on which Nginx will run (80 by default)
EXPOSE 80

# Start Nginx server when the container is launched
CMD ["nginx", "-g", "daemon off;"]
```

## Django Application - Dockerfile.django

The `Dockerfile.django` is used to build an image for the Django application. It uses the official Python image as the base image, copies the Django app's source code, installs Python dependencies from the `requirements.txt` file, and starts the Django app using the `runserver` command.

```Dockerfile
# Use the official Python image as the base image for Django app
FROM python:3.9-alpine

# Set the working directory for the Django app
WORKDIR ./app

# Copy the requirements.txt file to the container
COPY requirements.txt .

# Install Python dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the app's source code to the container
COPY . .

# Start the Django app
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

## Docker Compose - docker-compose.yml

The `docker-compose.yml` file defines two services, one for the frontend (Angular) and another for the backend (Django). It specifies how to build the images for each service and how to configure their interactions.

```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.angular
    ports:
      - 4200:80
    volumes:
      - ./frontend:./app

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.django
    ports:
      - 8000:8000
    volumes:
      - ./backend:./app
```

## Usage

1. Clone this repository to your local machine.

2. Place your Angular application source files inside the `frontend` directory.

3. Place your Django application source files inside the `backend` directory.

4. Remove the name of the language. just keep : Dockerfile

5. Modify the `Dockerfile.angular` and `Dockerfile.django` if necessary to meet your application's specific requirements.

6. Run the following command to build and start the containers:

   ```
   docker-compose up -d
   ```

7. Access your Angular application in the browser at `http://localhost:4200`.

8. Access your Django application in the browser at `http://localhost:8000`.

## Customization

Feel free to modify the Dockerfiles and Docker Compose file to suit your application's needs. You can also add additional services to the `docker-compose.yml` file if your application requires other components.

If you have any questions or need further assistance, please create an issue in this repository, and we will be happy to help!

Happy Dockerizing and Deploying! 🐳