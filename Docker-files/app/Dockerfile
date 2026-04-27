# multi-stage Build and Host Tomcat image

#1 build the artifact
# Use Base Image Maven build image that will be used to build our own customized image
FROM maven:3.9.9-eclipse-temurin-21-jammy AS BUILD_IMAGE
#1 fetch source code from github that will build with Maven
RUN git clone https://github.com/hkhcoder/vprofile-project.git
# ADD ../../ vprofile-project
#1a switch to the branch containers, this is where the source code is and run mvn install to generate and build the Artifact
RUN cd vprofile-project && git checkout containers && mvn install

#2 run/host the artifact
# take tomcat image from DockerHub and copies our image into tomcat image
# we are using 2 images to reduce the size of our custom image
FROM tomcat:10-jdk21
LABEL "Project"="Vprofile"
LABEL "Author"="Tina"

#2a remove the default application image that comes with the Tomcat Image
RUN rm -rf /usr/local/tomcat/webapps/*

#2b copy Artifact that was generated in step 1 to the Tomcat image so that image can be displayed on our website
# copy the previous built artifact from above(BUILD_IMAGE) to tomcat home directory with the name ROOT.war
# when the image is complete, we will have a Docker Image, with Tomcat service that will host our application.
COPY --from=BUILD_IMAGE vprofile-project/target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war

# expose the port you would like your container to listen on
EXPOSE 8080

# run command inside of the container
CMD ["catalina.sh", "run"]
