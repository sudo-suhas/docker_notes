## Concepts

### Docker Client and Daemon
  - Client/server architecture
  - Client rakes user inputs and sends them to the daemon
  - Daemon build, runs and distributes containers
  - Client and daemon can run on same or different hosts

### Docker Images
  - Made up of filesystems layered over each other
  - Read only template used to create containers
  - Build by you or other Docker users. There are official images maintained by organisations.
  - Stored in Docker Hub or other managed registry
  - Image layers
    + Images are comprised of multiple layers
    + A layer is also just another image
    + Every image contains a base layer
    + Docker uses a copy on write system
    + Layers are read only
    + Container writable layer
      - Docker creates a top writable layer for containers
      - Parent images are read only
      - All changes are made at the writable layer
  - Image Tags
    + Image tags are specified by reposityry:tag
    + The same image may have multiple tags
    + The default tag is `latest`
    + Look up the repository on Docker Hub to see what tags are available. Example - https://hub.docker.com/r/library/nginx/tags/

### Docker Containers
  - Isolated application platform
  - Contains everything needed to run your application
  - Based on one or more images
  - Containers Processes
    + A container only runs as long as the process from specified `docker run` command is running
    + Command's process is always PID 1 inside the container
  - Container ID
    + Containers can be specified using their ID or name
    + Long ID and short ID
    + Short ID and name can be obtained using `docker ps` command to list containers
    + Long ID obtained by inspecting a container

### Registry and Repository
A repository consists of multiple images stored with tags inside a registry.
A registry can have multiple repositories.

### Docker Hub Repositories
  - Users can create theird own repositories on Docker Hub
  - Public and Private
  - Push local images to a repository

### Docker Orchestration
  - Docker Machine - provision Docker hosts and install Docker engine on them
  - Docker Swarm - cluster many engines and schedule containers
  - Docker Compose - create and manage multi-container applications
