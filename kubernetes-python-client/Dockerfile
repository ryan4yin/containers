FROM  python:3.12.3-slim-bookworm

WORKDIR /work

# install kubectl
RUN apt-get update && apt-get install -y curl \
    && curl -LO "https://dl.k8s.io/release/v1.29.2/bin/linux/amd64/kubectl" \
    && chmod +x kubectl \
    && mv kubectl /usr/local/bin/ \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

ENTRYPOINT ["bin/bash"]
