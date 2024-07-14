FROM node:16.14-alpine

WORKDIR /app-backend

# Copy package.json and package-lock.json to the working directory
COPY package*.json /app-backend/

# Install dependencies
RUN npm install

# Copy the rest of the application files to the working directory
COPY . /app-backend/

# Expose the port the application will run on
EXPOSE 3000

# Command to run the application
CMD ["npm", "start"]

