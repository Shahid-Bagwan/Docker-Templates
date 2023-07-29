# Dockerizing Next.js and PostgreSQL Applications

This repository contains Dockerfiles and a Docker Compose file to help you containerize and deploy your Next.js application and PostgreSQL database. The Dockerfiles are used to build Docker images for each application, and the Docker Compose file is used to manage the containers and their interactions.

## Prerequisites

Make sure you have Docker and Docker Compose installed on your system before proceeding.

- [Docker Installation Guide](https://docs.docker.com/get-docker/)
- [Docker Compose Installation Guide](https://docs.docker.com/compose/install/)

## Directory Structure

The project directory structure is as follows:

```
/
|-- nextjs-app/
|   |-- (Next.js application source files)
|   |-- Dockerfile.nextjs
|
|-- docker-compose.yml
|-- README.md (this file)
```

## Next.js Application - Dockerfile.nextjs

The `Dockerfile.nextjs` is used to build an image for the Next.js application. It uses the official Node.js image as the base image and then sets the working directory, installs Node.js dependencies, and copies the Next.js app's source code. It then builds the Next.js app for production and sets an environment variable to indicate that the app is running in a production environment. Finally, the container starts the Next.js server using the `npm start` command.

```Dockerfile
# Use the official Node.js image as the base image
FROM node:14-alpine

# Set the working directory
WORKDIR ./app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the app's source code to the container
COPY . .

# Build the Next.js app for production
RUN npm run build

# Set the environment variable to production
ENV NODE_ENV=production

# Start the Next.js server
CMD ["npm", "start"]
```

## PostgreSQL Database - Dockerfile.postgres

The `Dockerfile.postgres` is used to build an image for the PostgreSQL database. It uses the official PostgreSQL image from Docker Hub. You can customize this file to include any additional database initialization scripts, such as creating additional databases or roles on startup.

```Dockerfile
# Use the official PostgreSQL image as the base image
FROM postgres:latest

# Set environment variables
ENV POSTGRES_USER=myuser
ENV POSTGRES_PASSWORD=mypassword
ENV POSTGRES_DB=mydatabase

# Expose the PostgreSQL default port (5432)
EXPOSE 5432

# (Optional) Copy custom initialization scripts to the container
# For example, to create additional databases or roles on startup
# COPY init-script.sql /docker-entrypoint-initdb.d/

# Start the PostgreSQL server when the container is launched
CMD ["postgres"]
```

## Docker Compose - docker-compose.yml

The `docker-compose.yml` file defines two services: `nextjs-app` and `postgres-db`. Each service corresponds to one of the Docker templates.

- `nextjs-app` service:
  - We specify the build context and the Dockerfile to build the Next.js application image.
  - The service will be accessible from the host at port 3000, as we are mapping port 3000 of the container to port 3000 of the host.
  - We use a volume to map the host's `nextjs-app` directory to the container's `./app` directory, allowing hot-reloading of changes during development.

- `postgres-db` service:
  - We use the official PostgreSQL image from Docker Hub.
  - We set environment variables `POSTGRES_USER`, `POSTGRES_PASSWORD`, and `POSTGRES_DB` to configure the PostgreSQL container.
  - The PostgreSQL container will be accessible from the host at port 5432, as we are mapping port 5432 of the container to port 5432 of the host.

```yaml
version: '3.8'

services:
  nextjs-app:
    build:
      context: ./nextjs-app
      dockerfile: Dockerfile.nextjs
    ports:
      - 3000:3000
    volumes:
      - ./nextjs-app:./app

  postgres-db:
    build:
      context: ./postgres-db
      dockerfile: Dockerfile.postgres
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydatabase
    ports:
      - 5432:5432
```

## Usage

1. Clone this repository to your local machine.

2. Place your Next.js application source files inside the `nextjs-app` directory.

3. Modify the `Dockerfile.nextjs` and `Dockerfile.postgres` if necessary to meet your application's specific requirements.

4. Remove the name of the language from dockerfile. just keep : Dockerfile.

5. Run the following command to build and start the containers:

   ```bash
   docker-compose up -d
   ```

   This will build the Next.js application image and the PostgreSQL image and run both containers in the background.

6. Access your Next.js application in the browser at `http://localhost:3000`.

7. Your PostgreSQL database is accessible on the default PostgreSQL port (5432). To interact with the database, you can use a PostgreSQL client or connect it to your application using the configured environment variables.

## Customization

Feel free to modify the Dockerfiles and Docker Compose file to suit your application's needs. You can also add additional services to the `docker-compose.yml` file if your application requires other components.

If you have any questions or need further assistance, please create an issue in this repository, and we will be happy to help!

Happy Dockerizing and Deploying! 🐳