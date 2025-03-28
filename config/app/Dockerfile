# Use the official Go image for building the app
FROM golang:1.23-alpine AS builder

# Set the working directory inside the container
WORKDIR /app

# Copy the Go mod and sum files and download dependencies
COPY go.mod go.sum ./ 
RUN go mod download

# Copy the rest of the source code
COPY . .

# Build the Go app for production
# RUN CGO_ENABLED=0 GOOS=linux go build -o server cmd/app/main.go
RUN CGO_ENABLED=0 GOOS=linux go build -o server cmd/app/*

# Use a minimal base image for the runtime
FROM alpine:3.20.3

RUN apk --no-cache add ca-certificates

# Set the working directory in the runtime container
WORKDIR /root/

# Set environment variable for production
ENV GO_ENV=production

# Copy the compiled binary from the builder container
COPY --from=builder /app/server .
COPY --from=builder /app/scripts/wait-for.sh ./wait-for.sh


# Make sure the script is executable
RUN chmod +x wait-for.sh


# Expose the port your app runs on
EXPOSE 8080

# Run the binary
# Use the wait-for script to ensure the Postgres is ready before starting the server

CMD ["./wait-for.sh", "postgres:5432", "--", "./server"]
