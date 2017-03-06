## Private Registry
Allows to use own registry instead of Docker Hub

### Push and Pull
The Docker image needs to be tagged with the host IP or domain of the registry server.

```bash
# Tag iimage and specify the registry host
$ sudo docker tag <image id> myserver.com:5000/my-app:1.0

# Push image to registry
$ sudo docker push myserver.com:5000/my-app:1.0

# Pull image from registry
$ sudo docker pull myserver.com:5000/my-app:1.0
```

<details>
<summary>Example</summary>

```bash
# Not Production suitable. Needs configuration.
# Okay for testing and demo.
$ sudo docker run -d -p 5001:5000 --name registry registry:2
c2f0e51a325ad45735e1401892abe6214b89176032ba2650276de29b3c19dd58

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
c2f0e51a325a        registry:2          "/entrypoint.sh /e..."   55 seconds ago      Up 54 seconds       0.0.0.0:5001->5000/tcp   registry

$ sudo docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
registry                2                   047218491f8c        2 days ago          33.2 MB
hello-world             latest              48b5124b2768        7 weeks ago         1.84 kB

$ sudo docker tag 48b5124b2768 localhost:5001/myhello-world:1.0

$ sudo docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
registry                       2                   047218491f8c        2 days ago          33.2 MB
hello-world                    latest              48b5124b2768        7 weeks ago         1.84 kB
localhost:5001/myhello-world   1.0                 48b5124b2768        7 weeks ago         1.84 kB

$ sudo docker push localhost:5001/myhello-world:1.0
The push refers to a repository [localhost:5001/myhello-world]
98c944e98de8: Pushed
1.0: digest: sha256:2075ac87b043415d35bb6351b4a59df19b8ad154e578f7048335feeb02d0f759 size: 524

$ curl -v -X GET http://localhost:5001/v2/myhello-world/tags/list
* Hostname was NOT found in DNS cache
*   Trying 127.0.0.1...
* Connected to localhost (127.0.0.1) port 5001 (#0)
> GET /v2/myhello-world/tags/list HTTP/1.1
> User-Agent: curl/7.35.0
> Host: localhost:5001
> Accept: */*
>
< HTTP/1.1 200 OK
< Content-Type: application/json; charset=utf-8
< Docker-Distribution-Api-Version: registry/2.0
< X-Content-Type-Options: nosniff
< Date: Mon, 06 Mar 2017 07:19:28 GMT
< Content-Length: 40
<
{"name":"myhello-world","tags":["1.0"]}
* Connection #0 to host localhost left intact

# On a different HOST
# Only for demo. Not Production safe.
# Configure docker daemon's insecure HOSTS to allow non-TLS connection to registry
# DOCKER_OPTS="--insecure-registry <HOST IP/DOMAIN>:5001"
$ docker pull <HOST IP/DOMAIN>:5001/myhello-world:1.0
```
</details>
