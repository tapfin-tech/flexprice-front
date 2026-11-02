# syntax=docker/dockerfile:1
# Production image for UAT: static build served by non-root nginx.
# VITE_* settings are read from .env at build time, so build once per environment.
FROM node:22-alpine AS build
WORKDIR /app
# Dependencies only reinstall when the lockfile changes; the npm cache keeps that fast too.
COPY package.json package-lock.json ./
RUN --mount=type=cache,target=/root/.npm \
    npm ci --no-audit --no-fund
COPY . .
# Keeps tsc's incremental state between builds so only changed files are re-checked.
RUN --mount=type=cache,target=/app/node_modules/.tmp \
    npm run build

FROM nginxinc/nginx-unprivileged:1.27-alpine
COPY nginx.conf /etc/nginx/conf.d/default.conf
# Serve on 3021 so the host and container ports match.
RUN sed -i 's/listen 3000;/listen 3021;/' /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 3021
