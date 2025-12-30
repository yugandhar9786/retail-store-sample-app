# =========================
# 1️⃣ Build Stage
# =========================
FROM eclipse-temurin:21-jdk AS build

WORKDIR /build

COPY .mvn .mvn
COPY mvnw .
COPY pom.xml .

RUN ./mvnw dependency:go-offline -B

COPY src src

RUN ./mvnw package -DskipTests \
    && cp target/carts-0.0.1-SNAPSHOT.jar app.jar


# =========================
# 2️⃣ Runtime Stage
# =========================
FROM eclipse-temurin:21-jre

# Create non-root user (no fixed UID)
RUN useradd -m appuser

WORKDIR /app

COPY --from=build /build/app.jar app.jar

USER appuser

ENV SPRING_PROFILES_ACTIVE=prod
ENV JAVA_OPTS="-XX:+UseContainerSupport -XX:MaxRAMPercentage=75.0"

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app/app.jar"]
