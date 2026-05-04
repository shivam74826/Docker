# ============================================================
# Multi-stage Dockerfile: Node.js Production Image
# Author: Shivam Kumar Pandey (shivam74826)
# ============================================================

# Stage 1: Builder — install all dependencies and build the app
FROM node:20-alpine AS builder

WORKDIR /app

# Copy dependency manifests first (enables Docker layer caching)
COPY package*.json ./

# Install all deps including devDependencies for build
RUN npm ci --include=dev

# Copy source code and build
COPY . .
RUN npm run build

# Remove devDependencies after build
RUN npm prune --production

# ============================================================
# Stage 2: Production — minimal runtime image
FROM node:20-alpine AS production

# Security: create a non-root user
RUN addgroup -g 1001 -S nodegroup && \
    adduser  -u 1001 -S nodeuser -G nodegroup

WORKDIR /app

# Copy only production-ready artifacts from builder
COPY --from=builder --chown=nodeuser:nodegroup /app/node_modules ./node_modules
COPY --from=builder --chown=nodeuser:nodegroup /app/dist        ./dist
COPY --from=builder --chown=nodeuser:nodegroup /app/package.json ./

# Switch to non-root user
USER nodeuser

EXPOSE 3000

HEALTHCHECK --interval=30s --timeout=5s --start-period=15s --retries=3 \
    CMD wget -qO- http://localhost:3000/health || exit 1

CMD ["node", "dist/server.js"]
