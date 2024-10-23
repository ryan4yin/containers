FROM  python:3.12.3-slim-bookworm

WORKDIR /work

# install kubectl
RUN ARCH=$(uname -m) \
    && case ${ARCH} in \
        x86_64) TARGETARCH=amd64 ;; \
        aarch64) TARGETARCH=arm64 ;; \
        *) echo "Unsupported architecture: ${ARCH}"; exit 1 ;; \
    esac \
    && apt-get update && apt-get install -y curl \
    && curl -LO "https://dl.k8s.io/release/v1.31.1/bin/linux/${TARGETARCH}/kubectl" \
    && chmod +x kubectl \
    && mv kubectl /usr/local/bin/ \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

ENTRYPOINT ["bin/bash"]
