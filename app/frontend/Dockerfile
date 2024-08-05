# Frontend Dockerfile
FROM node:16.14-alpine

WORKDIR /app-frontend

# Copy package.json and package-lock.json to the working directory
COPY package*.json /app-frontend/

# Install dependencies
RUN npm install

# Copy the rest of the application files to the working directory
COPY . /app-frontend/

# Build the application
RUN npm run build

# Expose the port the application will run on (if needed, adjust accordingly)
EXPOSE 3000

# Command to run the application
CMD ["npm", "start"]

