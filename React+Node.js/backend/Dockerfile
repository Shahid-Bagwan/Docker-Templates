# Use the official Node.js image as the base image for Node.js app
FROM node:14-alpine

# Set the working directory for the Node.js app
WORKDIR /app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install Node.js dependencies
RUN npm install

# Copy the rest of the app's source code to the container
COPY . .

# Start the Node.js app
CMD ["node", "index.js"]
