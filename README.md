# Dockerized Full Stack Web Application Deployment

## Project Overview

This repository contains Docker configuration files and setup instructions for deploying a full stack web application consisting of a React frontend, FastAPI backend with PostgreSQL database, and proxy configuration using Nginx Proxy.

## Prerequisites

Make sure you have installed the following tools and dependencies:

- Docker
- Docker Compose
- Git

## Setup Instructions

### Local Development

1. **Clone the Repository:**
   git clone <forked-repository-url>
   cd <repository-directory>

2. **Build and Run the Services:**
   docker-compose up -d --build

3. **Access the Application:**

- Frontend: http://localhost/
- Backend API: http://localhost/api/
- Adminer (Database UI): http://localhost:8080/

### Deployment on DigitalOcean

To deploy the application on DigitalOcean and set up HTTPS with Let's Encrypt, follow these steps:

1. **Prepare Docker Compose File:**
   Modify `docker-compose.yml` as necessary for production environment settings (e.g., environment variables, volume mounts).

2. **Set Up a DigitalOcean Droplet:**

- Create a Droplet (virtual machine) on DigitalOcean with Docker installed.
- Configure SSH access using SSH keys.

3. **Deploy Application to Droplet:**

- SSH into your Droplet:
  ```
  ssh root@your-droplet-ip
  ```
- Clone your repository inside the Droplet:
  ```
  git clone <repository-url>
  cd <repository-directory>
  ```
- Build and run the Docker containers:
  ```
  docker-compose up -d --build
  ```

4. **Set Up Domain and HTTPS:**

- Obtain a domain name from a registrar like Namecheap or GoDaddy.
- Configure DNS records to point to your Droplet's IP address.
- Install Certbot on your Droplet:
  ```
  sudo apt update
  sudo apt install certbot
  ```
- Generate SSL certificate using Certbot:
  ```
  sudo certbot certonly --nginx -d your-domain.com -d www.your-domain.com
  ```
- Update Nginx configuration (`nginx.conf`) to use SSL certificates and handle HTTPS traffic.

## Nginx Configuration

Modify Nginx configuration (`nginx.conf`) to handle routing for frontend and backend services, ensuring:

- Frontend serves on root (http://domain/)
- Backend APIs proxy to `/api` (http://domain/api/)
- Adminer accessible via subdomain (http://db.domain/)

### Example `nginx.conf`

```nginx
server {
 listen 80;
 server_name your-domain.com www.your-domain.com;

 location / {
     root /usr/share/nginx/html;
     index index.html;
     try_files $uri $uri/ /index.html;
 }

 location /api/ {
     proxy_pass http://backend:8000/;
     proxy_set_header Host $host;
     proxy_set_header X-Real-IP $remote_addr;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_set_header X-Forwarded-Proto $scheme;
 }

 location /docs/ {
     proxy_pass http://backend:8000/docs/;
     proxy_set_header Host $host;
     proxy_set_header X-Real-IP $remote_addr;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_set_header X-Forwarded-Proto $scheme;
 }

 listen [::]:443 ssl ipv6only=on; # managed by Certbot
 listen 443 ssl; # managed by Certbot
 ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem; # managed by Certbot
 ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem; # managed by Certbot
 include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
 ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
 listen 8080;
 server_name adminer.your-domain.com;

 location / {
     proxy_pass http://adminer:8080/;
     proxy_set_header Host $host;
     proxy_set_header X-Real-IP $remote_addr;
     proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     proxy_set_header X-Forwarded-Proto $scheme;
 }
}
```
