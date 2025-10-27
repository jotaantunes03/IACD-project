# Use a Node.js base image
FROM node:18-alpine

# Set the working directory
WORKDIR /app

# Copy dependency files
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the code
COPY . .

# Expose port 3000 (where React will run)
EXPOSE 3000

# Command to start the application
CMD ["npm", "start"]
