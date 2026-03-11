# ── Stage 1: Build ──────────────────────────────────────────────────────────
FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci --omit=dev

# ── Stage 2: Production ─────────────────────────────────────────────────────
FROM node:20-alpine AS production

# Actualizar paquetes del sistema para corregir vulnerabilidades
RUN apk update && apk upgrade

# Crear usuario no-root por seguridad
RUN addgroup -S coindash && adduser -S coindash -G coindash

WORKDIR /app

COPY --from=builder /app/node_modules ./node_modules

COPY src/ ./src/
COPY package.json ./

USER coindash

EXPOSE 3000

ENV NODE_ENV=production
ENV PORT=3000

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1

CMD ["node", "src/app.js"]