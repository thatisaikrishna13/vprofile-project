# Stage 1: Build the application
FROM openjdk:8 AS BUILD_IMAGE
RUN apt update && apt install maven -y

# Clone the repository, checkout the 'docker' branch, and build the application
RUN git clone -b vp-docker https://github.com/IfeanyiSam/vprofile-project.git
RUN cd vprofile-project && mvn install

# Stage 2: Create the final image with Tomcat
FROM tomcat:8-jre11

# Remove the default Tomcat webapps
RUN rm -rf /usr/local/tomcat/webapps/*

# Copy the built WAR file from the BUILD_IMAGE stage to Tomcat's webapps directory
COPY --from=BUILD_IMAGE vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

# Expose port 8080
EXPOSE 8080

# Run Tomcat
CMD ["catalina.sh", "run"]
