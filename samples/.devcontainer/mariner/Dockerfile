FROM mcr.microsoft.com/msfabric/synapsevscode/fabric-synapse-vscode:v2.0.6


RUN tdnf install -y wget
RUN tdnf install -y wget tar

ARG ACCEPT_EULA="N"
# Download the license file from the  URL
RUN wget -O ./license.txt https://raw.githubusercontent.com/microsoft/SynapseVSCode/main/container/LICENSE.txt

# Display the license agreement and prompt for acceptance
RUN cat ./license.txt && \
    if [ "$ACCEPT_EULA" != "Y" ]; then \
        echo "You must accept the terms of the license agreement. Set ACCEPT_EULA to 'Y'." && exit 1; \
    fi


WORKDIR /code

# Setup SSH with secure root login
RUN tdnf update -y

RUN tdnf install -y openssh-server nc \
 && mkdir /var/run/sshd \
 && echo 'root:password' | chpasswd \
 && sed -i 's/\#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# Install PySpark in the base Conda environment
ENV SPARK_VERSION=3.4.1
RUN conda install -c conda-forge pyspark==$SPARK_VERSION

# Generate new host keys
RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key && \
    chmod 600 /etc/ssh/ssh_host_rsa_key

# Restart SSH daemon
CMD ["/bin/sh", "-c", "/usr/sbin/sshd -D"]