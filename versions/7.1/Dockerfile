# Alpine Linux Edge, OpenSSH_7.1p1-hpn14v4, OpenSSL 1.0.2d 9 Jul 2015

FROM gliderlabs/alpine:edge
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

RUN \
  apk-install openssh && \
  sed -i s/#PermitRootLogin.*/PermitRootLogin\ yes/ /etc/ssh/sshd_config && \
  echo "root:root" | chpasswd

COPY entrypoint.sh /

EXPOSE 22
ENTRYPOINT ["/entrypoint.sh"]
