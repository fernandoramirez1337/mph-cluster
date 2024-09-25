FROM ubuntu:22.04

# Install dependencies
RUN apt-get update && \
    apt-get install -y \
        sudo \
        nano \
        net-tools \
        nfs-kernel-server \
        mpich \
        openssh-server \
        g++ \
        mpich-doc && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# SSH and NFS setup
RUN mkdir /var/run/sshd && \
    ssh-keygen -A && \
    echo "root:password" | chpasswd && \
    mkdir -p /root/.ssh && \
    ssh-keygen -t rsa -f /root/.ssh/id_rsa -q -P "" && \
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys && \
    chmod 600 /root/.ssh/authorized_keys

# Create mpiuser and grant sudo privileges
RUN adduser --disabled-password --gecos "" mpiuser && \
    usermod -aG sudo mpiuser

# Create NFS export directory for mpiuser
RUN mkdir -p /mnt/nfs_share && \
    chown mpiuser:mpiuser /mnt/nfs_share
RUN echo "mpiuser:mpi" | chpasswd

# Configure NFS exports
RUN echo "/mnt/nfs_share *(rw,sync,no_root_squash,no_subtree_check)" >> /etc/exports

# SSH setup for mpiuser
USER mpiuser
RUN mkdir -p /home/mpiuser/.ssh && \
    ssh-keygen -t rsa -f /home/mpiuser/.ssh/id_rsa -q -P "" && \
    cat /home/mpiuser/.ssh/id_rsa.pub >> /home/mpiuser/.ssh/authorized_keys && \
    chmod 700 /home/mpiuser/.ssh && \
    chmod 600 /home/mpiuser/.ssh/authorized_keys

# Back to root user for SSH configuration
USER root

RUN echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    echo "StrictHostKeyChecking no" >> /etc/ssh/ssh_config

# Expose port 22 for SSH
EXPOSE 22

# Start services
CMD service rpcbind start && service nfs-common start && /usr/sbin/sshd -D