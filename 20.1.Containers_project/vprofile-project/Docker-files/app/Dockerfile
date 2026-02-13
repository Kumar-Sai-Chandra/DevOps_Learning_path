# this is a multi-stage Dockerfile that first builds a Java web application using Maven and then deploys it to a Tomcat server.

# it builds a container with Maven and JDK 21 as the base image with the tag BUILD_IMAGE
FROM maven:3.9.9-eclipse-temurin-21-jammy as BUILD_IMAGE   
# Clone the vprofile-project repository 
RUN git clone https://github.com/hkhcoder/vprofile-project.git 
# Checkout the 'containers' branch and build the project, skipping tests
RUN cd vprofile-project && git checkout containers && mvn install -DskipTests  


# Use Tomcat with JDK 21 as the base image for the final container
FROM tomcat:10-jdk21   
# Remove default web applications from Tomcat            
RUN rm -rf /usr/local/tomcat/webapps/*  
# Copy the built WAR file from the BUILD_IMAGE stage to Tomcat's webapps directory
COPY --from=BUILD_IMAGE /vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war    


# Expose port 8080 for the Tomcat server
EXPOSE 8080
# Start the Tomcat server when the container launches
CMD ["catalina.sh", "run"]  


