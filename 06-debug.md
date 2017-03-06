## Debug
  - Use `service` command to start, stop or restart Docker daemon
  - While debugging, run Docker daemon with appropriate log level `sudo dockerd -D --log-level debug &`
  - To stop the docker daemon, use `sudo kill $(pidof dockerd)`

```bash
# Stop the docker daemon service
$ sudo service docker stop
docker stop/waiting

$ sudo dockerd -D --log-level debug &
[1] 3481
DEBU[0000] docker group found. gid: 999
DEBU[0000] Listener created for HTTP on unix (/var/run/docker.sock)
INFO[0000] libcontainerd: new containerd process, pid: 3486
DEBU[0000] containerd: read past events                  count=0
DEBU[0000] containerd: supervisor running                cpus=1 memory=3764 runtime=docker-runc runtimeArgs=[] stateDir=/var/run/docker/libcontainerd/containerd
DEBU[0000] containerd: grpc api on /var/run/docker/libcontainerd/docker-containerd.sock
DEBU[0000] libcontainerd: containerd health check returned error: rpc error: code = 14 desc = grpc: the connection is unavailable
DEBU[0001] libcontainerd: containerd health check returned error: rpc error: code = 14 desc = grpc: the connection is unavailable
DEBU[0001] Using default logging driver json-file
DEBU[0001] Golangs threads limit set to 53820
INFO[0001] [graphdriver] using prior storage driver: aufs
DEBU[0001] Using graph driver aufs
DEBU[0001] Max Concurrent Downloads: 3
DEBU[0001] Max Concurrent Uploads: 5
INFO[0001] Graph migration to content-addressability took 0.00 seconds
WARN[0001] Your kernel does not support swap memory limit
WARN[0001] Your kernel does not support cgroup rt period
WARN[0001] Your kernel does not support cgroup rt runtime
WARN[0001] mountpoint for pids not found
INFO[0001] Loading containers: start.
DEBU[0001] Loaded container 268b938a8899c15822e3ac58e8a65843aa1c58fa0255902f07ed07ed5b51741f
DEBU[0001] Loaded container 39ece6ede1b7271578dbdd90cb89b16cff5d398b6a52c838ce8f0af07836d372
DEBU[0001] Loaded container 4a1a9a9f4f509e68b7bb5591abb6af486fd20d252c8af62ed3480feb79e51450
DEBU[0001] Loaded container 4d2120150febe33531cf361323f9fe4829b63390c634bf7d430bfa2a96f84173
DEBU[0001] Loaded container 7168327142c95c54150a387e58ef463ca002f2aa9d2d5b49cbfdc5345aaa5811
DEBU[0001] Loaded container 761f0a6bf6429e02ec0560a545b93a01188f5488f75df568697ea46902a1c4c4
DEBU[0001] Loaded container 7a42922a197fde7d1bdc8391052fcb08f20234a0f68fbe3e18287efe03e54d02
DEBU[0001] Loaded container 87fec783fe5e527d323f430ff22b3616496047a102435a3bef9fc8d5d0734f72
DEBU[0001] Loaded container 881240cce4a34d7230983688aa54ea6a1940ba606a79ad5862058caeb8face5b
DEBU[0001] Loaded container 949674a608258717306604871164423a5ca1bf3d7199a769ac589f52b4498c04
DEBU[0001] Loaded container aa1c9d9328c9240fea0457028e959d722b2cb6552dcfe74e591db5658288a909
DEBU[0001] Loaded container acce37e17255405cd2faa7ecb202e541f713ee5c0e2681616eaaab0ab2995b1e
DEBU[0001] Loaded container cdfb8999e364aa501491a43a66c30a631fbf1c07d8ad4c152a1db227b63aea59
DEBU[0001] Loaded container f738c0ffd5a945f7f3cd9e551b5f3588bde7d82f6df762ae12d92d0c634d7b2c
DEBU[0001] Option Experimental: false
DEBU[0001] Option DefaultDriver: bridge
DEBU[0001] Option DefaultNetwork: bridge
INFO[0001] Firewalld running: false
DEBU[0001] /sbin/iptables, [--wait --version]
DEBU[0001] /sbin/iptables, [--wait -t nat -D PREROUTING -m addrtype --dst-type LOCAL -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -D OUTPUT -m addrtype --dst-type LOCAL ! --dst 127.0.0.0/8 -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -D OUTPUT -m addrtype --dst-type LOCAL -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -D PREROUTING]
DEBU[0001] /sbin/iptables, [--wait -t nat -D OUTPUT]
DEBU[0001] /sbin/iptables, [--wait -t nat -F DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -X DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -F DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -X DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -F DOCKER-ISOLATION]
DEBU[0001] /sbin/iptables, [--wait -t filter -X DOCKER-ISOLATION]
DEBU[0001] /sbin/iptables, [--wait -t nat -n -L DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -N DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -n -L DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -n -L DOCKER-ISOLATION]
DEBU[0001] /sbin/iptables, [--wait -t filter -C DOCKER-ISOLATION -j RETURN]
DEBU[0001] /sbin/iptables, [--wait -I DOCKER-ISOLATION -j RETURN]
DEBU[0001] /sbin/iptables, [--wait -t nat -C POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE]
DEBU[0001] /sbin/iptables, [--wait -t nat -C DOCKER -i docker0 -j RETURN]
DEBU[0001] /sbin/iptables, [--wait -t nat -I DOCKER -i docker0 -j RETURN]
DEBU[0001] /sbin/iptables, [--wait -D FORWARD -i docker0 -o docker0 -j DROP]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -i docker0 -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -i docker0 ! -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t nat -C PREROUTING -m addrtype --dst-type LOCAL -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -A PREROUTING -m addrtype --dst-type LOCAL -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -C OUTPUT -m addrtype --dst-type LOCAL -j DOCKER ! --dst 127.0.0.0/8]
DEBU[0001] /sbin/iptables, [--wait -t nat -A OUTPUT -m addrtype --dst-type LOCAL -j DOCKER ! --dst 127.0.0.0/8]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -o docker0 -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -o docker0 -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -j DOCKER-ISOLATION]
DEBU[0001] /sbin/iptables, [--wait -D FORWARD -j DOCKER-ISOLATION]
DEBU[0001] /sbin/iptables, [--wait -I FORWARD -j DOCKER-ISOLATION]
DEBU[0001] Network (c3afe1f) restored
DEBU[0001] Allocating IPv4 pools for network bridge (c3afe1f4c18e991e1013f4419ad40e6cdbdbf6d717d4e00a5df817d9ef585022)
DEBU[0001] RequestPool(LocalDefault, 172.17.0.0/16, , map[], false)
DEBU[0001] RequestAddress(LocalDefault/172.17.0.0/16, 172.17.0.1, map[RequestAddressType:com.docker.network.gateway])
DEBU[0001] /sbin/iptables, [--wait -t nat -C POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE]
DEBU[0001] /sbin/iptables, [--wait -t nat -D POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE]
DEBU[0001] /sbin/iptables, [--wait -t nat -C DOCKER -i docker0 -j RETURN]
DEBU[0001] /sbin/iptables, [--wait -t nat -D DOCKER -i docker0 -j RETURN]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -i docker0 -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -D FORWARD -i docker0 -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -i docker0 ! -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -D FORWARD -i docker0 ! -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -D FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -o docker0 -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -o docker0 -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -D FORWARD -o docker0 -j DOCKER]
DEBU[0001] releasing IPv4 pools from network bridge (c3afe1f4c18e991e1013f4419ad40e6cdbdbf6d717d4e00a5df817d9ef585022)
DEBU[0001] ReleaseAddress(LocalDefault/172.17.0.0/16, 172.17.0.1)
DEBU[0001] ReleasePool(LocalDefault/172.17.0.0/16)
INFO[0001] Default bridge (docker0) is assigned with an IP address 172.17.0.0/16. Daemon option --bip can be used to set a preferred IP address
DEBU[0001] Allocating IPv4 pools for network bridge (6de1e52e1c521aacb986cd6dc3bb904b7dac5a0f7e2f5f1d07127bda4c4857d1)
DEBU[0001] RequestPool(LocalDefault, 172.17.0.0/16, , map[], false)
DEBU[0001] RequestAddress(LocalDefault/172.17.0.0/16, 172.17.0.1, map[RequestAddressType:com.docker.network.gateway])
DEBU[0001] /sbin/iptables, [--wait -t nat -C POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE]
DEBU[0001] /sbin/iptables, [--wait -t nat -I POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE]
DEBU[0001] /sbin/iptables, [--wait -t nat -C DOCKER -i docker0 -j RETURN]
DEBU[0001] /sbin/iptables, [--wait -t nat -I DOCKER -i docker0 -j RETURN]
DEBU[0001] /sbin/iptables, [--wait -D FORWARD -i docker0 -o docker0 -j DROP]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -i docker0 -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -I FORWARD -i docker0 -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -i docker0 ! -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -I FORWARD -i docker0 ! -o docker0 -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -I FORWARD -o docker0 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT]
DEBU[0001] /sbin/iptables, [--wait -t nat -C PREROUTING -m addrtype --dst-type LOCAL -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -C PREROUTING -m addrtype --dst-type LOCAL -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t nat -C OUTPUT -m addrtype --dst-type LOCAL -j DOCKER ! --dst 127.0.0.0/8]
DEBU[0001] /sbin/iptables, [--wait -t nat -C OUTPUT -m addrtype --dst-type LOCAL -j DOCKER ! --dst 127.0.0.0/8]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -o docker0 -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -I FORWARD -o docker0 -j DOCKER]
DEBU[0001] /sbin/iptables, [--wait -t filter -C FORWARD -j DOCKER-ISOLATION]
DEBU[0001] /sbin/iptables, [--wait -D FORWARD -j DOCKER-ISOLATION]
DEBU[0001] /sbin/iptables, [--wait -I FORWARD -j DOCKER-ISOLATION]
INFO[0001] Loading containers: done.
INFO[0001] Daemon has completed initialization
INFO[0001] Docker daemon                                 commit=3a232c8 graphdriver=aufs version=17.03.0-ce
DEBU[0001] Registering routers
DEBU[0001] Registering GET, /containers/{name:.*}/checkpoints
DEBU[0001] Registering POST, /containers/{name:.*}/checkpoints
DEBU[0001] Registering DELETE, /containers/{name}/checkpoints/{checkpoint}
DEBU[0001] Registering HEAD, /containers/{name:.*}/archive
DEBU[0001] Registering GET, /containers/json
DEBU[0001] Registering GET, /containers/{name:.*}/export
DEBU[0001] Registering GET, /containers/{name:.*}/changes
DEBU[0001] Registering GET, /containers/{name:.*}/json
DEBU[0001] Registering GET, /containers/{name:.*}/top
DEBU[0001] Registering GET, /containers/{name:.*}/logs
DEBU[0001] Registering GET, /containers/{name:.*}/stats
DEBU[0001] Registering GET, /containers/{name:.*}/attach/ws
DEBU[0001] Registering GET, /exec/{id:.*}/json
DEBU[0001] Registering GET, /containers/{name:.*}/archive
DEBU[0001] Registering POST, /containers/create
DEBU[0001] Registering POST, /containers/{name:.*}/kill
DEBU[0001] Registering POST, /containers/{name:.*}/pause
DEBU[0001] Registering POST, /containers/{name:.*}/unpause
DEBU[0001] Registering POST, /containers/{name:.*}/restart
DEBU[0001] Registering POST, /containers/{name:.*}/start
DEBU[0001] Registering POST, /containers/{name:.*}/stop
DEBU[0001] Registering POST, /containers/{name:.*}/wait
DEBU[0001] Registering POST, /containers/{name:.*}/resize
DEBU[0001] Registering POST, /containers/{name:.*}/attach
DEBU[0001] Registering POST, /containers/{name:.*}/copy
DEBU[0001] Registering POST, /containers/{name:.*}/exec
DEBU[0001] Registering POST, /exec/{name:.*}/start
DEBU[0001] Registering POST, /exec/{name:.*}/resize
DEBU[0001] Registering POST, /containers/{name:.*}/rename
DEBU[0001] Registering POST, /containers/{name:.*}/update
DEBU[0001] Registering POST, /containers/prune
DEBU[0001] Registering PUT, /containers/{name:.*}/archive
DEBU[0001] Registering DELETE, /containers/{name:.*}
DEBU[0001] Registering GET, /images/json
DEBU[0001] Registering GET, /images/search
DEBU[0001] Registering GET, /images/get
DEBU[0001] Registering GET, /images/{name:.*}/get
DEBU[0001] Registering GET, /images/{name:.*}/history
DEBU[0001] Registering GET, /images/{name:.*}/json
DEBU[0001] Registering POST, /commit
DEBU[0001] Registering POST, /images/load
DEBU[0001] Registering POST, /images/create
DEBU[0001] Registering POST, /images/{name:.*}/push
DEBU[0001] Registering POST, /images/{name:.*}/tag
DEBU[0001] Registering POST, /images/prune
DEBU[0001] Registering DELETE, /images/{name:.*}
DEBU[0001] Registering OPTIONS, /{anyroute:.*}
DEBU[0001] Registering GET, /_ping
DEBU[0001] Registering GET, /events
DEBU[0001] Registering GET, /info
DEBU[0001] Registering GET, /version
DEBU[0001] Registering GET, /system/df
DEBU[0001] Registering POST, /auth
DEBU[0001] Registering GET, /volumes
DEBU[0001] Registering GET, /volumes/{name:.*}
DEBU[0001] Registering POST, /volumes/create
DEBU[0001] Registering POST, /volumes/prune
DEBU[0001] Registering DELETE, /volumes/{name:.*}
DEBU[0001] Registering POST, /build
DEBU[0001] Registering POST, /swarm/init
DEBU[0001] Registering POST, /swarm/join
DEBU[0001] Registering POST, /swarm/leave
DEBU[0001] Registering GET, /swarm
DEBU[0001] Registering GET, /swarm/unlockkey
DEBU[0001] Registering POST, /swarm/update
DEBU[0001] Registering POST, /swarm/unlock
DEBU[0001] Registering GET, /services
DEBU[0001] Registering GET, /services/{id}
DEBU[0001] Registering POST, /services/create
DEBU[0001] Registering POST, /services/{id}/update
DEBU[0001] Registering DELETE, /services/{id}
DEBU[0001] Registering GET, /services/{id}/logs
DEBU[0001] Registering GET, /nodes
DEBU[0001] Registering GET, /nodes/{id}
DEBU[0001] Registering DELETE, /nodes/{id}
DEBU[0001] Registering POST, /nodes/{id}/update
DEBU[0001] Registering GET, /tasks
DEBU[0001] Registering GET, /tasks/{id}
DEBU[0001] Registering GET, /secrets
DEBU[0001] Registering POST, /secrets/create
DEBU[0001] Registering DELETE, /secrets/{id}
DEBU[0001] Registering GET, /secrets/{id}
DEBU[0001] Registering POST, /secrets/{id}/update
DEBU[0001] Registering GET, /plugins
DEBU[0001] Registering GET, /plugins/{name:.*}/json
DEBU[0001] Registering GET, /plugins/privileges
DEBU[0001] Registering DELETE, /plugins/{name:.*}
DEBU[0001] Registering POST, /plugins/{name:.*}/enable
DEBU[0001] Registering POST, /plugins/{name:.*}/disable
DEBU[0001] Registering POST, /plugins/pull
DEBU[0001] Registering POST, /plugins/{name:.*}/push
DEBU[0001] Registering POST, /plugins/{name:.*}/upgrade
DEBU[0001] Registering POST, /plugins/{name:.*}/set
DEBU[0001] Registering POST, /plugins/create
DEBU[0001] Registering GET, /networks
DEBU[0001] Registering GET, /networks/
DEBU[0001] Registering GET, /networks/{id:.+}
DEBU[0001] Registering POST, /networks/create
DEBU[0001] Registering POST, /networks/{id:.*}/connect
DEBU[0001] Registering POST, /networks/{id:.*}/disconnect
DEBU[0001] Registering POST, /networks/prune
DEBU[0001] Registering DELETE, /networks/{id:.*}
INFO[0001] API listen on /var/run/docker.sock

$ sudo docker images
DEBU[0042] Calling GET /_ping
DEBU[0042] Calling GET /v1.26/images/json
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
sudosuhas/entryping     1.0                 a7b89ac9eb56        2 days ago          174 MB
sudosuhas/ping          1.0                 38996ff9a9af        2 days ago          174 MB
sudosuhas/myimg         1.1                 f29b04202a45        2 days ago          187 MB
sudosuhas/myubuntuimg   1.0                 f29b04202a45        2 days ago          187 MB
tomcat                  7                   0f140c316816        5 days ago          356 MB
nginx                   latest              6b914bbcb89e        5 days ago          182 MB
ubuntu                  16.04               0ef2e08ed3fa        6 days ago          130 MB
hello-world             latest              48b5124b2768        7 weeks ago         1.84 kB
centos                  7                   67591570dd29        2 months ago        192 MB

$ sudo docker ps
DEBU[0046] Calling GET /_ping
DEBU[0046] Calling GET /v1.26/containers/json
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES

$ sudo kill $(pidof dockerd)
INFO[0094] Processing signal 'terminated'
DEBU[0094] start clean shutdown of all containers with a 15 seconds timeout...
DEBU[0094] Cleaning up old mountid : start.
DEBU[0094] Cleaning up old mountid : done.
DEBU[0094] Clean shutdown succeeded
INFO[0094] stopping containerd after receiving terminated
DEBU[0094] Unix socket /run/docker/libnetwork/d83132430e305ee16b525cf156bf0a51f2950abd87ebba92bc8890e1a7e16295.sock doesnt exist. cannot accept client connections
DEBU[0094] libcontainerd: containerd health check returned error: rpc error: code = 9 desc = grpc: the client connection is closing

[1]+  Done                    sudo dockerd -D --log-level debug

$ sudo service docker start
```
