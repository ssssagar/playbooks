FROM python:3.11-slim

# Set labels
LABEL maintainer="DevOps Team"
LABEL description="Ansible Docker image for configuring Linux servers via Jenkins"

# Install system dependencies
RUN apt-get update && \
    apt-get install -y \
    openssh-client \
    sshpass \
    git \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Install Ansible
RUN pip install --no-cache-dir \
    ansible \
    ansible-lint \
    jmespath

# Create necessary directories
RUN groupadd -g 992 ansible && \
    useradd -m -u 992 -g ansible -s /bin/bash ansible

RUN mkdir -p /ansible/playbooks /ansible/inventory /ansible/roles && \
    chown -R ansible:ansible /ansible

RUN mkdir -p /home/ansible/.ssh && \
    chown -R ansible:ansible /home/ansible/.ssh && \
    chmod 700 /home/ansible/.ssh

# Set working directory
WORKDIR /ansible

# Copy SSH config (optional - for custom SSH settings)
RUN mkdir -p /root/.ssh && \
    chmod 700 /root/.ssh

RUN mkdir -p /home/ansible/.ansible && \
    chown -R ansible:ansible /home/ansible/.ansible

# Set environment variables
ENV ANSIBLE_HOST_KEY_CHECKING=False
ENV ANSIBLE_RETRY_FILES_ENABLED=False
ENV ANSIBLE_SSH_PIPELINING=True
ENV HOME=/home/ansible

USER ansible

# Default command
CMD ["/bin/bash"]
