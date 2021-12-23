From centos:7
Run yum -y install openssh-server openssh-clients traceroute net-tools NetworkManager
Run sed -e 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN echo "root:test_password" | chpasswd
