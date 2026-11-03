
FROM eclipse-temurin:21-jdk AS builder

WORKDIR /usr/src/app/

# Copy Gradle wrapper and configuration
COPY ./src/ad/gradlew* ./
COPY ./src/ad/settings.gradle* ./
COPY ./src/ad/build.gradle* ./
COPY ./src/ad/gradle ./gradle

# Give execute permission
RUN chmod +x ./gradlew

# Download Gradle dependencies
RUN ./gradlew
RUN ./gradlew downloadRepos

# Copy Ad application source code
COPY ./src/ad/ ./

# Copy protobuf files
COPY ./pb ./proto

# Build the application
RUN ./gradlew installDist -PprotoSourceDir=./proto

#####################################################

FROM eclipse-temurin:21-jre

WORKDIR /usr/src/app/

COPY --from=builder /usr/src/app/ ./

ENV AD_PORT=9099

ENTRYPOINT ["./build/install/opentelemetry-demo-ad/bin/Ad"]

