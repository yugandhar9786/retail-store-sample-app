# =========================
# 1️⃣ Build Stage
# =========================
FROM eclipse-temurin:21-jdk AS build

# Create working directory
WORKDIR /build

# Copy Maven wrapper and dependency files first (for caching)
COPY .mvn .mvn
COPY mvnw .
COPY pom.xml .

# Download dependencies (faster rebuilds)
RUN ./mvnw dependency:go-offline -B

# Copy source code
COPY src src

# Build the application (skip tests for image build)
RUN ./mvnw package -DskipTests \
    && cp target/*.jar app.jar


# =========================
# 2️⃣ Runtime Stage
# =========================
FROM eclipse-temurin:21-jre

# Create non-root user (UID auto-assigned)
RUN useradd -m appuser

# Set working directory
WORKDIR /app

# Copy the built JAR from build stage
COPY --from=build /build/app.jar app.jar

# Switch to non-root user
USER appuser

# Environment variables
ENV SPRING_PROFILES_ACTIVE=prod
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0"

# Application port
EXPOSE 8080

# Start the application (proper signal handling)
ENTRYPOINT ["java","-jar","/app/app.jar"]


