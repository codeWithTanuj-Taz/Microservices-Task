# Use an appropriate Node.js base image
FROM node:18-alpine

# Set the working directory inside the container
WORKDIR /app

# Copy the dependency manifest
COPY package*.json ./

# Install application dependencies
RUN npm install --omit=dev

# Copy the application source code
COPY app.js ./

# Expose the User Service port
EXPOSE 3000

# Start the application
CMD ["node", "app.js"]
