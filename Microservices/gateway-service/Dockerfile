# Use a Node.js base image
FROM node:18-alpine

# Set the working directory
WORKDIR /app

# Copy the dependency manifest
COPY package*.json ./

# Install dependencies
RUN npm install --omit=dev

# Copy the application code
COPY app.js ./

# Expose the Order Service port
EXPOSE 3002

# Start the application
CMD ["node", "app.js"]
