# Use the official Ubuntu 22.04 base image
FROM ubuntu:22.04
RUN apt update

# Install the Apache2 HTTP server package from the Ubuntu repository
RUN apt install -y apache2
RUN systemctl start apache2

# Copy the index.html file from the Docker build context to the default Apache document root directory in the container
COPY index.html /var/www/html/

# Expose port 80 to allow incoming HTTP traffic to the container
EXPOSE 80

# Specify the command to run when the container starts, which starts the Apache2 HTTP server in the foreground
CMD ["apache2ctl","-D","FOREGROUND"]