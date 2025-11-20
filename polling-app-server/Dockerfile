#### Stage 1: Build the application
FROM eclipse-temurin:8-jdk-alpine as build

# Set the current working directory inside the image
WORKDIR /app

# Copy maven wrapper
COPY mvnw .
COPY .mvn .mvn

# Copy pom.xml
COPY pom.xml .

# Download dependencies
RUN ./mvnw dependency:go-offline -B

# Copy source code
COPY src src

# Package the application
RUN ./mvnw package -DskipTests
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)

#### Stage 2: Minimal image to run the app
FROM eclipse-temurin:8-jre-alpine

ARG DEPENDENCY=/app/target/dependency

# Copy dependencies from build stage
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app

# Run the Spring Boot application
ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.polls.PollsApplication"]
