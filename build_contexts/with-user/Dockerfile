FROM busybox:uclibc

# Create a non-root user
RUN addgroup -g 1001 testuser && \
    adduser -D -u 1001 -G testuser testuser

# Create a data directory and set ownership
RUN mkdir -p /data && \
    chown testuser:testuser /data

# Switch to non-root user
USER testuser

# Default command
CMD ["sh", "-c", "while true; do date; sleep 0.1; done"]
