FROM golang:1.25-alpine AS builder

WORKDIR /src

RUN apk add --no-cache git

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o /fabric ./cmd/fabric

FROM alpine:latest

RUN apk add --no-cache ca-certificates yt-dlp \
    && mkdir -p /home/appuser/.config/fabric

COPY --from=builder /fabric /usr/local/bin/fabric

EXPOSE 8080

RUN adduser -D appuser
USER appuser

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
            CMD wget --quiet --tries=1 --spider http://localhost:8080/ || exit 1

ENTRYPOINT ["fabric"]

CMD ["--serve"]
