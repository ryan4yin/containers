FROM  python:3.12.3-slim-bookworm

WORKDIR /work

RUN ARCH=$(uname -m) \
    && case ${ARCH} in \
        x86_64) TARGETARCH=amd64 ;; \
        aarch64) TARGETARCH=arm64 ;; \
        *) echo "Unsupported architecture: ${ARCH}"; exit 1 ;; \
    esac \
    && apt-get update && apt-get install -y neovim \
        netcat-openbsd socat \
        curl wget \
        bind9-host bind9-dnsutils \
        mtr telnet traceroute tcpdump net-tools stress \
        postgresql-client \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENTRYPOINT ["sleep"]
CMD ["1800"]
