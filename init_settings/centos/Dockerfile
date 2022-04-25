From centos:7
Run yum update -y && install -y \ 
    openssh-server \
    openssh-clients \
    traceroute \
 && install -y \
    epel-release \
    hping3 \
 && yum clean all \
 && rm -rf /var/cache/yum/*    
Run sed -e 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN echo "root:test_password" | chpasswd
