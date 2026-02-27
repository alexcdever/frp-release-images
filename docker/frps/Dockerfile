FROM alpine:3.21

ARG FRP_VERSION
ARG TARGETARCH

RUN set -eux; \
    apk add --no-cache ca-certificates curl tar; \
    case "$TARGETARCH" in \
      amd64) FRP_ARCH='amd64' ;; \
      arm64) FRP_ARCH='arm64' ;; \
      *) echo "Unsupported TARGETARCH: $TARGETARCH"; exit 1 ;; \
    esac; \
    FRP_VERSION_STRIPPED="${FRP_VERSION#v}"; \
    FRP_PKG="frp_${FRP_VERSION_STRIPPED}_linux_${FRP_ARCH}.tar.gz"; \
    curl -fL "https://github.com/fatedier/frp/releases/download/${FRP_VERSION}/${FRP_PKG}" -o /tmp/frp.tgz; \
    tar -xzf /tmp/frp.tgz -C /tmp; \
    cp "/tmp/frp_${FRP_VERSION_STRIPPED}_linux_${FRP_ARCH}/frps" /usr/local/bin/frps; \
    chmod +x /usr/local/bin/frps; \
    mkdir -p /etc/frp; \
    rm -rf /tmp/frp.tgz "/tmp/frp_${FRP_VERSION_STRIPPED}_linux_${FRP_ARCH}"

EXPOSE 7000 7500

ENTRYPOINT ["/usr/local/bin/frps"]
CMD ["-c", "/etc/frp/frps.toml"]
