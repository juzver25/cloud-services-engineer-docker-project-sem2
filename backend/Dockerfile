# syntax=docker/dockerfile:1

FROM golang:1.17-alpine AS builder

WORKDIR /build

RUN apk add --no-cache ca-certificates git \
    && update-ca-certificates

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -trimpath -ldflags="-s -w" -o /app/api ./cmd/api

FROM alpine:3.18

RUN apk add --no-cache ca-certificates wget \
    && update-ca-certificates \
    && addgroup -S -g 10001 appgroup \
    && adduser -S -u 10001 -G appgroup appuser

WORKDIR /app

COPY --from=builder --chown=appuser:appgroup /app/api ./api

USER appuser

EXPOSE 8081

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD wget -q -O- http://127.0.0.1:8081/health || exit 1

ENTRYPOINT ["./api"]
