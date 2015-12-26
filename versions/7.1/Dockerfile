# Alpine Linux 3.3, OpenSSH_7.1p1-hpn14v4, OpenSSL 1.0.2e 3 Dec 2015

FROM gliderlabs/alpine:3.3
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

RUN \
  apk-install openssh && \
  sed -i s/#PermitRootLogin.*/PermitRootLogin\ yes/ /etc/ssh/sshd_config && \
  echo "root:root" | chpasswd

COPY entrypoint.sh /

EXPOSE 22
ENTRYPOINT ["/entrypoint.sh"]
