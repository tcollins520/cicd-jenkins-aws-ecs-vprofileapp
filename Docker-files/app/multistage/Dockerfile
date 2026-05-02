# multistage dockerfile to build and run tomcat app

# ---------- Stage 1: Build Artifact ----------
FROM maven:3.9.9-eclipse-temurin-21-jammy AS build_image

WORKDIR /app

COPY . .

RUN mvn clean package -DskipTests


# ---------- Stage 2: Run app ----------
FROM tomcat:9-jdk21

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=build_image /target/*.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080

CMD ["catalina.sh", "run"]
