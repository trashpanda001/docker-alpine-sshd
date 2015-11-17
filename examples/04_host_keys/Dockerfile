FROM sickp/alpine-sshd:latest
ADD https://github.com/sickp.keys /home/sickp/.ssh/authorized_keys
RUN \
  passwd -d root && \
  adduser -D -s /bin/ash sickp && \
  passwd -u sickp && \
  chown -R sickp:sickp /home/sickp && \
  ssh-keygen -A
