# =========================
# 1️⃣ Build Stage
# =========================
FROM golang:1.23-alpine AS build

# Install build dependencies for CGO
RUN apk add --no-cache gcc musl-dev sqlite-dev

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

# Build with CGO enabled (REQUIRED for sqlite)
RUN CGO_ENABLED=1 GOOS=linux GOARCH=amd64 \
    go build -o catalog main.go


# =========================
# 2️⃣ Runtime Stage
# =========================
FROM alpine:3.20

# Install sqlite runtime dependency
RUN apk add --no-cache sqlite-libs ca-certificates

# Create non-root user
RUN adduser -D appuser

WORKDIR /app

COPY --from=build /app/catalog /app/catalog

USER appuser

ENV GIN_MODE=release

EXPOSE 8080

ENTRYPOINT ["/app/catalog"]
