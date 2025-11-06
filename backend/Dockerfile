# Use a Node.js base image
FROM node:18-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy dependency files first
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose port 80 (where the app will run)
EXPOSE 80

# Command to start the application
CMD ["node", "app.js"]
