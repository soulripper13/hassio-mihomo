FROM alpine:latest

# Install dependencies including curl for downloading
RUN apk add --no-cache ca-certificates bash curl

WORKDIR /app

# Map BUILD_ARCH to Mihomo's binary architecture naming
ARG BUILD_ARCH
RUN case "${BUILD_ARCH}" in \
      amd64) ARCH=amd64 ;; \
      aarch64) ARCH=arm64 ;; \
      armv7) ARCH=armv7 ;; \
      armhf) ARCH=armv7 ;; \
      i386) ARCH=386 ;; \
      *) echo "Unsupported architecture: ${BUILD_ARCH}" && exit 1 ;; \
    esac

# Download, extract, and install the latest Mihomo binary
RUN LATEST_TAG=$(curl -s https://api.github.com/repos/MetaCubeX/mihomo/releases/latest | grep '"tag_name":' | sed -E 's/.*"([^"]+)".*/\1/') && \
    VERSION=${LATEST_TAG#v} && \
    curl -L -o /tmp/mihomo.gz "https://github.com/MetaCubeX/mihomo/releases/download/${LATEST_TAG}/mihomo-linux-${ARCH}-v${VERSION}.gz" && \
    gunzip /tmp/mihomo.gz && \
    mv /tmp/mihomo /app/mihomo && \
    chmod +x /app/mihomo && \
    rm -f /tmp/mihomo.gz

# Copy startup script and default config
COPY start-mihomo.sh /app/start-mihomo.sh
COPY mihomo-default.yaml /app/mihomo-default.yaml

# Make startup script executable
RUN chmod +x /app/start-mihomo.sh

# Expose ports (as per original)
EXPOSE 7890 7891 7893 9898 53 9090

# Run the startup script
CMD ["/app/start-mihomo.sh"]
