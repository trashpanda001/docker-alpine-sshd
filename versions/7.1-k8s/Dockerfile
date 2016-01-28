FROM janeczku/alpine-kubernetes:3.3
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

RUN \
  apk-install openssh && \
  sed -i s/#PermitRootLogin.*/PermitRootLogin\ yes/ /etc/ssh/sshd_config && \
  echo "root:root" | chpasswd

COPY rootfs /

EXPOSE 22
CMD ["/sshd"]
