## Docker for Node.js
Official images for Node.js are hosted here - https://github.com/nodejs/docker-node
A [best practices guide](https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md) is also included.

### Considerations
Some considerations for running node.js application using docker image
  - `node:alpine` variant is recommended for slim images
  - Node.js was not designed to run as PID 1 which leads to unexpected behaviour when running inside of Docker.
    Use of a light weight init system, [tini](https://github.com/krallin/tini#using-tini) is recommended.
  - It is better to run the application as an unprivileged user.
    Official node.js images come with a `node` user which can be used for this.
    However, there are some caveats to this as ownership of files comes into play.
    Although there were several [issues](https://github.com/docker/docker/issues/6119) and [pull requests](https://github.com/docker/docker/pull/21088)
    created, Docker does not honor the `USER` for `COPY` command.
    This can be easily fixed with a `chown` command but that has [size implications](https://github.com/docker/docker/issues/6119#issuecomment-70606158)
    The workaround for this is demonstrated in the example Dockerfile below.
  - Docker allows configuration of memory and swap and should be used when running the container
  - Installing npm packages is quite slow inside docker. There are few steps we can take to imporove the speed:
    + Copy the `package.json` *before* copying the source code and installing the modules to leverage docker caching.
      This way, the package installation layer only needs to be run when the `package.json` file is updated
    + Set the registry with `npm config set registry http://registry.npmjs.org`
    + Clean the cache after installation as it is of no use and only increases the docker image size
  - Typically, logs for the application are stored in the $PROJECT_ROOT/logs.
    We would want to be able to inspect these if and when the app crashes or even while the app is running.
    We shouldn't have to connect to the shell terminal of the container to do so.
    It would also be ideal if the logs were to persist between subsequent builds and deployments.
    For this, we can use a volume

### Example
The example covers building an image using `docker-compose`.
The image is automatically tagged with the version from the `package.json` file.
We use a simple shell script for picking the version from `package.json` and passing it to docker-compose.

The `.dockerignore` file can be largely similar to the `.gitignore` file but also ignore the following:
```
# These are not useful for the Docker image
.git
.gitignore
README.md
.vscode
.jsbeautifyrc
jsconfig.json

```

#### docker_build.sh
This script tags the image with both latest and application version.
It could just as easily pick the docker user from either the `package.json` or from the environment.

If `jq`, used to parse and extract values from `json` files, is not installed,
run `sudo apt-get update && sudo apt-get install jq`

```bash
#!/bin/bash

echo
echo "Extracting version and name from package.json"

APP_NAME=`jq -r '.name' package.json`
DOCKER_IMAGE_NAME="$APP_NAME"_img
TAG=`jq -r '.version' package.json`

echo
echo "Building docker image for app '${APP_NAME}'"

echo
echo "Tagging image with version '${TAG}'"

export APP_NAME=$APP_NAME
export TAG=$TAG

echo
docker-compose build

echo
echo "Tagging image ${DOCKER_IMAGE_NAME} with 'latest' tag"

docker tag $DOCKER_IMAGE_NAME:"$TAG" $DOCKER_IMAGE_NAME:latest

```

#### docker-compose.yml
This file is used for build. It also has some instructions not relevant to build like
`environment`, `mem_limit` and `stop_grace_period` but that can be useful for running the application
or at least as a reference for writing the `docker-compose.prod.yml`.

The build tags the image with environment variable `$TAG` which is passed in using the shell script above.

```yml
version: '2.1'

services:
  odyssey:
    image: "${APP_NAME}_img:${TAG}"
    container_name: "${APP_NAME}_container"
    build:
      context: .
      args:
        - APP_NAME
    restart: always
    environment:
      NODE_ENV: staging
      DEBUG: odyssey:*,express:application,express:router:route,compression
    ports:
      - "8085:8085"
    mem_limit: "300M"
    memswap_limit: "1G"
    stop_grace_period: 30s

```

#### Dockerfile
Notes inline

```Dockerfile
# Start off with the `node:6.10.0-alpine` image for keeping the size under check
FROM node:6.10.0-alpine


# `ARG APP_NAME` is picked up by the shell script and then passed as argument by `docker-compose.yml`
ARG APP_NAME

# Offical image has npm log verbosity as info. More info - https://github.com/nodejs/docker-node#verbosity
ENV NPM_CONFIG_LOGLEVEL warn

# We will use the `node` user which comes with the official node alpine image
ENV HOME /home/node


# Set our `WORKDIR` to /home/node/$APP_NAME
# Recommended by Dockerfile best practices - https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#workdir
WORKDIR $HOME/$APP_NAME


# Node.js process running as PID 1 will not respond to SIGTERM (CTRL-C) and similar signals
# https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md#handling-kernel-signals
# install lightweight init system
RUN apk add --update tini \
    # required to build node-zopfli used by webpack-compression-plugin
    # this step is quite costly(186 MB) so remove next line if not required
    && apk add --no-cache make gcc g++ python


# Add our package.json and install *before* adding our application files
# Files are copied as `root` user
COPY package.json npm-shrinkwrap.json $HOME/$APP_NAME/

# Change ownership to `node` user so that we need not change ownership of files generated by npm install
RUN chown -R node:node $HOME/*


# If we run the npm install as root, we'll have to also do chown.
# But that will double the contribution to size of docker from npm install
USER node

# Install npm dependencies into separate layer to leverage caching
RUN npm config set registry http://registry.npmjs.org \
    && npm install \
    # npm cache is of no use. So remove cache so that docker image size is small(er)
    && npm cache clean

# Swtich back to `root` user because we need to copy and change ownership for source files
USER root

# Copy to tmp folder, change ownership and then copy to `WORKDIR` preserving the permissions(-p)
# This is done because if we copy directly to WORKDIR and then change permissions,
# we end up having to pay the cost for running the command for node_modules as well.
# Excluding `node_modules` using following command did not work wither
# find $HOME/$APP_NAME -mindepth 1 -type -d -not -path '$HOME/$APP_NAME/node_modules' -print0 | xargs -0 chown node:node
COPY . /tmp/$APP_NAME
RUN chown -R node:node /tmp/$APP_NAME \
    # copy folder and preserve permissions
    && cp -rp /tmp/$APP_NAME $HOME \
    # remove files from tmp
    && rm -rf /tmp/$APP_NAME


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
REPOSITORY           TAG                 IMAGE ID            CREATED             SIZE
odyssey_img          0.1.0               3f8f247ab3ca        59 minutes ago      363 MB
odyssey_img          latest              3f8f247ab3ca        59 minutes ago      363 MB

$ docker history 3f8f247ab3ca
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
3f8f247ab3ca        35 minutes ago      /bin/sh -c #(nop)  CMD ["node" "src/app.js"]    0 B
d850df5a8b0c        35 minutes ago      /bin/sh -c #(nop)  ENTRYPOINT ["/sbin/tini...   0 B
ff18893314ce        35 minutes ago      |1 APP_NAME=odyssey /bin/sh -c npm run build    13.9 MB
f241578b1967        38 minutes ago      /bin/sh -c #(nop)  USER [node]                  0 B
5bc929147148        38 minutes ago      |1 APP_NAME=odyssey /bin/sh -c chown -R no...   723 kB
4e9532166f54        38 minutes ago      /bin/sh -c #(nop) COPY dir:bb430e26887430c...   723 kB
4e11ec29991b        38 minutes ago      /bin/sh -c #(nop)  USER [root]                  0 B
cfddf3b1c664        38 minutes ago      |1 APP_NAME=odyssey /bin/sh -c npm config ...   108 MB
fe91ba8191f1        3 hours ago         /bin/sh -c #(nop)  USER [node]                  0 B
94e255693b04        3 hours ago         |1 APP_NAME=odyssey /bin/sh -c chown -R no...   205 kB
aa885af17164        3 hours ago         /bin/sh -c #(nop) COPY multi:84d05d11e4602...   205 kB
733427687063        3 hours ago         |1 APP_NAME=odyssey /bin/sh -c apk add --u...   186 MB
930f5736bc33        3 hours ago         /bin/sh -c #(nop) WORKDIR /home/node/odyssey    0 B
50c47b4dbc95        3 hours ago         /bin/sh -c #(nop)  ENV HOME=/home/node          0 B
6df28b0b7055        3 hours ago         /bin/sh -c #(nop)  ENV NPM_CONFIG_LOGLEVEL...   0 B
aaee5a37ad85        3 hours ago         /bin/sh -c #(nop)  ARG APP_NAME                 0 B
8232a8b9c483        4 days ago          /bin/sh -c #(nop)  CMD ["node"]                 0 B
<missing>           4 days ago          /bin/sh -c apk add --no-cache --virtual .b...   3.56 MB
<missing>           4 days ago          /bin/sh -c #(nop)  ENV YARN_VERSION=0.21.3      0 B
<missing>           4 days ago          /bin/sh -c adduser -D -u 1000 node     && ...   45.4 MB
<missing>           4 days ago          /bin/sh -c #(nop)  ENV NODE_VERSION=6.10.0      0 B
<missing>           4 days ago          /bin/sh -c #(nop)  ENV NPM_CONFIG_LOGLEVEL...   0 B
<missing>           4 days ago          /bin/sh -c #(nop) ADD file:3df55c321c1c8d7...   4.81 MB
```
