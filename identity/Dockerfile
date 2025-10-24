# Stage 1: Build the custom dependency (common-events)
FROM maven:3.9.8-eclipse-temurin-21 AS common-events-builder

WORKDIR /common-events

COPY common-events /common-events

RUN mvn clean package -DskipTests -B

# Stage 2: Build the main application
FROM maven:3.9.8-eclipse-temurin-21 AS app-builder

WORKDIR /app

# Copy the common-events module from the previous stage
COPY --from=common-events-builder /common-events/target/common-events-0.0.1.jar /app/

RUN mvn install:install-file -Dfile=/app/common-events-0.0.1.jar -DgroupId=com.koclan.common -DartifactId=common-events -Dversion=0.0.1 -Dpackaging=jar

COPY agrictrades-identity/pom.xml .
RUN mvn dependency:go-offline -B

COPY agrictrades-identity/src ./src
RUN mvn clean package -DskipTests -B

# Stage 3: Create the final runtime image
FROM eclipse-temurin:21-jre-alpine

WORKDIR /app

ENV SPRING_PROFILES_ACTIVE=dev
EXPOSE 8083

# Copy the built application JAR
COPY --from=app-builder /app/target/*.jar app.jar

# Health check
HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
  CMD wget --quiet --tries=1 --spider http://localhost:8083/actuator/health || exit 1

ENTRYPOINT ["java", "-Xms256m", "-Xmx512m", "-jar", "/app/app.jar"]
