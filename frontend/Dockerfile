# Use Node.js LTS image as the base
FROM node:lts-alpine as build

# Set working directory inside the container
WORKDIR /app

# Copy package.json and yarn.lock (or package-lock.json if using npm)
COPY package.json yarn.lock ./

# Install dependencies
RUN yarn install --frozen-lockfile

# Copy the entire project
COPY . .

# Build the React application with Vite
RUN yarn build

# Stage 2 - Serve the built application with a lightweight HTTP Server
FROM nginx:alpine

# Copy the built artifacts from the build stage
COPY --from=build /app/dist /usr/share/nginx/html

# Copy nginx configuration file if needed (optional)
# COPY nginx/nginx.conf /etc/nginx/nginx.conf

# Expose port 80
EXPOSE 80

# Start nginx server
CMD ["nginx", "-g", "daemon off;"]
