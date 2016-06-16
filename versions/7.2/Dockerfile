FROM alpine:3.4
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

EXPOSE 22
ENTRYPOINT ["/entrypoint.sh"]

RUN apk --update add openssh \
  && sed -i s/#PermitRootLogin.*/PermitRootLogin\ yes/ /etc/ssh/sshd_config \
  && echo "root:root" | chpasswd \
  && rm -rf /var/cache/apk/*

COPY rootfs /
