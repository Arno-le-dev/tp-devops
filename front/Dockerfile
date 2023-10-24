# Use the official Nginx image as the base image
FROM httpd:2.4.58

# Copy your web content (HTML, CSS, JavaScript, etc.) into the container
COPY ./public-html/ /usr/local/apache2/htdocs/
COPY apache-config.conf /usr/local/apache2/conf/httpd.conf