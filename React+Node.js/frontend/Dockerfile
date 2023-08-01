# Use the official Node.js image as the base image for React app
FROM node:14-alpine

# Set the working directory for the app
WORKDIR /app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install Node.js dependencies
RUN npm install

# Copy the rest of the app's source code to the container
COPY . .

# Build the React app for production
RUN npm run build

# Install Node.js production dependencies only (optional, based on your needs)
# RUN npm install --production

# Start the Node.js app (assuming your server code is in index.js)
CMD ["node", "index.js"]
