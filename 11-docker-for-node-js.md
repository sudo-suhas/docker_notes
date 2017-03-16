## Docker for Node.js
Official images for Node.js are hosted here - https://github.com/nodejs/docker-node. <br>
A [best practices guide](https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md) is also included.

### Considerations
Some considerations for running node.js application using docker image
  - `node:alpine` variant is recommended for slim images.
  - Node.js was not designed to run as PID 1 which leads to unexpected behaviour when running inside of Docker.
    Use of a light weight init system, [tini](https://github.com/krallin/tini#using-tini) is recommended.
  - It is better to run the application as an unprivileged user.
    Official node.js images come with a `node` user which can be used for this.
    However, there are some caveats to this as ownership of files comes into play.
    Although there were several [issues](https://github.com/docker/docker/issues/6119) and [pull requests](https://github.com/docker/docker/pull/21088)
    created, Docker does not honor the `USER` for `COPY` command.
    This can be easily fixed with a `chown` command but that has [size implications](https://github.com/docker/docker/issues/6119#issuecomment-70606158)
    The workaround for this is demonstrated in the example Dockerfile below.
  - Docker allows configuration of memory and swap and should be used when running the container.
  - Installing npm packages is quite slow inside docker. There are few steps we can take to imporove the speed:
    + Copy the `package.json` *before* copying the source code and installing the modules to leverage docker caching.
      This way, the package installation layer only needs to be run when the `package.json` file is updated.
      Additionally, to have more predictable and consistent dependency tree, use `npm shrinkwrap --dev`
      to generate the `npm-shrinkwrap.json` file which ensures exact versions of modules are installed.
    + Set the registry with `npm config set registry http://registry.npmjs.org`.
    + Clean the cache after installation as it is of no use and only increases the docker image size.
  - Typically, logs for the application are stored in the $PROJECT_ROOT/logs.
    We would want to be able to inspect these if and when the app crashes or even while the app is running.
    We shouldn't have to connect to the shell terminal of the container to do so.
    It would also be ideal if the logs were to persist between subsequent builds and deployments.
    For this, we can use a volume. But care needs to be taken that there is no issue with permissions.
    For example, in the `docker-compose.yml` if we specify `/var/log/example-app:/home/node/example-app/logs`,
    if the folder /var/log/example-app does not exist, docker creates the folder as root!
    So when the app, running as non-root user tries to write to it:
    ```
    example_app_container | events.js:160
    example_app_container |       throw er; // Unhandled 'error' event
    example_app_container |       ^
    example_app_container |
    example_app_container | Error: EACCES: permission denied, open '/home/node/example-app/logs/webserver.out.log'
    example_app_container |     at Error (native)

    ```
    This can even crash the container! So to keep it simple, create a folder using the non-root user,
    give full permissions and then use it with docker volume.
    Typically, application logs are stored in /var/log.

## Example
The example covers building an image and running it on the remote server.
For running the image from Docker registry, we need to pull and start the container.
Another important thing that needs to be done is create and set appropriate permissions on the logs folder
which will be mounted on docker container.

```bash
$ mkdir $HOME/logs/example-app
$ chmod 777 $HOME/logs/example-app
```

So the steps are:
  - [Build and push the docker image to the registry](#build-image)
  - [Copy and place the docker compose file into the host machine.](#copy-docker-compose-file)
  - [Run Docker image on host](#run-docker-image)

### Build Image
A shell script is used to build the image with tags and push to the repository.
It can also extract static assets which would be generated during build.

The `.dockerignore` file can be largely similar to the `.gitignore` file but also ignore the following:
```
# These are not useful for the Docker image
.git
.gitignore
README.md
.vscode
.jsbeautifyrc
jsconfig.json
docker_build.sh
docker-compose.*yml

```

#### `docker_build.sh`
This script tags the image with both latest and application version.
If a private registry is being used, the user must be logged into Docker.

Additionally, it will clean up any images which no longer have any tags associated.

```bash
#!/bin/bash

set -e
# Any subsequent(*) commands which fail will cause the shell script to exit immediately

echo
echo "Extracting version and name from package.json"

APP_NAME="example_app"
REPO_URL='<PRIVATE_DOCKER_REGISTRY_URL>'
# TAG could be passed in as a parameter
# OR
# Read version from package.json using jq
# TAG=`jq -r '.version' package.json` <- Not OK. Invalidates npm cache when we increment version.
# OR
# Keep file with docker version - not very pretty
# TAG=$(head -n 1 .appversionrc | tr -d '\r') # Strip CRLF just in case. Screw you windows
# OR
# Use git commit for tagging - not user friendly
# TAG=$(git rev-parse --short HEAD)

echo
echo "Building docker image for app '${APP_NAME}'"

echo
echo "Tagging image with version '${TAG}'"

echo
docker build --pull --tag "$APP_NAME:$TAG" --tag "$APP_NAME:latest" .

echo
echo "Tagging images with Docker repository URL"
docker tag "$APP_NAME:$TAG" "$REPO_URL/$APP_NAME:$TAG"
docker tag "$APP_NAME:latest" "$REPO_URL/$APP_NAME:latest"

echo
echo "Creating temporary container to copy generated assets to HOST directory"
# Create the docker container for copying files(does not run)
CONTAINER_ID=$(docker create "$APP_NAME:latest")
# WORKSPACE is absolute path of the directory assigned to the build as a workspace. from JENKINS
docker cp $CONTAINER_ID:/home/node/example-app/dist/assets/. <DESTINATION FOLDER>/cdn-assets/
docker rm -v $CONTAINER_ID

echo
echo "Removing orphaned images"
if docker rmi $(docker images --filter "dangling=true" -q --no-trunc); then :; fi

# Push to registry
echo
echo "Pushing to registry"
docker push "$REPO_URL/$APP_NAME:$TAG"
docker push "$REPO_URL/$APP_NAME:latest"

```

#### `Dockerfile`
Notes inline

```Dockerfile
# Start off with the `node:6.10.0-alpine` image for keeping the size under check
FROM node:6.10.0-alpine

# Offical image has npm log verbosity as info. More info - https://github.com/nodejs/docker-node#verbosity
ENV NPM_CONFIG_LOGLEVEL warn

# We will use the `node` user which comes with the official node alpine image
ENV HOME /home/node


# Set our `WORKDIR` to /home/node/example-app
# Recommended by Dockerfile best practices - https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#workdir
WORKDIR $HOME/example-app


# Node.js process running as PID 1 will not respond to SIGTERM (CTRL-C) and similar signals
# https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md#handling-kernel-signals
# install lightweight init system
RUN apk add --update tini \
    # required to build node-zopfli used by webpack-compression-plugin
    # this step is quite costly(186 MB) so remove next line if not required
    && apk add --no-cache make gcc g++ python


# Add our package.json and install *before* adding our application files
# Files are copied as `root` user
COPY package.json npm-shrinkwrap.json $HOME/example-app/

# Change ownership to `node` user so that we need not change ownership of files generated by npm install
RUN chown -R node:node $HOME/*


# If we run the npm install as root, we'll have to also do chown.
# But that will double the contribution to size of docker from npm install
USER node

# Install npm dependencies into separate layer to leverage caching
RUN npm config set registry http://registry.npmjs.org \
    && npm install \
    # npm cache is of no use. So remove cache so that docker image size is small(er)
    && npm cache clean \
    && rm -rf /tmp/* \
    && rm -rf $HOME/.node-gyp


# Swtich back to `root` user because we need to copy and change ownership for source files
USER root

# Copy to tmp folder, change ownership and then copy to `WORKDIR` preserving the permissions(-p)
# This is done because if we copy directly to WORKDIR and then change permissions,
# we end up having to pay the cost for running the command for node_modules as well.
# Excluding `node_modules` using following command did not work either
# find $HOME/example-app -mindepth 1 -type -d -not -path '$HOME/example-app/node_modules' -print0 | xargs -0 chown node:node
COPY . /tmp/example-app
RUN chown -R node:node /tmp/example-app \
    # copy folder and preserve permissions
    && cp -rp /tmp/example-app $HOME \
    # remove files from tmp
    && rm -rf /tmp/example-app


# Switch back to `node` user and run postinstall, build
USER node

# Add before build for postinstall script.
# npm run postinstall
# We could ignore the exit code and keep it anyway by doing `npm run postinstall --silent || true`
# But this breaks docker caching for this command. So add only if required.
RUN npm run build


# Tini is installed at /sbin/tini
ENTRYPOINT ["/sbin/tini", "--"]

# Run program under Tini
# node best practices advices to avoid using npm start script - https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md#cmd
CMD ["node", "src/app.js"]

```

**Docker history output:**
```
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
example-app             0.1.0               2e34a7fd38ca        18 hours ago        360 MB
example-app             latest              2e34a7fd38ca        18 hours ago        360 MB

$ docker history 2e34a7fd38ca
IMAGE               CREATED             CREATED BY                                        SIZE
2e34a7fd38ca        18 hours ago        /bin/sh -c #(nop)  CMD ["node" "src/app.js"]      0 B
390b0bbfb1e3        18 hours ago        /bin/sh -c #(nop)  ENTRYPOINT ["/sbin/tini...     0 B
651f30d33339        18 hours ago        /bin/sh -c npm run build                          13.9 MB
fa681907e1e8        18 hours ago        /bin/sh -c #(nop)  USER [node]                    0 B
ef85d8493d1d        18 hours ago        /bin/sh -c chown -R node:node /tmp/example-app... 722 kB
bdbc0df14ca8        18 hours ago        /bin/sh -c #(nop) COPY dir:4b0aaece6449624...     722 kB
a6cc7a5a1593        18 hours ago        /bin/sh -c #(nop)  USER [root]                    0 B
df2e01b8a7be        18 hours ago        /bin/sh -c npm config set registry http://...     105 MB
5d0c25935beb        18 hours ago        /bin/sh -c #(nop)  USER [node]                    0 B
c13f2d367b05        18 hours ago        /bin/sh -c chown -R node:node $HOME/*             205 kB
38dbf1d12e58        18 hours ago        /bin/sh -c #(nop) COPY multi:14dbf9e77c89c...     205 kB
9ca830beb22e        18 hours ago        /bin/sh -c apk add --update tini     && ap...     186 MB
e1fcf66602ee        18 hours ago        /bin/sh -c #(nop) WORKDIR /home/node/example-app  0 B
6e3f1a0ad02f        18 hours ago        /bin/sh -c #(nop)  ENV HOME=/home/node            0 B
75cd82ef5bf5        18 hours ago        /bin/sh -c #(nop)  ENV NPM_CONFIG_LOGLEVEL...     0 B
8232a8b9c483        7 days ago          /bin/sh -c #(nop)  CMD ["node"]                   0 B
<missing>           7 days ago          /bin/sh -c apk add --no-cache --virtual .b...     3.56 MB
<missing>           7 days ago          /bin/sh -c #(nop)  ENV YARN_VERSION=0.21.3        0 B
<missing>           7 days ago          /bin/sh -c adduser -D -u 1000 node     && ...     45.4 MB
<missing>           7 days ago          /bin/sh -c #(nop)  ENV NODE_VERSION=6.10.0        0 B
<missing>           7 days ago          /bin/sh -c #(nop)  ENV NPM_CONFIG_LOGLEVEL...     0 B
<missing>           7 days ago          /bin/sh -c #(nop) ADD file:3df55c321c1c8d7...     4.81 MB

```

### Copy docker-compose File
This can be done using `rsync`. It will also create target directory if it does not exists.

```bash
rsync -aq --rsync-path="mkdir -p /home/dockeruser/dist/docker/example-app && rsync" \
	docker-compose.yml \
    -e 'ssh -i ssl-cert.pem' \
    dockeruser@my.docker.host:/home/dockeruser/dist/docker/example-app
```

### Run Docker image
SSH into the host
  - Stop container if running
  - Pull latest image and run it using `docker-compose`

Another important thing that needs to be done is create and set appropriate permissions on the logs folder
which will be mounted on docker container. This is a one time task.

```bash
ssh -i ssl-cert.pem \
	dockeruser@my.docker.host << EOF

set -e

source ~/.profile

cd ~/dist/docker/example-app

if docker stop example_app_container; then docker rm example_app_container; fi

docker-compose pull && docker-compose up -d

EOF
```

#### `docker-compose.yml`
This is used to specify `docker run` instructions in a easily readable, maintainable way.
It also maps volumes and connects to the docker isolated network(needs to be created prior).
The logs are written to a host folder but care needs to be taken that it has appropriate permissions.

```yml
version: '2.1'

services:
  example_app:
    image: "<PRIVATE_DOCKER_REGISTRY_URL>/example_app:latest"
    container_name: "example_app_container"
    volumes:
      - $HOME/logs/example-app:/home/node/example-app/logs
    restart: always
    environment:
      NODE_ENV: staging
      DEBUG: example_app:*,express:application,express:router:route,compression
    ports:
      - "8081:8085"
    mem_limit: "300M"
    memswap_limit: "1G"
    stop_grace_period: 30s

```

### Other resources
  - http://jdlm.info/articles/2016/03/06/lessons-building-node-app-docker.html
  - http://bitjudo.com/blog/2014/03/13/building-efficient-dockerfiles-node-dot-js/
