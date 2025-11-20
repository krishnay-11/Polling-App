#### Stage 1: Build the application
FROM eclipse-temurin:8-jdk-alpine AS build

# Set working directory inside container
WORKDIR /app

# Copy Maven wrapper and make it executable
COPY mvnw .
COPY .mvn .mvn
RUN chmod +x mvnw

# Copy pom.xml first (for dependency caching)
COPY pom.xml .

# Download all dependencies offline (cached unless pom.xml changes)
RUN ./mvnw dependency:go-offline -B

# Copy source code
COPY src src

# Build and package the application (skip tests for faster build)
RUN ./mvnw package -DskipTests

# Extract dependencies to target/dependency folder
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)

#### Stage 2: Minimal runtime image
FROM eclipse-temurin:8-jre-alpine

ARG DEPENDENCY=/app/target/dependency

# Copy the built dependencies and classes from the build stage
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app

# Run the Spring Boot application
ENTRYPOINT ["java", "-cp", "app:app/lib/*", "com.example.polls.PollsApplication"]
