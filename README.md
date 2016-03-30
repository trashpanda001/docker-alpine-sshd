### Alpine Linux SSHD

A lightweight [OpenSSH][openssh] [Docker image][dockerhub_project] built atop [Alpine Linux][alpine_linux]. Available on [GitHub][github_project].

The root password is "root". SSH host keys (RSA, DSA, ECDSA, and ED25519) are auto-generated when the container is started, unless already present. Virtual size is ~8.8 MB.

Tags ending with the suffix `-k8s` are build on [Alpine-Kubernetes][alpine_kubernetes], an image for Kubernetes and other Docker cluster environments that use DNS-based service discovery. It adds the necessary `search` domain support for DNS resolution.

#### Tags

* [`7.2`][dockerfile_7_2], [`latest`][dockerfile_7_2], [`7.2-k8s`][dockerfile_7_2_k8s] (OpenSSH_7.2p2-hpn14v4, OpenSSL 1.0.2g  1 Mar 2016)
* [`7.1`][dockerfile_7_1], [`7.1-k8s`][dockerfile_7_1_k8s] (Alpine Linux 3.3, OpenSSH_7.1p2-hpn14v4, OpenSSL 1.0.2e 3 Dec 2015)
* [`6.8`][dockerfile_6_8] (Alpine Linux 3.2, OpenSSH_6.8p1-hpn14v4, OpenSSL 1.0.2d 9 Jul 2015)
* [`6.7`][dockerfile_6_7] (Alpine Linux 3.1, OpenSSH_6.7p1-hpn14v4, OpenSSL 1.0.1p 9 Jul 2015)
* [`6.4`][dockerfile_6_4] (Alpine Linux 2.7, OpenSSH_6.4p1-hpn14v1, OpenSSL 1.0.1p 9 Jul 2015)
* [`6.2`][dockerfile_6_2] (Alpine Linux 2.6, OpenSSH_6.2p2, OpenSSL 1.0.1m 19 Mar 2015)

#### Basic usage

```bash
$ docker run -dP --name=sshd sickp/alpine-sshd # /sshd
4d9060b18efad357d1216704068006d0dcaadf06367dbd3f551d1e943aabb5ec

$ docker logs sshd
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519
Server listening on 0.0.0.0 port 22.
Server listening on :: port 22.

$ docker port sshd
22/tcp -> 0.0.0.0:32768

$ ssh root@localhost -p 32768 # on Mac/Windows replace localhost with $(docker-machine ip default)
# The root password is "root".
```

Any additional arguments are passed to `sshd`. For example, to enable debug output:

```bash
$ docker run -dP --name=sshd sickp/alpine-sshd /sshd -o LogLevel=DEBUG
```


### Customize

This image doesn't attempt to be "the one" solution that suits everyone's needs. It's actually pretty useless in the real world. But it is easy to extend via your own `Dockerfile`. See the [examples directory.][examples]

#### Change root password

Change the root password to something more fun, like "password" or "sunshine":

```dockerfile
FROM sickp/alpine-sshd:latest
RUN echo "root:sunshine" | chpasswd
```

#### Use authorized keys

Disable the root password completely, and use your SSH key instead:

```dockerfile
FROM sickp/alpine-sshd:latest
RUN passwd -d root
COPY identity.pub /root/.ssh/authorized_keys
```

#### Create multiple users

Disable root and create individual user accounts:

```dockerfile
FROM sickp/alpine-sshd:latest
ADD https://github.com/sickp.keys /home/sickp/.ssh/authorized_keys
ADD https://github.com/afrojas.keys /home/afrojas/.ssh/authorized_keys
RUN \
  passwd -d root && \
  adduser -D -s /bin/ash sickp && \
  passwd -u sickp && \
  chown -R sickp:sickp /home/sickp && \
  adduser -D -s /bin/ash afrojas && \
  passwd -u afrojas && \
  chown -R afrojas:afrojas /home/afrojas
```

#### Embed SSH host keys

Embed SSH host keys directly in your private image, so you can treat your containers like cattle.

```dockerfile
FROM sickp/alpine-sshd:latest
ADD https://github.com/sickp.keys /home/sickp/.ssh/authorized_keys
RUN \
  passwd -d root && \
  adduser -D -s /bin/ash sickp && \
  passwd -u sickp && \
  chown -R sickp:sickp /home/sickp && \
  ssh-keygen -A
```

#### History

- 2016-03-30 - Updated to 7.2p2, OpenSSL 1.0.2g.
- 2016-02-09 - Added support for ALPINE_NO_RESOLVER in Kubernetes version.
- 2016-01-28 - Added Kubernetes version, Alpine Linux 3.3.1.
- 2015-12-25 - Updated 7.1 to Alpine Linux 3.3.
- 2015-11-16 - Initial version, based on [Centos SSHD][centos_sshd].

[alpine_kubernetes]:  https://hub.docker.com/r/janeczku/alpine-kubernetes/
[alpine_linux]:       https://hub.docker.com/_/alpine/
[centos_sshd]:        https://hub.docker.com/r/sickp/centos-sshd/
[dockerhub_project]:  https://hub.docker.com/r/gliderlabs/alpine/
[dockerfile_6_2]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/6.2/Dockerfile
[dockerfile_6_4]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/6.4/Dockerfile
[dockerfile_6_7]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/6.7/Dockerfile
[dockerfile_6_8]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/6.8/Dockerfile
[dockerfile_7_1]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/7.1/Dockerfile
[dockerfile_7_1_k8s]: https://github.com/sickp/docker-alpine-sshd/tree/master/versions/7.1-k8s/Dockerfile
[dockerfile_7_2]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/7.2/Dockerfile
[dockerfile_7_2_k8s]: https://github.com/sickp/docker-alpine-sshd/tree/master/versions/7.2-k8s/Dockerfile
[examples]:           https://github.com/sickp/docker-alpine-sshd/tree/master/examples/
[github_project]:     https://github.com/sickp/docker-alpine-sshd/
[openssh]:            http://www.openssh.com
