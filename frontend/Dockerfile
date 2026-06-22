# syntax=docker/dockerfile:1

FROM node:16-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci --ignore-scripts

COPY . .

ARG VUE_APP_API_URL=http://localhost:8080/api
ENV VUE_APP_API_URL=${VUE_APP_API_URL}

RUN npm run build

FROM nginxinc/nginx-unprivileged:1.25-alpine

COPY --from=builder --chown=nginx:nginx /app/dist /usr/share/nginx/html
COPY --chown=nginx:nginx nginx.conf /etc/nginx/conf.d/default.conf

USER nginx

EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD wget -q -O- http://127.0.0.1:8080/momo-store/ || exit 1
