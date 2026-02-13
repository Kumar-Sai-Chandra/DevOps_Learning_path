# Use the official Nginx image as the base image
FROM nginx
# Label the image with metadata about the project, author, and description
LABEL "Project"="vprofile-project" "Author"="Kumar" "Description"="Nginx web server for vprofile-project"
# Remove the default Nginx web content  
RUN rm -rf /etc/nginx/conf.d/default.conf
# Copy the custom Nginx configuration file to the container
COPY nginvproapp.conf /etc/nginx/conf.d/vproapp.conf
