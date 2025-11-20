#### Stage 1: Build the application ####
FROM eclipse-temurin:11-jdk as build

# Set the current working directory inside the image
WORKDIR /app

# Copy Maven wrapper and .mvn folder
COPY mvnw .
COPY .mvn .mvn

# Copy the pom.xml
COPY pom.xml .

# Download all dependencies offline (cached unless pom.xml changes)
RUN ./mvnw dependency:go-offline -B

# Copy the source code
COPY src src

# Package the application
RUN ./mvnw package -DskipTests
RUN mkdir -p target/dependency && (cd target/dependency; jar -xf ../*.jar)

#### Stage 2: Minimal runtime image ####
FROM eclipse-temurin:11-jre

ARG DEPENDENCY=/app/target/dependency

# Copy dependencies and compiled classes from build stage
COPY --from=build ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=build ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=build ${DEPENDENCY}/BOOT-INF/classes /app

# Run the application
ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.polls.PollsApplication"]
