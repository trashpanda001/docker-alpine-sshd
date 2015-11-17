# Alpine Linux 2.7.x, OpenSSH_6.4p1-hpn14v1, OpenSSL 1.0.1p 9 Jul 2015

FROM gliderlabs/alpine:2.7
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

RUN \
  apk-install openssh && \
  echo "root:root" | chpasswd

COPY entrypoint.sh /

EXPOSE 22
ENTRYPOINT ["/entrypoint.sh"]
