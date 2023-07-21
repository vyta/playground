# Use an official Alpine runtime as a parent image
FROM alpine:3.14

# Set the working directory in the container to /workspace
WORKDIR /workspace

# Install git, curl, vim
RUN apk add --no-cache git curl vim build-base bash

# Install Rust
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y

# Set the PATH
ENV PATH="/root/.cargo/bin:${PATH}"

# Install rust components
RUN rustup component add rustfmt
RUN rustup component add clippy

# Install Azure CLI
RUN apk add --no-cache py-pip
RUN pip install --upgrade pip
RUN apk add --update --no-cache bash gcc libffi-dev musl-dev openssl-dev make python3-dev
RUN pip install azure-cli

# Install Docker CLI
RUN apk add --no-cache docker-cli

# Install KinD
RUN curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
RUN chmod +x ./kind
RUN mv ./kind /usr/local/bin/kind

# Copy the current directory contents into the container at /workspace
COPY . /workspace
