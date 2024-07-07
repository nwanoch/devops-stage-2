# Stage 1: Build the application
FROM node:18.20.0 as build-stage

COPY . /app

WORKDIR /app

RUN npm install

RUN npm run build

# Stage 2: Serve app with Nginx server
FROM nginx:1.19.6

COPY --from=build-stage /app/dist /usr/share/nginx/html
# COPY frontend/nginx.conf /etc/nginx/nginx.conf