FROM debian:bookworm-slim

# Set non interactive frontend for debian apt
ENV DEBIAN_FRONTEND=noninteractive

# Install required debian packages
COPY requirements_deb .
RUN apt-get update && \
    apt-get install -y $(sed -e '/^#/d' requirements_deb) && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    rm requirements_deb

# Install ansible
RUN python3 -m pip install ansible-core==2.16.4 --break-system-packages

# Create a user for running Ansible
RUN useradd -u 2000 -m ansible

# Copy ansible.cfg into the container
COPY --chown=ansible:ansible ansible.cfg /etc/ansible/

# Set the working directory
WORKDIR /ansible-project

# Copy the entrypoint script into the container
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# Switch to the non-root user
USER ansible
# Install ansible collections
COPY --chown=ansible:ansible ansible_collections.yml .
RUN ansible-galaxy collection install -r ansible_collections.yml
    
# Set the entrypoint
ENTRYPOINT ["/entrypoint.sh"]