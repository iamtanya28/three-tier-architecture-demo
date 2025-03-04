#
# Build Stage
#
FROM debian:10 AS build

# Fix certificate issues first
RUN apt-get update && apt-get install -y --no-install-recommends ca-certificates-java
RUN update-ca-certificates -f

# Install Java and Maven
RUN apt-get install -y openjdk-11-jre-headless maven

# Clean up unnecessary files
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Set working directory for the application
WORKDIR /opt/shipping

# Copy Maven config file and resolve dependencies
COPY pom.xml /opt/shipping/
RUN mvn dependency:resolve

# Copy the source code and build the project
COPY src /opt/shipping/src/
RUN mvn package

#
# Run Stage
#
FROM openjdk:8-jdk

# Expose the application port
EXPOSE 8080

# Set the working directory inside the container
WORKDIR /opt/shipping

# Set environment variables
ENV CART_ENDPOINT=cart:8080
ENV DB_HOST=mysql

# Copy the built JAR file from the build stage
COPY --from=build /opt/shipping/target/shipping-1.0.jar shipping.jar

# Command to run the application
CMD [ "java", "-Xmn256m", "-Xmx768m", "-jar", "shipping.jar" ]
