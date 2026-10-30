# Production image for UAT: static build served by non-root nginx.
# VITE_* settings are read from .env at build time, so build once per environment.
FROM node:22-alpine AS build
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginxinc/nginx-unprivileged:1.27-alpine
COPY nginx.conf /etc/nginx/conf.d/default.conf
# Serve on 3021 so the host and container ports match.
RUN sed -i 's/listen 3000;/listen 3021;/' /etc/nginx/conf.d/default.conf
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 3021
