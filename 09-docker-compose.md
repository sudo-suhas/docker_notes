## Docker Compose
Docker Compose is a tool for creating and managing multi-continer applications
  - Containers are all defined in a single file called docker-compose.yml
  - Each container runs a particular component/service of your applicaiton.
  - Compose will spin up all containers in a single command

Compose has commands for managing the whole lifecycle of your application:
  - Start, stop and rebuild services
  - View the status of running services
  - Stream the log output of running services
  - Run a one-off command on a service

### Features
The features of Compose that make it effective are:
  - [Multiple isolated environments on a single host](#multiple-isolated-environments-on-a-single-host)
  - [Preserve volume data when containers are created](#preserve-volume-data-when-containers-are-created)
  - [Only recreate containers that have changed](#only-recreate-containers-that-have-changed)
  - [Variables and moving a composition between environments](#variables-and-moving-a-composition-between-environments)

Reference - https://docs.docker.com/compose/overview

#### Multiple isolated environments on a single host
The default project name is the basename of the project directory.
You can set a custom project name by using the `-p` command line option or
the `COMPOSE_PROJECT_NAME` environment variable.

#### Preserve volume data when containers are created
Compose preserves all volumes used by your services.
When `docker-compose` up runs, if it finds any containers from previous runs,
it copies the volumes from the old container to the new container.
This process ensures that any data you’ve created in volumes isn’t lost.

#### Only recreate containers that have changed
Compose caches the configuration used to create a container.
When you restart a service that has not changed, Compose re-uses the existing containers.
Re-using containers means that you can make changes to your environment very quickly.

#### Variables and moving a composition between environments
Compose supports variables in the Compose file.
You can use these variables to customize your composition for different environments, or different users.
See [Variable substitution](https://docs.docker.com/compose/compose-file/#variable-substitution) for more details.
You can extend a Compose file using the extends field or by creating multiple Compose files.
See [extends](https://docs.docker.com/compose/extends/) for more details.

### Installation(ubuntu)
  - Go to the [Compose repository release page on GitHub](https://github.com/docker/compose/releases).
  - Follow the instructions from the release page and run the curl command, which the release page specifies, in your terminal.
  `sudo -i` might be required before running the `curl` and `chmod` commands.
  - Installing Command Completion. Completion will be available upon next login.
    ```bash
    $ sudo curl -L https://raw.githubusercontent.com/docker/compose/$(docker-compose version --short)/contrib/completion/bash/docker-compose -o /etc/bash_completion.d/docker-compose
    ...

    $ docker-compose --version
    docker-compose version 1.11.2, build dfed245
    ```

### Configuring the Compose yml file
  - Defines the services that make up your application
  - Each serice contains instructions for building and running a container
  - **Build** defines the path to the Dockerfile that will be used to build the image.
    Container will be run using the image built
  - **Image** defines the image that will be used to run the container
  - All services must have either a build or image instruction

```yml
version: '2'
services:
  web:
    # Build image using Dockerfile in current directory
    build: .
    ports:
     - "5000:5000"
    volumes:
     - .:/code
  redis:
    # Use the latest redis(alpine) image from Docker hub
    image: "redis:alpine"

# Use pre-existing existing network
# docker-compose up will not attempt to create the network, and will raise an error if it doesn’t exist.
networks:
  default:
    external:
      name: my-pre-existing-network
```

### Running your application
  - Use the `docker-compose up` command. `-d` flag can be used to run it in the background.
  - It will build the image for each service, create and start the containers
