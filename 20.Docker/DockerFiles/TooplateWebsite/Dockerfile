FROM ubuntu:latest                      # Use the latest Ubuntu image as the base                
LABEL "Project"="LilFasion"             # Label the project
ENV DEBIAN_FRONTEND=noninteractive      # Set environment variable to avoid interactive prompts during package installation
RUN apt update && apt install git -y    # Update package lists and install git
RUN apt install apache2 -y              # Install Apache2 web server  
WORKDIR /var/www/html/                  # Set working directory to Apaches web root
#COPY lilfasion.tar.gz /var/www/html/
ADD lilfasion.tar.gz /var/www/html/     # Extract the website files into the web root
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"] # Start Apache in the foreground, as we dont have any systemctl command we use this to start the service
VOLUME /var/log/apache2              # Define a volume for Apache logs
EXPOSE 80           # port mapping for web server
