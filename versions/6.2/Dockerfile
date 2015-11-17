# Alpine Linux 2.6.x, OpenSSH_6.2p2, OpenSSL 1.0.1m 19 Mar 2015

FROM gliderlabs/alpine:2.6
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

RUN \
  apk-install openssh && \
  echo "root:root" | chpasswd

COPY entrypoint.sh /

EXPOSE 22
ENTRYPOINT ["/entrypoint.sh"]
