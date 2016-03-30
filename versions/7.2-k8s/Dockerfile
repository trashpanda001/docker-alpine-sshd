FROM janeczku/alpine-kubernetes:3.3
MAINTAINER Adrian B. Danieli "https://github.com/sickp"

RUN apk --update add openssh \
  && sed -i s/#PermitRootLogin.*/PermitRootLogin\ yes/ /etc/ssh/sshd_config \
  && echo "root:root" | chpasswd \
  && rm -rf /var/cache/apk/*

COPY rootfs /

EXPOSE 22
CMD ["/sshd"]
