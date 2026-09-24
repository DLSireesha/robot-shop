FROM golang:1.22 AS builder

WORKDIR /app

# Copy go.mod and go.sum first (for caching)
COPY go.mod go.sum ./

# Copy source code
COPY . .

# Build the binary
RUN go build -o dispatch .

# Final stage
FROM debian:bullseye-slim
WORKDIR /app
COPY --from=builder /app/dispatch .
CMD ["./dispatch"]

