# Use Node.js LTS image as the base
FROM node:lts-alpine as build

# Set working directory inside the container
WORKDIR /app

# Copy package.json (and if using npm, package-lock.json) from your repository
COPY package.json . 
# Adjust if using npm (COPY package-lock.json .)

# Install dependencies without the lock file
RUN yarn install  # Adjust if using npm (RUN npm install)

# Copy the entire project from your repository
COPY . .

# Build the React application with Vite
RUN yarn build

# Stage 2 - Serve the built application with a lightweight HTTP Server
FROM nginx:alpine

# Copy the built artifacts from the build stage
COPY --from=build /app/dist /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start nginx server
CMD ["nginx", "-g", "daemon off;"]
