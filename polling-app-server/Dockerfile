#### Stage 1: Build the application ####
# Use official Maven image with JDK 17 for building (compatible with Java 11)
FROM maven:3.9.2-eclipse-temurin-17 AS build

# Set working directory
WORKDIR /app

# Copy Maven wrapper (if exists) and project files
COPY mvnw . 
COPY .mvn .mvn
COPY pom.xml .

# Pre-download dependencies (offline)
RUN ./mvnw dependency:go-offline -B || mvn dependency:go-offline -B

# Copy source code
COPY src src

# Package the application (skip tests)
RUN ./mvnw package -DskipTests || mvn package -DskipTests

# Prepare dependencies for runtime stage
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)


#### Stage 2: Runtime image ####
# Use official JRE image (minimal, reliable)
FROM eclipse-temurin:17-jre

ARG DEPENDENCY=/app/target/dependency

# Copy compiled classes and dependencies from build stage
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app

# Expose default Spring Boot port
EXPOSE 8080

# Run the Spring Boot application
ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.polls.PollsApplication"]
