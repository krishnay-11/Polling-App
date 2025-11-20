# Stage 1: Build the application
FROM openjdk:11-jdk-slim as build

WORKDIR /app

# Copy Maven wrapper and give execute permission
COPY mvnw .
RUN chmod +x mvnw

# Copy .mvn folder
COPY .mvn .mvn

# Copy pom.xml
COPY pom.xml .

# Download dependencies offline
RUN ./mvnw dependency:go-offline -B

# Copy source code
COPY src src

# Package the application
RUN ./mvnw package -DskipTests
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)

# Stage 2: minimal runtime image
FROM openjdk:11-jre-slim

ARG DEPENDENCY=/app/target/dependency

COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app

ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.polls.PollsApplication"]
