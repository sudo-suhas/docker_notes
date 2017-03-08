## Container Communication
Docker enables us to run multiple containers on a single host.
Each continer has its own isolated scope. If we want the containers to be able to
communicate with each other, we should create and use a `docker network`.
For example, a Web application backed by a REST API server.
For communicating across hosts, it might be simpler to map ports on to the host.

Docker network commands are available through the Docker Engine CLI.
These commands are:
  - `docker network create`
  - `docker network connect`
  - `docker network ls`
  - `docker network rm`
  - `docker network disconnect`
  - `docker network inspect`

There are 2 types of Docker networks.
A `bridge` network resides on a single host running an instance of Docker Engine.
An `overlay` network can span multiple hosts running their own engines.
If you run docker network create and supply only a network name, it creates a `bridge` network for you.

Unlike an `overlay` network has some requirements for creation. Read more [here](https://docs.docker.com/engine/userguide/networking/work-with-networks/#create-networks).

### Create a network on host

It is highly recommended to use the --subnet option while creating a network.
If the --subnet is not specified, the docker daemon automatically
chooses and assigns a subnet for the network and it could overlap
with another subnet in your infrastructure that is not managed by docker.
Such overlaps can cause connectivity issues or failures when containers are connected to that network.

```bash
# --internal flag restricts external access to network
$ sudo docker network create --driver bridge --subnet 172.25.0.0/16 isolated_nw
1b885c8aa2ed91486313783bbd3fc0b5e59ae9fc30e4b6ade0c01f751b902e6e

$ sudo docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
502f7718841e        bridge              bridge              local
800397b59b95        host                host                local
1b885c8aa2ed        isolated_nw         bridge              local
2591db9cf83f        none                null                local

$ sudo docker network inspect 1b885c8aa2ed
[
    {
        "Name": "isolated_nw",
        "Id": "1b885c8aa2ed91486313783bbd3fc0b5e59ae9fc30e4b6ade0c01f751b902e6e",
        "Created": "2017-03-06T11:05:22.701660048Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.25.0.0/16"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]

$ sudo docker run -d -P --name redis --network isolated_nw redis
Unable to find image 'redis:latest' locally
latest: Pulling from library/redis
693502eb7dfb: Pull complete
338a71333959: Pull complete
83f12ff60ff1: Pull complete
4b7726832aec: Pull complete
19a7e34366a6: Pull complete
622732cddc34: Pull complete
3b281f2bcae3: Pull complete
Digest: sha256:4c8fb09e8d634ab823b1c125e64f0e1ceaf216025aa38283ea1b42997f1e8059
Status: Downloaded newer image for redis:latest
393b24ca462d3e6cc0caa73a040e8b4b6067fbefef58f30ec207fc511cf83811

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
393b24ca462d        redis               "docker-entrypoint..."   6 seconds ago       Up 5 seconds        0.0.0.0:1024->6379/tcp   redis

$ sudo docker network inspect 1b885c8aa2ed
[
    {
        "Name": "isolated_nw",
        "Id": "1b885c8aa2ed91486313783bbd3fc0b5e59ae9fc30e4b6ade0c01f751b902e6e",
        "Created": "2017-03-06T11:05:22.701660048Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.25.0.0/16"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Containers": {
            "393b24ca462d3e6cc0caa73a040e8b4b6067fbefef58f30ec207fc511cf83811": {
                "Name": "redis",
                "EndpointID": "003758a92cbc11b612159ecb118eaa645bf73b67c2ccb091e716fbf9750df60d",
                "MacAddress": "02:42:ac:19:00:02",
                "IPv4Address": "172.25.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

# Run ubuntu image and connect to the user created network
$ sudo docker run -it --network isolated_nw ubuntu:16.04 bash

root@d69774133e1c:/$ apt-get update && apt-get install -y iputils-ping
...

root@d69774133e1c:/$ ping -w 4 redis
PING redis (172.25.0.2) 56(84) bytes of data.
64 bytes from redis.isolated_nw (172.25.0.2): icmp_seq=1 ttl=64 time=0.083 ms
64 bytes from redis.isolated_nw (172.25.0.2): icmp_seq=2 ttl=64 time=0.054 ms
64 bytes from redis.isolated_nw (172.25.0.2): icmp_seq=3 ttl=64 time=0.056 ms
64 bytes from redis.isolated_nw (172.25.0.2): icmp_seq=4 ttl=64 time=0.048 ms
64 bytes from redis.isolated_nw (172.25.0.2): icmp_seq=5 ttl=64 time=0.048 ms

--- redis ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 3999ms
rtt min/avg/max/mdev = 0.048/0.057/0.083/0.016 ms

root@d69774133e1c:/$ apt-get install redis-tools

# Connect to the named redis container using redis-cli
root@d69774133e1c:/$ redis-cli -h redis
redis:6379> set test 1
OK
redis:6379> exit

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
393b24ca462d        redis               "docker-entrypoint..."   11 minutes ago      Up 11 minutes       0.0.0.0:1024->6379/tcp   redis

# Connect to the redis container and get a bash terminal
$ sudo docker exec -it 393b24ca462d bash
root@393b24ca462d:/data# redis-cli
# Check if the value we set into 'test' is there
127.0.0.1:6379> get test
"1"
127.0.0.1:6379> exit
```
