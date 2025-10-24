# Stage 1: Build the application with Maven
FROM maven:3.9.8-eclipse-temurin-21 AS build
WORKDIR /app

# Copy only necessary files for dependency resolution first
COPY pom.xml .
RUN mvn dependency:go-offline

# Copy the rest of the project
COPY src ./src

# Build the application
RUN mvn clean package -DskipTests

# Stage 2: Create the final image
FROM eclipse-temurin:21-jdk-alpine

# Expose application ports
EXPOSE 8082

# Copy the JAR from the build stage
COPY --from=build /app/target/*.jar app.jar

# Set the entrypoint
ENTRYPOINT ["java", "-jar", "/app.jar"]
