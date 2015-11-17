# Alpine Linux 3.2.x, OpenSSH_6.8p1-hpn14v4, OpenSSL 1.0.2d 9 Jul 2015

FROM gliderlabs/alpine:3.2
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

RUN \
  apk-install openssh && \
  echo "root:root" | chpasswd

COPY entrypoint.sh /

EXPOSE 22
ENTRYPOINT ["/entrypoint.sh"]
