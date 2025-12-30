# =========================
# 1️⃣ Build Stage
# =========================
FROM node:20-alpine AS build

WORKDIR /app

# Copy dependency files first (cache)
COPY package.json yarn.lock ./

# Install dependencies
RUN yarn install --frozen-lockfile

# Copy source and build
COPY . .
RUN yarn build


# =========================
# 2️⃣ Runtime Stage
# =========================
FROM node:20-alpine

WORKDIR /app

# Install only production dependencies
COPY package.json yarn.lock ./
RUN yarn install --frozen-lockfile --production

# Copy built files
COPY --from=build /app/dist ./dist

# Use non-root user provided by node image
USER node

EXPOSE 8080

ENTRYPOINT ["node", "dist/main.js"]
