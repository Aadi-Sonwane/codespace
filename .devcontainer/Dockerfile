# Use Ubuntu as the base image
FROM mcr.microsoft.com/devcontainers/universal:2

# Install dependencies
RUN apt-get update && apt-get install -y \
    docker.io \
    curl \
    unzip \
    python3 \
    python3-pip \
    ansible \
    && rm -rf /var/lib/apt/lists/*

# Install Kind
RUN curl -Lo /usr/local/bin/kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64 \
    && chmod +x /usr/local/bin/kind

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" \
    && chmod +x kubectl \
    && mv kubectl /usr/local/bin/

# Install Helm
RUN curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Install AWS CLI
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip \
    && ./aws/install \
    && rm -rf awscliv2.zip aws

# Install Terraform
RUN curl -fsSL https://apt.releases.hashicorp.com/gpg | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
    | tee /etc/apt/sources.list.d/hashicorp.list \
    && apt-get update \
    && apt-get install -y terraform

# Create a Kind cluster (if not exists)
RUN kind create cluster --name devops-lab

# Install Prometheus & Grafana using Helm
RUN kubectl create namespace monitoring \
    && helm repo add prometheus-community https://prometheus-community.github.io/helm-charts \
    && helm repo add grafana https://grafana.github.io/helm-charts \
    && helm repo update \
    && helm install prometheus prometheus-community/kube-prometheus-stack --namespace monitoring \
    && helm install grafana grafana/grafana --namespace monitoring

# Set default workspace directory
WORKDIR /workspace

CMD ["/bin/bash"]
