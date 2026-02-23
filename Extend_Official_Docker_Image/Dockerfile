# Use nginx as the base image
FROM nginx:latest

# Identify the maintainer of the Dockerfile. Update the email address to your own.
LABEL version="0.0.1"
LABEL key="mailtojoyjoel"

# Update the work DIR
WORKDIR /usr/share/nginx/html

# Copy custom HTML file to nginx web server directory
COPY index.html index.html

