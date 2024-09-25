FROM ubuntu:22.04

RUN apt-get update && \
    apt-get install -y mpich openssh-server && \
    apt-get clean

RUN mkdir /var/run/sshd && \
    ssh-keygen -A && \
    echo "root:password" | chpasswd && \
    mkdir /root/.ssh && \
    ssh-keygen -t rsa -f /root/.ssh/id_rsa -q -P "" && \
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys && \
    chmod 600 /root/.ssh/authorized_keys

RUN echo "PermitRootLogin yes" >> /etc/ssh/sshd_config && \
    echo "StrictHostKeyChecking no" >> /etc/ssh/ssh_config

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]