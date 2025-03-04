#
# Build Stage
#
FROM debian:10 AS build

# Update package lists
RUN apt-get update 

# Install required dependencies first
RUN apt-get install -y --no-install-recommends ca-certificates-java
RUN update-ca-certificates -f

# Install Java (OpenJDK 11) and Maven
RUN apt-get install -y openjdk-11-jre-headless maven

# Clean up unnecessary files
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Set working directory for the app
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

# Expose port
EXPOSE 8080

# Set working directory
WORKDIR /opt/shipping

# Environment variables
ENV CART_ENDPOINT=cart:8080
ENV DB_HOST=mysql

# Copy built JAR file from build stage
COPY --from=build /opt/shipping/target/shipping-1.0.jar shipping.jar

# Run the application
CMD [ "java", "-Xmn256m", "-Xmx768m", "-jar", "shipping.jar" ]
