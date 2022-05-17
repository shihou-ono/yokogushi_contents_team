FROM shihou/vyos:1.4
RUN setcap cap_net_admin=ep /sbin/tc
COPY config.boot opt/vyatta/etc/config/
