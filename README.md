### Alpine Linux SSHD

A lightweight [OpenSSH][openssh] [Docker image][dockerhub_project] built atop [Alpine Linux][alpine_linux]. Available on [GitHub][github_project].

The root password is "root". SSH host keys (RSA, DSA, ECDSA, and ED25519) are auto-generated when the container is started, unless already present.

> If you're running Kubernetes 1.2.0 or later on all your cluster nodes, you should now use the non-`k8s` tags below. These tags are built on Alpine Linux 3.4, which adds the necessary DNS search support for service discovery. Kubernetes defaults to `dnsPolicy=ClusterFirst` in pod specs, and defines a single `nameserver` in `/etc/resolv.conf`. This means things should finally work correctly for Alpine Linux images without modification.

#### OpenSSL Version Tags

- `7.2`, `latest` (OpenSSH_7.2p2-hpn14v4, OpenSSL 1.0.2j  26 Sep 2016, [Dockerfile][dockerfile_7_2])
- `7.1` (Alpine Linux 3.3, OpenSSH_7.1p2-hpn14v4, OpenSSL 1.0.2e 3 Dec 2015, [Dockerfile][dockerfile_7_1])
- `6.8` (Alpine Linux 3.2, OpenSSH_6.8p1-hpn14v4, OpenSSL 1.0.2d 9 Jul 2015, [Dockerfile][dockerfile_6_8])
- `6.7` (Alpine Linux 3.1, OpenSSH_6.7p1-hpn14v4, OpenSSL 1.0.1p 9 Jul 2015, [Dockerfile][dockerfile_6_7])
- `6.4` (Alpine Linux 2.7, OpenSSH_6.4p1-hpn14v1, OpenSSL 1.0.1p 9 Jul 2015, [Dockerfile][dockerfile_6_4])
- `6.2` (Alpine Linux 2.6, OpenSSH_6.2p2, OpenSSL 1.0.1m 19 Mar 2015, [Dockerfile][dockerfile_6_2])

#### Kubernetes <1.2.0

Tags with the `-k8s` suffix are built on [Alpine-Kubernetes 3.3][alpine_kubernetes], an image for Kubernetes and other Docker cluster environments that use DNS-based service discovery. It adds the necessary `search` domain support for DNS resolution.

- `7.2-k8s` (OpenSSH_7.2p2-hpn14v4, OpenSSL 1.0.2g  1 Mar 2016)
- `7.1-k8s` (Alpine Linux 3.3, OpenSSH_7.1p2-hpn14v4, OpenSSL 1.0.2e 3 Dec 2015)

### Basic Usage

```bash
$ docker run --rm --publish=2222:22 sickp/alpine-sshd:7.2 # /entrypoint.sh
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519
Server listening on 0.0.0.0 port 22.
Server listening on :: port 22.

$ ssh root@localhost -p 2222  # or $(docker-machine ip default)
# The root password is "root".

$ docker ps | grep 2222
cf8097ea881d        sickp/alpine-sshd:7.2   "/entrypoint.sh"    8 seconds ago       Up 4 seconds        0.0.0.0:2222->22/tcp   stoic_ptolemy
$ docker stop cf80
```

Any arguments are passed to `sshd`. For example, to enable debug output:

```bash
$ docker run --rm --publish=2222:22 sickp/alpine-sshd:7.2 -o LogLevel=DEBUG
...
```

#### Version Info

```bash
$ docker run --rm sickp/alpine-sshd:7.2 -v
...
OpenSSH_7.2p2-hpn14v4, OpenSSL 1.0.2j  26 Sep 2016
...

$ docker run --rm --entrypoint=cat sickp/alpine-sshd:7.2 /etc/alpine-release
3.4.4
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

- 2016-11-16 - Updated to Alpine Linux 3.4.4, OpenSSL 1.0.2j.
- 2016-09-29 - Updated to Alpine Linux 3.4.3, OpenSSL 1.0.2i.
- 2016-06-16 - Updated to Alpine Linux 3.4.0 (with `search` support for Kubernetes >=1.2.0).
- 2016-03-30 - Updated to 7.2p2, OpenSSL 1.0.2g.
- 2016-02-09 - Added support for ALPINE_NO_RESOLVER in Kubernetes version.
- 2016-01-28 - Added Kubernetes version, Alpine Linux 3.3.1.
- 2015-12-25 - Updated 7.1 to Alpine Linux 3.3.
- 2015-11-16 - Initial version, based on [Centos SSHD][centos_sshd].

[alpine_kubernetes]:  https://hub.docker.com/r/janeczku/alpine-kubernetes/
[alpine_linux]:       https://hub.docker.com/_/alpine/
[dockerhub_project]:  https://hub.docker.com/r/sickp/alpine-sshd/
[dockerfile_6_2]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/6.2/Dockerfile
[dockerfile_6_4]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/6.4/Dockerfile
[dockerfile_6_7]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/6.7/Dockerfile
[dockerfile_6_8]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/6.8/Dockerfile
[dockerfile_7_1]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/7.1/Dockerfile
[dockerfile_7_2]:     https://github.com/sickp/docker-alpine-sshd/tree/master/versions/7.2/Dockerfile
[examples]:           https://github.com/sickp/docker-alpine-sshd/tree/master/examples/
[github_project]:     https://github.com/sickp/docker-alpine-sshd/
[openssh]:            http://www.openssh.com
