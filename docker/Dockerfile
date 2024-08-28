FROM ubuntu:20.04

LABEL maintainer="Aoibheann Coppinger <eaoicop>"

# Built for ccd v2.9.1
# Install tzdata package non-interactively for Ubuntu
RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && apt-get install tzdata -qy && \
    ln -fs /usr/share/zoneinfo/Europe/Dublin /etc/localtime && \
    dpkg-reconfigure --frontend noninteractive tzdata && \
    echo "Europe/Dublin" > /etc/timezone && \
# Make sure the package repository is up to date
    apt-get -qy full-upgrade && \
    apt-get install -qy git && \
# Install a basic SSH server for Jenkins docker plugin
    apt-get install -qy openssh-server && \
    sed -i 's|session    required     pam_loginuid.so|session    optional     pam_loginuid.so|g' /etc/pam.d/sshd && \
    mkdir -p /var/run/sshd && \
# Install JDK 8 for Jenkins docker plugin
    apt-get install -qy openjdk-8-jdk && \
# Install other tools
    apt-get install -qy python3-pip wget curl unzip tar gzip sshpass vim rsync && \
# Install kubectl v1.17.3
# Check https://packages.cloud.google.com/apt/dists/kubernetes-xenial/main/binary-amd64/Packages for apt-get version number
    apt-get update && apt-get install -qy apt-transport-https gnupg2 && \
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add - && \
    echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | tee -a /etc/apt/sources.list.d/kubernetes.list && \
    apt-get update && \
    apt-get install -qy kubectl=1.17.3-00 && \
# Install helm v2.16.1 (client only)
    curl -LO https://get.helm.sh/helm-v2.16.1-linux-amd64.tar.gz && \
    tar -zxf helm-v2.16.1-linux-amd64.tar.gz && \
    mv linux-amd64/helm /usr/local/bin/helm && \
    rm -rf helm-v2.16.1-linux-amd64 helm-v2.16.1-linux-amd64.tar.gz && \
    helm init --client-only && \
# Install helm v3.2.1
    curl -LO https://get.helm.sh/helm-v3.2.1-linux-amd64.tar.gz && \
    tar -zxf helm-v3.2.1-linux-amd64.tar.gz && \
    mv linux-amd64/helm /usr/local/bin/helm3 && \
    rm -rf helm-v3.2.1-linux-amd64 helm-v3.2.1-linux-amd64.tar.gz && \
# Install YQ package
    curl -O -L https://github.com/mikefarah/yq/releases/download/3.3.0/yq_linux_amd64 && \
    chmod +x yq_linux_amd64 && \
    cp yq_linux_amd64 /usr/local/bin/yq && \
    rm yq_linux_amd64 && \
# Cleanup old packages
    apt-get -qy autoremove && \
# Add user jenkins to the image
    adduser --quiet lciadm100 && \
# Set password for the jenkins user
    echo "lciadm100:ericsson123" | chpasswd

COPY python3_modules.txt /root/python3_modules.txt

RUN /usr/bin/pip3 install -r /root/python3_modules.txt

# Copy files to set up ssh for gerrit
RUN mkdir /home/lciadm100/.ssh
COPY config /home/lciadm100/.ssh/config
COPY id_rsa /home/lciadm100/.ssh/id_rsa
COPY id_rsa.pub /home/lciadm100/.ssh/id_rsa.pub

RUN chown lciadm100:lciadm100 /home/lciadm100/.ssh/id_rsa.pub /home/lciadm100/.ssh/id_rsa /home/lciadm100/.ssh/config /home/lciadm100/.ssh && \
    chmod 644 /home/lciadm100/.ssh/id_rsa.pub && \
    chmod 600 /home/lciadm100/.ssh/id_rsa && \
    chmod 700 /home/lciadm100/.ssh

USER lciadm100

# Install ansible community kubernetes module
RUN ansible-galaxy collection install community.kubernetes

# Install ansible community openstack cloud module
RUN ansible-galaxy collection install openstack.cloud

USER root

# Standard SSH port
EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]

