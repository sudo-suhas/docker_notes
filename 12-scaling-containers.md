## Scaling Containers
Having multiple instances of an application to increase availability and throughput is a common use case.
By default, the IPs and ports are private to the host and cannot be accessed externally unless they are bound to the host.
Binding the container to the hosts port can prevent multiple containers from running on the same host.
For example, only one container can bind to port 80 at a time.
So we need some kind of load balancing for the multiple docker instances which do the same thing.

  - One way would be to run multiple instances of the container with different port mappings
    and load balance on the host using something like nginx.
    But this would mean that we are limited to the initial configuration and to add additional instance,
    we would have to edit the nginx config, docker-compose file(if we're using that) and start the new container.
  - Another way is to use the network container [alias](https://docs.docker.com/compose/compose-file/compose-file-v2/#aliases)
    along with docker compose [scaling](https://docs.docker.com/compose/reference/scale/). <br>
    Example:
    ```yml
    version: '2.1'

    services:
      app:
        image: "example-app:latest"
        networks:
          isolated_nw:
            aliases:
              - example-app-alias
        restart: always
        ports:
          - "8080"
        mem_limit: "300M"
        memswap_limit: "1G"
        stop_grace_period: 30s

    networks:
      isolated_nw:
        external: true

    ```
    We do not specify the host port and a random port is selected.
    However, from within the network `isolated_nw`, we can access the app with `http://example-app-alias:8080`.
    [Theoretically](https://docs.docker.com/engine/userguide/networking/work-with-networks/#resolving-multiple-containers-to-a-single-alias),
    this should work with automatic load balancing but in practice, I found that the load balancing is not quite reliable and some instances are never hit.
    When I had 3 instances of a node.js app running with `docker-compose scale app=3`, the 1st instance was never hit.
  - Another way for solving this problem is explained in this [blog post](http://jasonwilder.com/blog/2014/03/25/automated-nginx-reverse-proxy-for-docker/).
    The `jwilder/nginx-proxy` Docker generates and dynamically updates the nginx config for load balancing various Docker app instances.
    These Docker instances can be spawned using `docker-compose scale app=3`.
    Although this has the additional cost of running nginx, the benefits outweigh the cost(at least for node.js).
    Load balancing with docker compose scaling can be achieved with the following:
    ```yml
    version: '2.1'

    services:
      nginx-proxy:
        image: jwilder/nginx-proxy:alpine
        container_name: example-app-nginx-proxy
        environment:
          DEFAULT_HOST: example-app.local
        ports:
          - "8080:80"
        volumes:
          - /var/run/docker.sock:/tmp/docker.sock:ro

      app:
        image: "example-app:latest"
        restart: always
        environment:
          NODE_ENV: production
          VIRTUAL_HOST: example-app.local
        ports:
          - "8081"
        mem_limit: "300M"
        memswap_limit: "1G"
        stop_grace_period: 30s

    networks:
      default:
        external:
          name: isolated_nw

    ```
    The environment variable `VIRTUAL_HOST` is used to identify the container by `nginx-proxy`.
    We specify `DEFAULT_HOST` for `nginx-proxy` because otherwise the requests need to have a header `Host: example-app.local`.
    This can be useful when we have different applications load balanced by the same `nginx-proxy` but for us, it is better to have a default.
    Within the network `isolated_nw`, the application can be accessed with `http://example-app.local`, which will connect on port 80 to nginx.
    From outside the network, the `nginx-proxy` is available on port 8080.
