## Dockerfile
A Dockerfile is a configuration file that contains instructions for building a Docker image
  - Provides a more effective way to build images compared to using `docker commit`
  - Easily fits into continuous integration and deployment process

### Dockerfile Instructions
  - Instructions specify what to do when building the image
  - `FROM` instruction specifies what the base image should be
  - RUN
    + Instruction specifies a command to execute
    + Each `RUN` instruction will execute the command on the top writable layer and perform a commit on the image.
    + Can aggregate multiple `RUN` instructions by using `&&`
  - CMD
    + `CMD` defines a default command to execute when a container is created
    + `CMD` performs no action during the image build
    + Both shell format(ex: `ls -a`) and EXEC format(ex: `["ls", "-a"]`) are supported.
    + Can only be specified _once_ in a Dockerfile
    + **Can be overridden at run time**
    + If no `CMD` is specified in the Dockerfile and during the `run`, the `CMD` from base image would be used(bash terminal for ubuntu).
  - ENTRYPOINT
    + Defines the command that will run when a container is executed
    + Runtime arguments and `CMD` instruction are passed as parameters to the `ENTRYPOINT` instruction
    + Shell and EXEC form but EXEC form preferred as shell form cannot accept arguments at runtime
    + Container essentially runs as an executable
    + Cannot be overridden at runtime
  - VOLUME
    + Volume instruction creates a mount point
    + Can specify arguments JSON array or string
    + Cannot map volumes to host directories
    + Volumes are initialized when the container is executed
  - EXPOSE
    + Configures which ports a container will listen on at runtime
    + Ports still need to be mapped when container is executed

Example Dockerfile -

```Dockerfile
# Comments can be made using '#'
FROM ubuntu:16.04
RUN apt-get update && apt-get install -y curl

# Shell format
CMD ping 127.0.0.1 -c 30

#Exec format
CMD ["ping", "127.0.0.1", "-c", "30"]

ENTRYPOINT ["ping"]

VOLUME /myvol

# String, multiple
VOLUME /www/website1.com /www/website2.com

# JSON example
VOLUME ["myvol", "myvol2"]

EXPOSE 80 443
```


<details>
<summary>CMD example</summary>

```Dockerfile
FROM ubuntu:16.04
RUN apt-get update && apt-get install -y iputils-ping
CMD ["ping", "127.0.0.1", "-c", "10"]
```

```bash
$ docker build -t sudosuhas/ping:1.0 .
Sending build context to Docker daemon 2.048 kB
Step 1/3 : FROM ubuntu:16.04
 ---> 0ef2e08ed3fa
Step 2/3 : RUN apt-get update && apt-get install -y iputils-ping
 ---> Running in f29e1f8f0c8d
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
Fetched 24.9 MB in 8s (2777 kB/s)
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
The following additional packages will be installed:
  libffi6 libgmp10 libgnutls-openssl27 libgnutls30 libhogweed4 libidn11
  libnettle6 libp11-kit0 libtasn1-6
Suggested packages:
  gnutls-bin
The following NEW packages will be installed:
  iputils-ping libffi6 libgmp10 libgnutls-openssl27 libgnutls30 libhogweed4
  libidn11 libnettle6 libp11-kit0 libtasn1-6
0 upgraded, 10 newly installed, 0 to remove and 0 not upgraded.
Need to get 1303 kB of archives.
After this operation, 3778 kB of additional disk space will be used.
Get:1 http://archive.ubuntu.com/ubuntu xenial/main amd64 libgmp10 amd64 2:6.1.0+dfsg-2 [240 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libnettle6 amd64 3.2-1ubuntu0.16.04.1 [93.5 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libhogweed4 amd64 3.2-1ubuntu0.16.04.1 [136 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libidn11 amd64 1.32-3ubuntu1.1 [45.6 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial/main amd64 libffi6 amd64 3.2.1-4 [17.8 kB]
Get:6 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libp11-kit0 amd64 0.23.2-5~ubuntu16.04.1 [105 kB]
Get:7 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libtasn1-6 amd64 4.7-3ubuntu0.16.04.1 [43.2 kB]
Get:8 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libgnutls30 amd64 3.4.10-4ubuntu1.2 [547 kB]
Get:9 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 libgnutls-openssl27 amd64 3.4.10-4ubuntu1.2 [21.9 kB]
Get:10 http://archive.ubuntu.com/ubuntu xenial/main amd64 iputils-ping amd64 3:20121221-5ubuntu2 [52.7 kB]
debconf: delaying package configuration, since apt-utils is not installed
Fetched 1303 kB in 3s (365 kB/s)
Selecting previously unselected package libgmp10:amd64.
(Reading database ... 7256 files and directories currently installed.)
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
Selecting previously unselected package libffi6:amd64.
Preparing to unpack .../libffi6_3.2.1-4_amd64.deb ...
Unpacking libffi6:amd64 (3.2.1-4) ...
Selecting previously unselected package libp11-kit0:amd64.
Preparing to unpack .../libp11-kit0_0.23.2-5~ubuntu16.04.1_amd64.deb ...
Unpacking libp11-kit0:amd64 (0.23.2-5~ubuntu16.04.1) ...
Selecting previously unselected package libtasn1-6:amd64.
Preparing to unpack .../libtasn1-6_4.7-3ubuntu0.16.04.1_amd64.deb ...
Unpacking libtasn1-6:amd64 (4.7-3ubuntu0.16.04.1) ...
Selecting previously unselected package libgnutls30:amd64.
Preparing to unpack .../libgnutls30_3.4.10-4ubuntu1.2_amd64.deb ...
Unpacking libgnutls30:amd64 (3.4.10-4ubuntu1.2) ...
Selecting previously unselected package libgnutls-openssl27:amd64.
Preparing to unpack .../libgnutls-openssl27_3.4.10-4ubuntu1.2_amd64.deb ...
Unpacking libgnutls-openssl27:amd64 (3.4.10-4ubuntu1.2) ...
Selecting previously unselected package iputils-ping.
Preparing to unpack .../iputils-ping_3%3a20121221-5ubuntu2_amd64.deb ...
Unpacking iputils-ping (3:20121221-5ubuntu2) ...
Processing triggers for libc-bin (2.23-0ubuntu5) ...
Setting up libgmp10:amd64 (2:6.1.0+dfsg-2) ...
Setting up libnettle6:amd64 (3.2-1ubuntu0.16.04.1) ...
Setting up libhogweed4:amd64 (3.2-1ubuntu0.16.04.1) ...
Setting up libidn11:amd64 (1.32-3ubuntu1.1) ...
Setting up libffi6:amd64 (3.2.1-4) ...
Setting up libp11-kit0:amd64 (0.23.2-5~ubuntu16.04.1) ...
Setting up libtasn1-6:amd64 (4.7-3ubuntu0.16.04.1) ...
Setting up libgnutls30:amd64 (3.4.10-4ubuntu1.2) ...
Setting up libgnutls-openssl27:amd64 (3.4.10-4ubuntu1.2) ...
Setting up iputils-ping (3:20121221-5ubuntu2) ...
Setcap is not installed, falling back to setuid
Processing triggers for libc-bin (2.23-0ubuntu5) ...
 ---> 0e46e97d9607
Removing intermediate container f29e1f8f0c8d
Step 3/3 : CMD ping 127.0.0.1 -c 10
 ---> Running in 4a9e01030bc8
 ---> 38996ff9a9af
Removing intermediate container 4a9e01030bc8
Successfully built 38996ff9a9af

$ docker run sudosuhas/ping:1.0
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.026 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.043 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.040 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.041 ms
64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.040 ms
64 bytes from 127.0.0.1: icmp_seq=6 ttl=64 time=0.041 ms
64 bytes from 127.0.0.1: icmp_seq=7 ttl=64 time=0.040 ms
64 bytes from 127.0.0.1: icmp_seq=8 ttl=64 time=0.039 ms
64 bytes from 127.0.0.1: icmp_seq=9 ttl=64 time=0.033 ms
64 bytes from 127.0.0.1: icmp_seq=10 ttl=64 time=0.036 ms

--- 127.0.0.1 ping statistics ---
10 packets transmitted, 10 received, 0% packet loss, time 8999ms
rtt min/avg/max/mdev = 0.026/0.037/0.043/0.009 ms

$ docker run sudosuhas/ping:1.0 echo "hello world"
hello world
```

</details>


<details>
<summary>ENTRYPOINT example</summary>

```Dockerfile
FROM ubuntu:16.04
RUN apt-get update && apt-get install -y iputils-ping
ENTRYPOINT ["ping"]
```

```bash
$ docker build -t sudosuhas/entryping:1.0 .
Sending build context to Docker daemon 2.048 kB
Step 1/3 : FROM ubuntu:16.04
 ---> 0ef2e08ed3fa
Step 2/3 : RUN apt-get update && apt-get install -y iputils-ping
 ---> Using cache
 ---> 0e46e97d9607
Step 3/3 : ENTRYPOINT ping
 ---> Running in 5f9072de1a71
 ---> a7b89ac9eb56
Removing intermediate container 5f9072de1a71
Successfully built a7b89ac9eb56

$ docker run sudosuhas/entryping:1.0
Usage: ping [-aAbBdDfhLnOqrRUvV] [-c count] [-i interval] [-I interface]
            [-m mark] [-M pmtudisc_option] [-l preload] [-p pattern] [-Q tos]
            [-s packetsize] [-S sndbuf] [-t ttl] [-T timestamp_option]
            [-w deadline] [-W timeout] [hop1 ...] destination

$ docker run sudosuhas/entryping:1.0 127.0.0.1 -c 5
PING 127.0.0.1 (127.0.0.1) 56(84) bytes of data.
64 bytes from 127.0.0.1: icmp_seq=1 ttl=64 time=0.027 ms
64 bytes from 127.0.0.1: icmp_seq=2 ttl=64 time=0.034 ms
64 bytes from 127.0.0.1: icmp_seq=3 ttl=64 time=0.039 ms
64 bytes from 127.0.0.1: icmp_seq=4 ttl=64 time=0.033 ms
64 bytes from 127.0.0.1: icmp_seq=5 ttl=64 time=0.037 ms

--- 127.0.0.1 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 3999ms
rtt min/avg/max/mdev = 0.027/0.034/0.039/0.004 ms
```

</details>

<details>
<summary>EXPOSE example</summary>

```Dockerfile
FROM ubuntu:16.04
RUN apt-get update && \
  apt-get install -y nginx

EXPOSE 80 443

CMD ["nginx", "-g", "daemon off;"]
```

</details>
