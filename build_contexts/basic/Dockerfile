FROM alpine:latest

# Install some basic tools for testing
RUN apk add --no-cache curl

# Create a simple data directory
RUN mkdir -p /data

# Default command
CMD ["sh", "-c", "echo 'Container started' && sleep 3600"]
