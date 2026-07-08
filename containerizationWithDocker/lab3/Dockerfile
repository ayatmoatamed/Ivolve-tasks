FROM maven:3.9.16-eclipse-temurin-17-alpine
WORKDIR  /app
RUN adduser  -D appuser 
COPY pom.xml
RUN mvn dependency:go-offline 
COPY src ./src
RUN mvn package 
USER appuser 
CMD ["java",  "-jar", "target/demo-0.0.1-SNAPSHOT.jar" ]
EXPOSE 8080
