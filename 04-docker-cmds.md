## Docker cli commands
Commands:
 - [`docker version`](#docker-version)
 - [`docker images`](#docker-images)
 - [`docker run`](#docker-run)
 - [`docker ps`](#docker-ps)
 - [`docker commit`](#docker-commit)
 - [`docker build`](#docker-build)
 - [`docker start`](#docker-start)
 - [`docker stop`](#docker-stop)
 - [`docker exec`](#docker-exec)
 - [`docker rm`](#docker-rm)
 - [`docker rmi`](#docker-rmi)
 - [`docker push`](#docker-push)
 - [`docker tag`](#docker-tag)

### Docker version
Shows the Docker version information.

Cmd - `sudo docker version`

Documentation - https://docs.docker.com/engine/reference/commandline/version/

<details>
<summary>Example</summary>

```bash
$ sudo docker version
Client:
 Version:      17.03.0-ce
 API version:  1.26
 Go version:   go1.7.5
 Git commit:   3a232c8
 Built:        Tue Feb 28 07:57:58 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.03.0-ce
 API version:  1.26 (minimum version 1.12)
 Go version:   go1.7.5
 Git commit:   3a232c8
 Built:        Tue Feb 28 07:57:58 2017
 OS/Arch:      linux/amd64
 Experimental: false
```

</details>

### Docker images
Lists images. Includes information about the repository, tag and image ID.

Cmd - `sudo docker images`

Documentation - https://docs.docker.com/engine/reference/commandline/images/
<details>
<summary>Example</summary>

```bash
$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              48b5124b2768        6 weeks ago         1.84 kB
```

</details>

### Docker run
Runs a command in a new container. Image is specified with `repository:tag`.
Docker will cache images locally and does not need to download the image for each command.
If no name is chosed when a container is run, Docker will automatically choose a name at random.
It creates a writeable container layer over the specified image, and then starts it using the specified command.
That is, docker run is equivalent to the API `/containers/create` then `/containers/(id)/start`.

Cmd - `sudo docker run [options] [image] [command] [args]`

Documentation - https://docs.docker.com/engine/reference/commandline/run/

<details>
<summary>Example</summary>

```bash
$ sudo docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/

$ sudo docker run ubuntu:16.04 echo "Hello World"
Unable to find image 'ubuntu:16.04' locally
16.04: Pulling from library/ubuntu
d54efb8db41d: Pull complete
f8b845f45a87: Pull complete
e8db7bf7c39f: Pull complete
9654c40e9079: Pull complete
6d9ef359eaaa: Pull complete
Digest: sha256:dd7808d8792c9841d0b460122f1acf0a2dd1f56404f8d1e56298048885e45535
Status: Downloaded newer image for ubuntu:16.04
Hello World

$ sudo docker run ubuntu:16.04 ps ax
  PID TTY      STAT   TIME COMMAND
    1 ?        Rs     0:00 ps ax
```

</details>

**Container with Terminal**

 - Use `-i` and `-t` flags with `docker run`
 - The `-i` flag tells docker to connect to STDIN on the container
 - The `-t` flags specified to get a pseudo-terminal

Cmd - `sudo docker run -it ubuntu:16.04 bash`

<details>
<summary>Example</summary>

```bash
$ sudo docker run -it ubuntu:16.04 bash

root@d167e274da21:/# echo "Hello World"
Hello World

root@d167e274da21:/# ps -aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  18232  1916 ?        Ss   08:05   0:00 bash
root         9  0.0  0.0  34416  1464 ?        R+   08:05   0:00 ps -aux

root@d167e274da21:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

```

</details>

If we start the ubuntu bash process, on exiting the command line, the container stops.
To exit the container without stopping the container, use `CTRL + P + Q`

<details>
<summary>Example</summary>

```bash
$ sudo docker run -it ubuntu:16.04 bash

root@d167e274da21:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 08:05 ?        00:00:00 bash
root        11     1  0 08:19 ?        00:00:00 ps -ef

# CTRL + P + Q #
$ ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 02:30 ?        00:00:02 /sbin/init
root         2     0  0 02:30 ?        00:00:00 [kthreadd]
root         3     2  0 02:30 ?        00:00:00 [ksoftirqd/0]
root         5     2  0 02:30 ?        00:00:00 [kworker/0:0H]
root         7     2  0 02:30 ?        00:00:35 [rcu_sched]
root         8     2  0 02:30 ?        00:00:05 [rcuos/0]
root       816 29657  0 08:05 ?        00:00:00 docker-containerd-shim d167e274da219267efc0fb3f8afb632487e28612da9f9540a4957512ea9f2d16
 ...
root       832   816  0 08:05 pts/8    00:00:00 bash
...
root     29647     1  0 04:26 ?        00:00:36 /usr/bin/dockerd --raw-logs
root     29657 29647  0 04:26 ?        00:00:07 docker-containerd -l unix:///var/run/docker/libcontainerd/docker-containerd.sock --metr
 ...
```

</details>

**Running in detached mode**
  - Also known as running in the background or as a daemon
  - Use `-d` flag
  - To observe output use `docker logs [container ID]`

<details>
<summary>Example</summary>

```bash
$ sudo docker run -d centos:7 ping 127.0.0.1 -c 10
268b938a8899c15822e3ac58e8a65843aa1c58fa0255902f07ed07ed5b51741f

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
268b938a8899        centos:7            "ping 127.0.0.1 -c 10"   2 seconds ago       Up 2 seconds                            quirky_stonebraker
d167e274da21        ubuntu:16.04        "bash"                   35 minutes ago      Up 35 minutes                           trusting_clarke

$ sudo docker logs 268b938a8899
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.022 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.025 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.025 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.032 ms
64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.028 ms
64 bytes from 127.0.0.1: icmp_seq=6 ttl=64 time=0.030 ms
64 bytes from 127.0.0.1: icmp_seq=7 ttl=64 time=0.024 ms
64 bytes from 127.0.0.1: icmp_seq=8 ttl=64 time=0.025 ms
64 bytes from 127.0.0.1: icmp_seq=9 ttl=64 time=0.028 ms
64 bytes from 127.0.0.1: icmp_seq=10 ttl=64 time=0.038 ms

--- 127.0.0.1 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 8999ms
rtt min/avg/max/mdev = 0.022/0.027/0.038/0.007 ms
```

</details>

**Map container and host ports**
  - Run a web application inside a container
  - The `-p` flag to map container ports to host ports

<details>
<summary>Example</summary>

```bash
$ sudo docker run -d -P tomcat:7

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
acce37e17255        tomcat:7            "catalina.sh run"   18 seconds ago      Up 17 seconds       0.0.0.0:1024->8080/tcp   kickass_clarke
d167e274da21        ubuntu:16.04        "bash"              41 minutes ago      Up 41 minutes                                trusting_clarke

```

</details>

### Docker ps
Lists containers. `-a` flag lists all containers, including ones that are stopped.
It shows the short ID for containers.

Cmd - `sudo docker ps [OPTIONS]`

Documentation - https://docs.docker.com/engine/reference/commandline/ps/

<details>
<summary>Example</summary>

```bash
$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
d167e274da21        ubuntu:16.04        "bash"              26 minutes ago      Up 26 minutes                           trusting_clarke

$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS                         PORTS               NAMES
d167e274da21        ubuntu:16.04        "bash"                 27 minutes ago      Up 27 minutes                                      trusting_clarke
07dcebb08695        ubuntu:16.04        "ps ax"                34 minutes ago      Exited (0) 34 minutes ago                          competent_mestorf
7dce8ba03027        ubuntu:16.04        "echo 'Hello World'"   35 minutes ago      Exited (0) 35 minutes ago                          gallant_kalam
2a350761ac11        hello-world         "/hello"               36 minutes ago      Exited (0) 36 minutes ago                          laughing_meitner

```

</details>

### Docker commit
Creates a new image from a container's changes. Repository name should be based on `${username}/${application}`.
If no tag is specified, docker uses the default tag which is `latest`.

Cmd - `docker commit [OPTIONS] [CONTAINER ID/NAME] [REPOSITORY[:TAG]]`

Documentation - https://docs.docker.com/engine/reference/commandline/commit/

<details>
<summary>Example</summary>

```bash
$ sudo docker run -it ubuntu:16.04 bash

root@881240cce4a3:/# curl 127.0.0.1
bash: curl: command not found

root@881240cce4a3:/# apt-get update
Get:1 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/main Sources [1103 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/restricted Sources [5179 B]
Get:6 http://archive.ubuntu.com/ubuntu xenial/universe Sources [9802 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial/main amd64 Packages [1558 kB]
Get:8 http://archive.ubuntu.com/ubuntu xenial/restricted amd64 Packages [14.1 kB]
Get:9 http://archive.ubuntu.com/ubuntu xenial/universe amd64 Packages [9827 kB]
Get:10 http://archive.ubuntu.com/ubuntu xenial-updates/main Sources [295 kB]
Get:11 http://archive.ubuntu.com/ubuntu xenial-updates/restricted Sources [2815 B]
Get:12 http://archive.ubuntu.com/ubuntu xenial-updates/universe Sources [173 kB]
Get:13 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [618 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial-updates/restricted amd64 Packages [12.4 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [541 kB]
Get:16 http://archive.ubuntu.com/ubuntu xenial-security/main Sources [73.4 kB]
Get:17 http://archive.ubuntu.com/ubuntu xenial-security/restricted Sources [2392 B]
Get:18 http://archive.ubuntu.com/ubuntu xenial-security/universe Sources [26.3 kB]
Get:19 http://archive.ubuntu.com/ubuntu xenial-security/main amd64 Packages [273 kB]
Get:20 http://archive.ubuntu.com/ubuntu xenial-security/restricted amd64 Packages [12.0 kB]
Get:21 http://archive.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [109 kB]
Fetched 24.9 MB in 10s (2393 kB/s)
Reading package lists... Done

root@881240cce4a3:/# apt-get install -y curl
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  ca-certificates krb5-locales libasn1-8-heimdal libcurl3-gnutls libffi6 libgmp10 libgnutls30 libgssapi-krb5-2 libgssapi3-heimdal
  libhcrypto4-heimdal libheimbase1-heimdal libheimntlm0-heimdal libhogweed4 libhx509-5-heimdal libidn11 libk5crypto3 libkeyutils1
  libkrb5-26-heimdal libkrb5-3 libkrb5support0 libldap-2.4-2 libnettle6 libp11-kit0 libroken18-heimdal librtmp1 libsasl2-2
  libsasl2-modules libsasl2-modules-db libsqlite3-0 libssl1.0.0 libtasn1-6 libwind0-heimdal openssl
Suggested packages:
  gnutls-bin krb5-doc krb5-user libsasl2-modules-otp libsasl2-modules-ldap libsasl2-modules-sql libsasl2-modules-gssapi-mit
  | libsasl2-modules-gssapi-heimdal
The following NEW packages will be installed:
  ca-certificates curl krb5-locales libasn1-8-heimdal libcurl3-gnutls libffi6 libgmp10 libgnutls30 libgssapi-krb5-2
  libgssapi3-heimdal libhcrypto4-heimdal libheimbase1-heimdal libheimntlm0-heimdal libhogweed4 libhx509-5-heimdal libidn11
  libk5crypto3 libkeyutils1 libkrb5-26-heimdal libkrb5-3 libkrb5support0 libldap-2.4-2 libnettle6 libp11-kit0 libroken18-heimdal
  librtmp1 libsasl2-2 libsasl2-modules libsasl2-modules-db libsqlite3-0 libssl1.0.0 libtasn1-6 libwind0-heimdal openssl
0 upgraded, 34 newly installed, 0 to remove and 0 not upgraded.
Need to get 5363 kB of archives.
After this operation, 19.1 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libffi6 amd64 3.2.1-4 [17.8 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgmp10 amd64 2:6.1.0+dfsg-2 [240 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libnettle6 amd64 3.2-1ubuntu0.16.04.1 [93.5 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libhogweed4 amd64 3.2-1ubuntu0.16.04.1 [136 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libidn11 amd64 1.32-3ubuntu1.1 [45.6 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libp11-kit0 amd64 0.23.2-5~ubuntu16.04.1 [105 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libtasn1-6 amd64 4.7-3ubuntu0.16.04.1 [43.2 kB]
Get:8 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libgnutls30 amd64 3.4.10-4ubuntu1.2 [547 kB]
Get:9 http://archive.ubuntu.com/ubuntu xenial/main amd64 libsqlite3-0 amd64 3.11.0-1ubuntu1 [396 kB]
Get:10 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libssl1.0.0 amd64 1.0.2g-1ubuntu4.6 [1082 kB]
Get:11 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 openssl amd64 1.0.2g-1ubuntu4.6 [492 kB]
Get:12 http://archive.ubuntu.com/ubuntu xenial/main amd64 ca-certificates all 20160104ubuntu1 [191 kB]
Get:13 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 krb5-locales all 1.13.2+dfsg-5ubuntu2 [13.2 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial/main amd64 libroken18-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [41.2 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial/main amd64 libasn1-8-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [174 kB]
Get:16 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libkrb5support0 amd64 1.13.2+dfsg-5ubuntu2 [30.8 kB]
Get:17 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libk5crypto3 amd64 1.13.2+dfsg-5ubuntu2 [81.2 kB]
Get:18 http://archive.ubuntu.com/ubuntu xenial/main amd64 libkeyutils1 amd64 1.5.9-8ubuntu1 [9904 B]
Get:19 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libkrb5-3 amd64 1.13.2+dfsg-5ubuntu2 [273 kB]
Get:20 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libgssapi-krb5-2 amd64 1.13.2+dfsg-5ubuntu2 [120 kB]
Get:21 http://archive.ubuntu.com/ubuntu xenial/main amd64 libhcrypto4-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [84.9 kB]
Get:22 http://archive.ubuntu.com/ubuntu xenial/main amd64 libheimbase1-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [29.2 kB]
Get:23 http://archive.ubuntu.com/ubuntu xenial/main amd64 libwind0-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [48.2 kB]
Get:24 http://archive.ubuntu.com/ubuntu xenial/main amd64 libhx509-5-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [107 kB]
Get:25 http://archive.ubuntu.com/ubuntu xenial/main amd64 libkrb5-26-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [202 kB]
Get:26 http://archive.ubuntu.com/ubuntu xenial/main amd64 libheimntlm0-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [15.1 kB]
Get:27 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgssapi3-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [96.1 kB]
Get:28 http://archive.ubuntu.com/ubuntu xenial/main amd64 libsasl2-modules-db amd64 2.1.26.dfsg1-14build1 [14.5 kB]
Get:29 http://archive.ubuntu.com/ubuntu xenial/main amd64 libsasl2-2 amd64 2.1.26.dfsg1-14build1 [48.7 kB]
Get:30 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libldap-2.4-2 amd64 2.4.42+dfsg-2ubuntu3.1 [161 kB]
Get:31 http://archive.ubuntu.com/ubuntu xenial/main amd64 librtmp1 amd64 2.4+20151223.gitfa8646d-1build1 [53.9 kB]
Get:32 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libcurl3-gnutls amd64 7.47.0-1ubuntu2.2 [184 kB]
Get:33 http://archive.ubuntu.com/ubuntu xenial/main amd64 libsasl2-modules amd64 2.1.26.dfsg1-14build1 [47.5 kB]
Get:34 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 curl amd64 7.47.0-1ubuntu2.2 [139 kB]
Fetched 5363 kB in 4s (1160 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libffi6:amd64.
(Reading database ... 7256 files and directories currently installed.)
Preparing to unpack .../libffi6_3.2.1-4_amd64.deb ...
Unpacking libffi6:amd64 (3.2.1-4) ...
Selecting previously unselected package libgmp10:amd64.
Preparing to unpack .../libgmp10_2%3a6.1.0+dfsg-2_amd64.deb ...
Unpacking libgmp10:amd64 (2:6.1.0+dfsg-2) ...
Selecting previously unselected package libnettle6:amd64.
Preparing to unpack .../libnettle6_3.2-1ubuntu0.16.04.1_amd64.deb ...
Unpacking libnettle6:amd64 (3.2-1ubuntu0.16.04.1) ...
Selecting previously unselected package libhogweed4:amd64.
Preparing to unpack .../libhogweed4_3.2-1ubuntu0.16.04.1_amd64.deb ...
Unpacking libhogweed4:amd64 (3.2-1ubuntu0.16.04.1) ...
Selecting previously unselected package libidn11:amd64.
Preparing to unpack .../libidn11_1.32-3ubuntu1.1_amd64.deb ...
Unpacking libidn11:amd64 (1.32-3ubuntu1.1) ...
Selecting previously unselected package libp11-kit0:amd64.
Preparing to unpack .../libp11-kit0_0.23.2-5~ubuntu16.04.1_amd64.deb ...
Unpacking libp11-kit0:amd64 (0.23.2-5~ubuntu16.04.1) ...
Selecting previously unselected package libtasn1-6:amd64.
Preparing to unpack .../libtasn1-6_4.7-3ubuntu0.16.04.1_amd64.deb ...
Unpacking libtasn1-6:amd64 (4.7-3ubuntu0.16.04.1) ...
Selecting previously unselected package libgnutls30:amd64.
Preparing to unpack .../libgnutls30_3.4.10-4ubuntu1.2_amd64.deb ...
Unpacking libgnutls30:amd64 (3.4.10-4ubuntu1.2) ...
Selecting previously unselected package libsqlite3-0:amd64.
Preparing to unpack .../libsqlite3-0_3.11.0-1ubuntu1_amd64.deb ...
Unpacking libsqlite3-0:amd64 (3.11.0-1ubuntu1) ...
Selecting previously unselected package libssl1.0.0:amd64.
Preparing to unpack .../libssl1.0.0_1.0.2g-1ubuntu4.6_amd64.deb ...
Unpacking libssl1.0.0:amd64 (1.0.2g-1ubuntu4.6) ...
Selecting previously unselected package openssl.
Preparing to unpack .../openssl_1.0.2g-1ubuntu4.6_amd64.deb ...
Unpacking openssl (1.0.2g-1ubuntu4.6) ...
Selecting previously unselected package ca-certificates.
Preparing to unpack .../ca-certificates_20160104ubuntu1_all.deb ...
Unpacking ca-certificates (20160104ubuntu1) ...
Selecting previously unselected package krb5-locales.
Preparing to unpack .../krb5-locales_1.13.2+dfsg-5ubuntu2_all.deb ...
Unpacking krb5-locales (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libroken18-heimdal:amd64.
Preparing to unpack .../libroken18-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libroken18-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libasn1-8-heimdal:amd64.
Preparing to unpack .../libasn1-8-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libasn1-8-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libkrb5support0:amd64.
Preparing to unpack .../libkrb5support0_1.13.2+dfsg-5ubuntu2_amd64.deb ...
Unpacking libkrb5support0:amd64 (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libk5crypto3:amd64.
Preparing to unpack .../libk5crypto3_1.13.2+dfsg-5ubuntu2_amd64.deb ...
Unpacking libk5crypto3:amd64 (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libkeyutils1:amd64.
Preparing to unpack .../libkeyutils1_1.5.9-8ubuntu1_amd64.deb ...
Unpacking libkeyutils1:amd64 (1.5.9-8ubuntu1) ...
Selecting previously unselected package libkrb5-3:amd64.
Preparing to unpack .../libkrb5-3_1.13.2+dfsg-5ubuntu2_amd64.deb ...
Unpacking libkrb5-3:amd64 (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libgssapi-krb5-2:amd64.
Preparing to unpack .../libgssapi-krb5-2_1.13.2+dfsg-5ubuntu2_amd64.deb ...
Unpacking libgssapi-krb5-2:amd64 (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libhcrypto4-heimdal:amd64.
Preparing to unpack .../libhcrypto4-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libhcrypto4-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libheimbase1-heimdal:amd64.
Preparing to unpack .../libheimbase1-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libheimbase1-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libwind0-heimdal:amd64.
Preparing to unpack .../libwind0-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libwind0-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libhx509-5-heimdal:amd64.
Preparing to unpack .../libhx509-5-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libhx509-5-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libkrb5-26-heimdal:amd64.
Preparing to unpack .../libkrb5-26-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libkrb5-26-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libheimntlm0-heimdal:amd64.
Preparing to unpack .../libheimntlm0-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libheimntlm0-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libgssapi3-heimdal:amd64.
Preparing to unpack .../libgssapi3-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libgssapi3-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libsasl2-modules-db:amd64.
Preparing to unpack .../libsasl2-modules-db_2.1.26.dfsg1-14build1_amd64.deb ...
Unpacking libsasl2-modules-db:amd64 (2.1.26.dfsg1-14build1) ...
Selecting previously unselected package libsasl2-2:amd64.
Preparing to unpack .../libsasl2-2_2.1.26.dfsg1-14build1_amd64.deb ...
Unpacking libsasl2-2:amd64 (2.1.26.dfsg1-14build1) ...
Selecting previously unselected package libldap-2.4-2:amd64.
Preparing to unpack .../libldap-2.4-2_2.4.42+dfsg-2ubuntu3.1_amd64.deb ...
Unpacking libldap-2.4-2:amd64 (2.4.42+dfsg-2ubuntu3.1) ...
Selecting previously unselected package librtmp1:amd64.
Preparing to unpack .../librtmp1_2.4+20151223.gitfa8646d-1build1_amd64.deb ...
Unpacking librtmp1:amd64 (2.4+20151223.gitfa8646d-1build1) ...
Selecting previously unselected package libcurl3-gnutls:amd64.
Preparing to unpack .../libcurl3-gnutls_7.47.0-1ubuntu2.2_amd64.deb ...
Unpacking libcurl3-gnutls:amd64 (7.47.0-1ubuntu2.2) ...
Selecting previously unselected package libsasl2-modules:amd64.
Preparing to unpack .../libsasl2-modules_2.1.26.dfsg1-14build1_amd64.deb ...
Unpacking libsasl2-modules:amd64 (2.1.26.dfsg1-14build1) ...
Selecting previously unselected package curl.
Preparing to unpack .../curl_7.47.0-1ubuntu2.2_amd64.deb ...
Unpacking curl (7.47.0-1ubuntu2.2) ...
Processing triggers for libc-bin (2.23-0ubuntu5) ...
Setting up libffi6:amd64 (3.2.1-4) ...
Setting up libgmp10:amd64 (2:6.1.0+dfsg-2) ...
Setting up libnettle6:amd64 (3.2-1ubuntu0.16.04.1) ...
Setting up libhogweed4:amd64 (3.2-1ubuntu0.16.04.1) ...
Setting up libidn11:amd64 (1.32-3ubuntu1.1) ...
Setting up libp11-kit0:amd64 (0.23.2-5~ubuntu16.04.1) ...
Setting up libtasn1-6:amd64 (4.7-3ubuntu0.16.04.1) ...
Setting up libgnutls30:amd64 (3.4.10-4ubuntu1.2) ...
Setting up libsqlite3-0:amd64 (3.11.0-1ubuntu1) ...
Setting up libssl1.0.0:amd64 (1.0.2g-1ubuntu4.6) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 76.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/local/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up openssl (1.0.2g-1ubuntu4.6) ...
Setting up ca-certificates (20160104ubuntu1) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 76.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/local/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up krb5-locales (1.13.2+dfsg-5ubuntu2) ...
Setting up libroken18-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libasn1-8-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libkrb5support0:amd64 (1.13.2+dfsg-5ubuntu2) ...
Setting up libk5crypto3:amd64 (1.13.2+dfsg-5ubuntu2) ...
Setting up libkeyutils1:amd64 (1.5.9-8ubuntu1) ...
Setting up libkrb5-3:amd64 (1.13.2+dfsg-5ubuntu2) ...
Setting up libgssapi-krb5-2:amd64 (1.13.2+dfsg-5ubuntu2) ...
Setting up libhcrypto4-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libheimbase1-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libwind0-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libhx509-5-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libkrb5-26-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libheimntlm0-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libgssapi3-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libsasl2-modules-db:amd64 (2.1.26.dfsg1-14build1) ...
Setting up libsasl2-2:amd64 (2.1.26.dfsg1-14build1) ...
Setting up libldap-2.4-2:amd64 (2.4.42+dfsg-2ubuntu3.1) ...
Setting up librtmp1:amd64 (2.4+20151223.gitfa8646d-1build1) ...
Setting up libcurl3-gnutls:amd64 (7.47.0-1ubuntu2.2) ...
Setting up libsasl2-modules:amd64 (2.1.26.dfsg1-14build1) ...
Setting up curl (7.47.0-1ubuntu2.2) ...
Processing triggers for libc-bin (2.23-0ubuntu5) ...
Processing triggers for ca-certificates (20160104ubuntu1) ...
Updating certificates in /etc/ssl/certs...
173 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.

root@e361cd827d8c:/# which curl
/usr/bin/curl

root@881240cce4a3:/# exit

$ sudo docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                                PORTS               NAMES
881240cce4a3        ubuntu:16.04        "bash"                   4 minutes ago       Exited (127) Less than a second ago                       xenodochial_morse

$ sudo docker commit 881240cce4a3 sudosuhas/myimg:1.0
sha256:6cd06ec4c83558b2d6c7e26f1e76f08a2bc4448712c3e6e4c156830d3ab2bec5

$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
sudosuhas/myimg     1.0                 6cd06ec4c835        45 seconds ago      187 MB
tomcat              7                   0f140c316816        2 days ago          356 MB
ubuntu              16.04               0ef2e08ed3fa        3 days ago          130 MB
hello-world         latest              48b5124b2768        6 weeks ago         1.84 kB
centos              7                   67591570dd29        2 months ago        192 MB

$ sudo docker run -it sudosuhas/myimg:1.0 bash
root@e361cd827d8c:/# which curl
/usr/bin/curl
```

</details>

### Docker build
Builds an image from a Dockerfile. The path specified will be the build context.
Files within the build context can be referenced in `Dockerfile`.
Docker client packs all the files in the build context into a tar and sends it to the daemon.
Also, the build context is where docker looks for the `Dockerfile`. A different path can be specified by using the `-f` option.
Usually `-t` flag is used to tag the build.

Cmd - `docker build [OPTIONS] PATH | URL | -`

Documentation - https://docs.docker.com/engine/reference/commandline/build/

<details>

<summary>Example</summary>

```Dockerfile
FROM ubuntu:16.04
RUN apt-get update && apt-get install -y curl
```

```bash
$ mkdir -p learn-docker/myimg

$ cd learn-docker/myimg

# Create Dockerfile and paste contents from above
$ nano Dockerfile

$ sudo docker build -t sudosuhas/myimg:1.1 .
Sending build context to Docker daemon 2.048 kB
Step 1/2 : FROM ubuntu:16.04
 ---> 0ef2e08ed3fa
Step 2/2 : RUN apt-get update && apt-get install -y curl
 ---> Running in 3c4f82a1e1af
Get:1 http://archive.ubuntu.com/ubuntu xenial InRelease [247 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [102 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-security InRelease [102 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial/main Sources [1103 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/restricted Sources [5179 B]
Get:6 http://archive.ubuntu.com/ubuntu xenial/universe Sources [9802 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial/main amd64 Packages [1558 kB]
Get:8 http://archive.ubuntu.com/ubuntu xenial/restricted amd64 Packages [14.1 kB]
Get:9 http://archive.ubuntu.com/ubuntu xenial/universe amd64 Packages [9827 kB]
Get:10 http://archive.ubuntu.com/ubuntu xenial-updates/main Sources [295 kB]
Get:11 http://archive.ubuntu.com/ubuntu xenial-updates/restricted Sources [2815 B]
Get:12 http://archive.ubuntu.com/ubuntu xenial-updates/universe Sources [173 kB]
Get:13 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [618 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial-updates/restricted amd64 Packages [12.4 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [541 kB]
Get:16 http://archive.ubuntu.com/ubuntu xenial-security/main Sources [73.4 kB]
Get:17 http://archive.ubuntu.com/ubuntu xenial-security/restricted Sources [2392 B]
Get:18 http://archive.ubuntu.com/ubuntu xenial-security/universe Sources [26.3 kB]
Get:19 http://archive.ubuntu.com/ubuntu xenial-security/main amd64 Packages [273 kB]
Get:20 http://archive.ubuntu.com/ubuntu xenial-security/restricted amd64 Packages [12.0 kB]
Get:21 http://archive.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [109 kB]
Fetched 24.9 MB in 11s (2220 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  ca-certificates krb5-locales libasn1-8-heimdal libcurl3-gnutls libffi6
  libgmp10 libgnutls30 libgssapi-krb5-2 libgssapi3-heimdal libhcrypto4-heimdal
  libheimbase1-heimdal libheimntlm0-heimdal libhogweed4 libhx509-5-heimdal
  libidn11 libk5crypto3 libkeyutils1 libkrb5-26-heimdal libkrb5-3
  libkrb5support0 libldap-2.4-2 libnettle6 libp11-kit0 libroken18-heimdal
  librtmp1 libsasl2-2 libsasl2-modules libsasl2-modules-db libsqlite3-0
  libssl1.0.0 libtasn1-6 libwind0-heimdal openssl
Suggested packages:
  gnutls-bin krb5-doc krb5-user libsasl2-modules-otp libsasl2-modules-ldap
  libsasl2-modules-sql libsasl2-modules-gssapi-mit
  | libsasl2-modules-gssapi-heimdal
The following NEW packages will be installed:
  ca-certificates curl krb5-locales libasn1-8-heimdal libcurl3-gnutls libffi6
  libgmp10 libgnutls30 libgssapi-krb5-2 libgssapi3-heimdal libhcrypto4-heimdal
  libheimbase1-heimdal libheimntlm0-heimdal libhogweed4 libhx509-5-heimdal
  libidn11 libk5crypto3 libkeyutils1 libkrb5-26-heimdal libkrb5-3
  libkrb5support0 libldap-2.4-2 libnettle6 libp11-kit0 libroken18-heimdal
  librtmp1 libsasl2-2 libsasl2-modules libsasl2-modules-db libsqlite3-0
  libssl1.0.0 libtasn1-6 libwind0-heimdal openssl
0 upgraded, 34 newly installed, 0 to remove and 0 not upgraded.
Need to get 5363 kB of archives.
After this operation, 19.1 MB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libffi6 amd64 3.2.1-4 [17.8 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgmp10 amd64 2:6.1.0+dfsg-2 [240 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libnettle6 amd64 3.2-1ubuntu0.16.04.1 [93.5 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libhogweed4 amd64 3.2-1ubuntu0.16.04.1 [136 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libidn11 amd64 1.32-3ubuntu1.1 [45.6 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libp11-kit0 amd64 0.23.2-5~ubuntu16.04.1 [105 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libtasn1-6 amd64 4.7-3ubuntu0.16.04.1 [43.2 kB]
Get:8 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libgnutls30 amd64 3.4.10-4ubuntu1.2 [547 kB]
Get:9 http://archive.ubuntu.com/ubuntu xenial/main amd64 libsqlite3-0 amd64 3.11.0-1ubuntu1 [396 kB]
Get:10 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libssl1.0.0 amd64 1.0.2g-1ubuntu4.6 [1082 kB]
Get:11 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 openssl amd64 1.0.2g-1ubuntu4.6 [492 kB]
Get:12 http://archive.ubuntu.com/ubuntu xenial/main amd64 ca-certificates all 20160104ubuntu1 [191 kB]
Get:13 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 krb5-locales all 1.13.2+dfsg-5ubuntu2 [13.2 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial/main amd64 libroken18-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [41.2 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial/main amd64 libasn1-8-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [174 kB]
Get:16 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libkrb5support0 amd64 1.13.2+dfsg-5ubuntu2 [30.8 kB]
Get:17 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libk5crypto3 amd64 1.13.2+dfsg-5ubuntu2 [81.2 kB]
Get:18 http://archive.ubuntu.com/ubuntu xenial/main amd64 libkeyutils1 amd64 1.5.9-8ubuntu1 [9904 B]
Get:19 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libkrb5-3 amd64 1.13.2+dfsg-5ubuntu2 [273 kB]
Get:20 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libgssapi-krb5-2 amd64 1.13.2+dfsg-5ubuntu2 [120 kB]
Get:21 http://archive.ubuntu.com/ubuntu xenial/main amd64 libhcrypto4-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [84.9 kB]
Get:22 http://archive.ubuntu.com/ubuntu xenial/main amd64 libheimbase1-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [29.2 kB]
Get:23 http://archive.ubuntu.com/ubuntu xenial/main amd64 libwind0-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [48.2 kB]
Get:24 http://archive.ubuntu.com/ubuntu xenial/main amd64 libhx509-5-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [107 kB]
Get:25 http://archive.ubuntu.com/ubuntu xenial/main amd64 libkrb5-26-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [202 kB]
Get:26 http://archive.ubuntu.com/ubuntu xenial/main amd64 libheimntlm0-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [15.1 kB]
Get:27 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgssapi3-heimdal amd64 1.7~git20150920+dfsg-4ubuntu1 [96.1 kB]
Get:28 http://archive.ubuntu.com/ubuntu xenial/main amd64 libsasl2-modules-db amd64 2.1.26.dfsg1-14build1 [14.5 kB]
Get:29 http://archive.ubuntu.com/ubuntu xenial/main amd64 libsasl2-2 amd64 2.1.26.dfsg1-14build1 [48.7 kB]
Get:30 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libldap-2.4-2 amd64 2.4.42+dfsg-2ubuntu3.1 [161 kB]
Get:31 http://archive.ubuntu.com/ubuntu xenial/main amd64 librtmp1 amd64 2.4+20151223.gitfa8646d-1build1 [53.9 kB]
Get:32 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libcurl3-gnutls amd64 7.47.0-1ubuntu2.2 [184 kB]
Get:33 http://archive.ubuntu.com/ubuntu xenial/main amd64 libsasl2-modules amd64 2.1.26.dfsg1-14build1 [47.5 kB]
Get:34 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 curl amd64 7.47.0-1ubuntu2.2 [139 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 5363 kB in 4s (1091 kB/s)
Selecting previously unselected package libffi6:amd64.
(Reading database ... 7256 files and directories currently installed.)
Preparing to unpack .../libffi6_3.2.1-4_amd64.deb ...
Unpacking libffi6:amd64 (3.2.1-4) ...
Selecting previously unselected package libgmp10:amd64.
Preparing to unpack .../libgmp10_2%3a6.1.0+dfsg-2_amd64.deb ...
Unpacking libgmp10:amd64 (2:6.1.0+dfsg-2) ...
Selecting previously unselected package libnettle6:amd64.
Preparing to unpack .../libnettle6_3.2-1ubuntu0.16.04.1_amd64.deb ...
Unpacking libnettle6:amd64 (3.2-1ubuntu0.16.04.1) ...
Selecting previously unselected package libhogweed4:amd64.
Preparing to unpack .../libhogweed4_3.2-1ubuntu0.16.04.1_amd64.deb ...
Unpacking libhogweed4:amd64 (3.2-1ubuntu0.16.04.1) ...
Selecting previously unselected package libidn11:amd64.
Preparing to unpack .../libidn11_1.32-3ubuntu1.1_amd64.deb ...
Unpacking libidn11:amd64 (1.32-3ubuntu1.1) ...
Selecting previously unselected package libp11-kit0:amd64.
Preparing to unpack .../libp11-kit0_0.23.2-5~ubuntu16.04.1_amd64.deb ...
Unpacking libp11-kit0:amd64 (0.23.2-5~ubuntu16.04.1) ...
Selecting previously unselected package libtasn1-6:amd64.
Preparing to unpack .../libtasn1-6_4.7-3ubuntu0.16.04.1_amd64.deb ...
Unpacking libtasn1-6:amd64 (4.7-3ubuntu0.16.04.1) ...
Selecting previously unselected package libgnutls30:amd64.
Preparing to unpack .../libgnutls30_3.4.10-4ubuntu1.2_amd64.deb ...
Unpacking libgnutls30:amd64 (3.4.10-4ubuntu1.2) ...
Selecting previously unselected package libsqlite3-0:amd64.
Preparing to unpack .../libsqlite3-0_3.11.0-1ubuntu1_amd64.deb ...
Unpacking libsqlite3-0:amd64 (3.11.0-1ubuntu1) ...
Selecting previously unselected package libssl1.0.0:amd64.
Preparing to unpack .../libssl1.0.0_1.0.2g-1ubuntu4.6_amd64.deb ...
Unpacking libssl1.0.0:amd64 (1.0.2g-1ubuntu4.6) ...
Selecting previously unselected package openssl.
Preparing to unpack .../openssl_1.0.2g-1ubuntu4.6_amd64.deb ...
Unpacking openssl (1.0.2g-1ubuntu4.6) ...
Selecting previously unselected package ca-certificates.
Preparing to unpack .../ca-certificates_20160104ubuntu1_all.deb ...
Unpacking ca-certificates (20160104ubuntu1) ...
Selecting previously unselected package krb5-locales.
Preparing to unpack .../krb5-locales_1.13.2+dfsg-5ubuntu2_all.deb ...
Unpacking krb5-locales (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libroken18-heimdal:amd64.
Preparing to unpack .../libroken18-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libroken18-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libasn1-8-heimdal:amd64.
Preparing to unpack .../libasn1-8-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libasn1-8-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libkrb5support0:amd64.
Preparing to unpack .../libkrb5support0_1.13.2+dfsg-5ubuntu2_amd64.deb ...
Unpacking libkrb5support0:amd64 (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libk5crypto3:amd64.
Preparing to unpack .../libk5crypto3_1.13.2+dfsg-5ubuntu2_amd64.deb ...
Unpacking libk5crypto3:amd64 (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libkeyutils1:amd64.
Preparing to unpack .../libkeyutils1_1.5.9-8ubuntu1_amd64.deb ...
Unpacking libkeyutils1:amd64 (1.5.9-8ubuntu1) ...
Selecting previously unselected package libkrb5-3:amd64.
Preparing to unpack .../libkrb5-3_1.13.2+dfsg-5ubuntu2_amd64.deb ...
Unpacking libkrb5-3:amd64 (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libgssapi-krb5-2:amd64.
Preparing to unpack .../libgssapi-krb5-2_1.13.2+dfsg-5ubuntu2_amd64.deb ...
Unpacking libgssapi-krb5-2:amd64 (1.13.2+dfsg-5ubuntu2) ...
Selecting previously unselected package libhcrypto4-heimdal:amd64.
Preparing to unpack .../libhcrypto4-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libhcrypto4-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libheimbase1-heimdal:amd64.
Preparing to unpack .../libheimbase1-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libheimbase1-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libwind0-heimdal:amd64.
Preparing to unpack .../libwind0-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libwind0-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libhx509-5-heimdal:amd64.
Preparing to unpack .../libhx509-5-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libhx509-5-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libkrb5-26-heimdal:amd64.
Preparing to unpack .../libkrb5-26-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libkrb5-26-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libheimntlm0-heimdal:amd64.
Preparing to unpack .../libheimntlm0-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libheimntlm0-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libgssapi3-heimdal:amd64.
Preparing to unpack .../libgssapi3-heimdal_1.7~git20150920+dfsg-4ubuntu1_amd64.deb ...
Unpacking libgssapi3-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Selecting previously unselected package libsasl2-modules-db:amd64.
Preparing to unpack .../libsasl2-modules-db_2.1.26.dfsg1-14build1_amd64.deb ...
Unpacking libsasl2-modules-db:amd64 (2.1.26.dfsg1-14build1) ...
Selecting previously unselected package libsasl2-2:amd64.
Preparing to unpack .../libsasl2-2_2.1.26.dfsg1-14build1_amd64.deb ...
Unpacking libsasl2-2:amd64 (2.1.26.dfsg1-14build1) ...
Selecting previously unselected package libldap-2.4-2:amd64.
Preparing to unpack .../libldap-2.4-2_2.4.42+dfsg-2ubuntu3.1_amd64.deb ...
Unpacking libldap-2.4-2:amd64 (2.4.42+dfsg-2ubuntu3.1) ...
Selecting previously unselected package librtmp1:amd64.
Preparing to unpack .../librtmp1_2.4+20151223.gitfa8646d-1build1_amd64.deb ...
Unpacking librtmp1:amd64 (2.4+20151223.gitfa8646d-1build1) ...
Selecting previously unselected package libcurl3-gnutls:amd64.
Preparing to unpack .../libcurl3-gnutls_7.47.0-1ubuntu2.2_amd64.deb ...
Unpacking libcurl3-gnutls:amd64 (7.47.0-1ubuntu2.2) ...
Selecting previously unselected package libsasl2-modules:amd64.
Preparing to unpack .../libsasl2-modules_2.1.26.dfsg1-14build1_amd64.deb ...
Unpacking libsasl2-modules:amd64 (2.1.26.dfsg1-14build1) ...
Selecting previously unselected package curl.
Preparing to unpack .../curl_7.47.0-1ubuntu2.2_amd64.deb ...
Unpacking curl (7.47.0-1ubuntu2.2) ...
Processing triggers for libc-bin (2.23-0ubuntu5) ...
Setting up libffi6:amd64 (3.2.1-4) ...
Setting up libgmp10:amd64 (2:6.1.0+dfsg-2) ...
Setting up libnettle6:amd64 (3.2-1ubuntu0.16.04.1) ...
Setting up libhogweed4:amd64 (3.2-1ubuntu0.16.04.1) ...
Setting up libidn11:amd64 (1.32-3ubuntu1.1) ...
Setting up libp11-kit0:amd64 (0.23.2-5~ubuntu16.04.1) ...
Setting up libtasn1-6:amd64 (4.7-3ubuntu0.16.04.1) ...
Setting up libgnutls30:amd64 (3.4.10-4ubuntu1.2) ...
Setting up libsqlite3-0:amd64 (3.11.0-1ubuntu1) ...
Setting up libssl1.0.0:amd64 (1.0.2g-1ubuntu4.6) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/local/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up openssl (1.0.2g-1ubuntu4.6) ...
Setting up ca-certificates (20160104ubuntu1) ...
debconf: unable to initialize frontend: Dialog
debconf: (TERM is not set, so the dialog frontend is not usable.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.22.1 /usr/local/share/perl/5.22.1 /usr/lib/x86_64-linux-gnu/perl5/5.22 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl/5.22 /usr/share/perl/5.22 /usr/local/lib/site_perl /usr/lib/x86_64-linux-gnu/perl-base .) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up krb5-locales (1.13.2+dfsg-5ubuntu2) ...
Setting up libroken18-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libasn1-8-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libkrb5support0:amd64 (1.13.2+dfsg-5ubuntu2) ...
Setting up libk5crypto3:amd64 (1.13.2+dfsg-5ubuntu2) ...
Setting up libkeyutils1:amd64 (1.5.9-8ubuntu1) ...
Setting up libkrb5-3:amd64 (1.13.2+dfsg-5ubuntu2) ...
Setting up libgssapi-krb5-2:amd64 (1.13.2+dfsg-5ubuntu2) ...
Setting up libhcrypto4-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libheimbase1-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libwind0-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libhx509-5-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libkrb5-26-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libheimntlm0-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libgssapi3-heimdal:amd64 (1.7~git20150920+dfsg-4ubuntu1) ...
Setting up libsasl2-modules-db:amd64 (2.1.26.dfsg1-14build1) ...
Setting up libsasl2-2:amd64 (2.1.26.dfsg1-14build1) ...
Setting up libldap-2.4-2:amd64 (2.4.42+dfsg-2ubuntu3.1) ...
Setting up librtmp1:amd64 (2.4+20151223.gitfa8646d-1build1) ...
Setting up libcurl3-gnutls:amd64 (7.47.0-1ubuntu2.2) ...
Setting up libsasl2-modules:amd64 (2.1.26.dfsg1-14build1) ...
Setting up curl (7.47.0-1ubuntu2.2) ...
Processing triggers for libc-bin (2.23-0ubuntu5) ...
Processing triggers for ca-certificates (20160104ubuntu1) ...
Updating certificates in /etc/ssl/certs...
173 added, 0 removed; done.
Running hooks in /etc/ca-certificates/update.d...
done.
 ---> f29b04202a45
Removing intermediate container 3c4f82a1e1af
Successfully built f29b04202a45

$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
sudosuhas/myimg     1.1                 f29b04202a45        6 minutes ago       187 MB
sudosuhas/myimg     1.0                 6cd06ec4c835        31 minutes ago      187 MB
tomcat              7                   0f140c316816        2 days ago          356 MB
ubuntu              16.04               0ef2e08ed3fa        3 days ago          130 MB
hello-world         latest              48b5124b2768        6 weeks ago         1.84 kB
centos              7                   67591570dd29        2 months ago        192 MB
```

</details>

### Docker start
Starts one or more stopped containers

Cmd - `docker start [OPTIONS] CONTAINER [CONTAINER...]`

Documentation - https://docs.docker.com/engine/reference/commandline/start/

<details>
<summary>Example</summary>

```bash
$ sudo docker ps -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                           PORTS               NAMES
7168327142c9        nginx                     "nginx -g 'daemon ..."   19 seconds ago      Exited (0) 3 seconds ago                             dreamy_hugle

$ sudo docker start dreamy_hugle

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
7168327142c9        nginx               "nginx -g 'daemon ..."   2 minutes ago       Up 29 seconds       80/tcp, 443/tcp     dreamy_hugle
```

</details>

### Docker stop
Stops one or more running containers

Cmd - `docker stop [OPTIONS] CONTAINER [CONTAINER...]`

Documentation - https://docs.docker.com/engine/reference/commandline/stop/

<details>
<summary>Example</summary>

```bash
$ sudo docker run -d nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
693502eb7dfb: Already exists
6decb850d2bc: Pull complete
c3e19f087ed6: Pull complete
Digest: sha256:52a189e49c0c797cfc5cbfe578c68c225d160fb13a42954144b29af3fe4fe335
Status: Downloaded newer image for nginx:latest
949674a608258717306604871164423a5ca1bf3d7199a769ac589f52b4498c04

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
949674a60825        nginx               "nginx -g 'daemon ..."   28 seconds ago      Up 27 seconds       80/tcp, 443/tcp     elastic_wiles

$ sudo docker stop 949674a60825
949674a60825

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

$ sudo docker ps -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                           PORTS               NAMES
7168327142c9        nginx                     "nginx -g 'daemon ..."   19 seconds ago      Exited (0) 3 seconds ago                             dreamy_hugle

```

</details>

### Docker exec
Runs a command in a running container. Can be used to get terminal access
to a docker container running a process other than bash.
Exiting from the terminal will not terminate the container.

Cmd - `docker exec [OPTIONS] CONTAINER COMMAND [ARG...]`

Documentation - https://docs.docker.com/engine/reference/commandline/exec/

<details>
<summary>Example</summary>

```bash
$ sudo docker run -d tomcat:7
0d9b134d5c25e7595dce30451d1295774d24be28912f9b469fb68d4f5cdea34c

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
0d9b134d5c25        tomcat:7            "catalina.sh run"   9 seconds ago       Up 9 seconds        8080/tcp            determined_jepsen

$ sudo docker exec -it 0d9b134d5c25 bash

root@0d9b134d5c25:/usr/local/tomcat# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0 23 12:09 ?        00:00:07 /usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java -Djava.util.logging.config.file=/usr/loc
root        28     0  0 12:10 ?        00:00:00 bash
root        32    28  0 12:10 ?        00:00:00 ps -ef

root@0d9b134d5c25:/usr/local/tomcat# exit
exit

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED              STATUS              PORTS               NAMES
0d9b134d5c25        tomcat:7            "catalina.sh run"   About a minute ago   Up About a minute   8080/tcp            determined_jepsen

```

</details>

### Docker rm
Removes one or more containers using container ID or name. Can only delete containers that have been stopped.

Cmd - `docker rm [OPTIONS] CONTAINER [CONTAINER...]`

Documentation - https://docs.docker.com/engine/reference/commandline/rm/

<details>
<summary>Example</summary>

```bash
$ sudo docker ps -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                            PORTS               NAMES
0d9b134d5c25        tomcat:7                  "catalina.sh run"        4 minutes ago       Exited (143) About a minute ago                       determined_jepsen
7168327142c9        nginx                     "nginx -g 'daemon ..."   12 minutes ago      Exited (0) 9 minutes ago                              dreamy_hugle

$ sudo docker rm determined_jepsen
determined_jepsen

$ sudo docker ps -a
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS                           PORTS               NAMES
7168327142c9        nginx                     "nginx -g 'daemon ..."   14 minutes ago      Exited (0) 11 minutes ago                            dreamy_hugle
```

</details>

### Docker rmi
Removes one or more images. If an image is tagged multiple times, remove each tag.

Cmd - `docker rmi [OPTIONS] IMAGE [IMAGE...]`

Documentation - https://docs.docker.com/engine/reference/commandline/rmi/

<details>
<summary>Example</summary>

```bash
$ sudo docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
sudosuhas/entryping   1.0                 a7b89ac9eb56        32 minutes ago      174 MB
sudosuhas/ping        1.0                 38996ff9a9af        40 minutes ago      174 MB
sudosuhas/myimg       1.1                 f29b04202a45        About an hour ago   187 MB
sudosuhas/myimg       1.0                 6cd06ec4c835        About an hour ago   187 MB
tomcat                7                   0f140c316816        2 days ago          356 MB
nginx                 latest              6b914bbcb89e        2 days ago          182 MB
ubuntu                16.04               0ef2e08ed3fa        3 days ago          130 MB
hello-world           latest              48b5124b2768        6 weeks ago         1.84 kB
centos                7                   67591570dd29        2 months ago        192 MB

$ sudo docker rmi sudosuhas/myimg:1.0
Error response from daemon: conflict: unable to remove repository reference "sudosuhas/myimg:1.0" (must force) - container e361cd827d8c is using its referenced image 6cd06ec4c835

$ sudo docker rm e361cd827d8c
e361cd827d8c

$ sudo docker rmi sudosuhas/myimg:1.0
Untagged: sudosuhas/myimg:1.0
Deleted: sha256:6cd06ec4c83558b2d6c7e26f1e76f08a2bc4448712c3e6e4c156830d3ab2bec5
Deleted: sha256:cc0227527dc20ee51e227e5d3e6750a9b8d4e5b29d4e38bd520187285d619c23

$ sudo docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
sudosuhas/entryping   1.0                 a7b89ac9eb56        35 minutes ago      174 MB
sudosuhas/ping        1.0                 38996ff9a9af        43 minutes ago      174 MB
sudosuhas/myimg       1.1                 f29b04202a45        About an hour ago   187 MB
tomcat                7                   0f140c316816        2 days ago          356 MB
nginx                 latest              6b914bbcb89e        2 days ago          182 MB
ubuntu                16.04               0ef2e08ed3fa        3 days ago          130 MB
hello-world           latest              48b5124b2768        6 weeks ago         1.84 kB
centos                7                   67591570dd29        2 months ago        192 MB
```

</details>

### Docker push
Pushes an image or a repository to a registry.
Local repo must have same name and tag as the Docker Hub repo.

Cmd - `docker push [OPTIONS] NAME[:TAG]`

Documentation - https://docs.docker.com/engine/reference/commandline/push/

<details>
<summary>Example</summary>

```bash
$ sudo docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
sudosuhas/entryping   1.0                 a7b89ac9eb56        35 minutes ago      174 MB
sudosuhas/ping        1.0                 38996ff9a9af        43 minutes ago      174 MB
sudosuhas/myimg       1.1                 f29b04202a45        About an hour ago   187 MB
tomcat                7                   0f140c316816        2 days ago          356 MB
nginx                 latest              6b914bbcb89e        2 days ago          182 MB
ubuntu                16.04               0ef2e08ed3fa        3 days ago          130 MB
hello-world           latest              48b5124b2768        6 weeks ago         1.84 kB
centos                7                   67591570dd29        2 months ago        192 MB

$ sudo docker login --username=sudosuhas
Password:
Login Succeeded

$ sudo docker push sudosuhas/entryping:1.0
The push refers to a repository [docker.io/sudosuhas/entryping]
61eed8dbb6f1: Pushed
56827159aa8b: Pushed
440e02c3dcde: Pushed
29660d0e5bb2: Pushed
85782553e37a: Pushed
745f5be9952c: Pushed
1.0: digest: sha256:5c17b3a78fc3698224cdd8340eb065e67a3fdb31e2d28cfabf635b157a583578 size: 1569

# https://hub.docker.com/r/sudosuhas/entryping/tags/

```

</details>

### Docker tag
Create a tag `TARGET_IMAGE` that refers to `SOURCE_IMAGE`

Cmd - `docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]`

Documentation - https://docs.docker.com/engine/reference/commandline/tag/

<details>
<summary>Example</summary>

```bash
$ sudo docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
sudosuhas/entryping   1.0                 a7b89ac9eb56        35 minutes ago      174 MB
sudosuhas/ping        1.0                 38996ff9a9af        43 minutes ago      174 MB
sudosuhas/myimg       1.1                 f29b04202a45        About an hour ago   187 MB
tomcat                7                   0f140c316816        2 days ago          356 MB
nginx                 latest              6b914bbcb89e        2 days ago          182 MB
ubuntu                16.04               0ef2e08ed3fa        3 days ago          130 MB
hello-world           latest              48b5124b2768        6 weeks ago         1.84 kB
centos                7                   67591570dd29        2 months ago        192 MB

$ sudo docker tag f29b04202a45 sudosuhas/myubuntuimg:1.0

$ sudo docker tag sudosuhas/myimg:1.0 sudosuhas/myubuntuimg:1.0
```

</details>
