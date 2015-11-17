# Alpine Linux 3.1.x, OpenSSH_6.7p1-hpn14v4, OpenSSL 1.0.1p 9 Jul 2015

FROM gliderlabs/alpine:3.1
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

RUN \
  apk-install openssh && \
  echo "root:root" | chpasswd

COPY entrypoint.sh /

EXPOSE 22
ENTRYPOINT ["/entrypoint.sh"]
