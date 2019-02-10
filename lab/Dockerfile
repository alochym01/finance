# Base image is Ubuntu
FROM node:latest

# Author: Dr. Peter
MAINTAINER Dr. Peter <peterindia@gmail.com>

# Install apache2 package
RUN npm install redis

# Copy the source code to the Docker image
ADD example.js /myapp/example.js
