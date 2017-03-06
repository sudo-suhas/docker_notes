## Security
  - Docker helps make applications safer as it provides a reduced set of default privileges and capabilities
  - Namespaces provide an isolated view of the system.
    Each container has its own IPC, network stack, root file system etc...
  - Processes running in one container cannot see and effect processes in another container
  - Control groups (Cgroups) isolate resourcce usage per container.
    This ensures that a compromised container won't bring down the entire host by exhausting resources

### Considerations
  - Docker daemon needs to run as root
  - Only ensure that trusted users can control the Docker daemon by managing the docker group
  - If binding the daemon to a TCP socket, secure it with TLS. Allows docker clients to connect to the daemon from remote host.
  - Use Linux hardening solution
    + Apparmor
    + SELinux
    + GRSEC

### Resources
https://docs.docker.com/engine/security/security/, <br>
https://www.slideshare.net/jpetazzo/docker-linux-containers-lxc-and-security
