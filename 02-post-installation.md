## Post-installation

### Manage Docker as a non-root user
It is possible to use docker as a non-root user but this requires
granting privileges equivalent to the root user.
Extreme caution is advised while adding users to the docker group.

```bash
# Create the docker group.
$ sudo groupadd docker

# Add your user to the docker group.
$ sudo usermod -aG docker $USER

# Log out and log back in so that your group membership is re-evaluated.
# Verify that you can docker commands without sudo.
$ docker run hello-world
```

### Configure Docker to start on boot

#### `systemd` - Ubuntu 16.04 and higher

```bash
# enable
$ sudo systemctl enable docker

# disable
$ sudo systemctl disable docker

```

#### `upstart` - Ubuntu 14.10 and lower
Docker is automatically configured to start on boot using upstart

```bash
# disable start on boot
$ echo manual | sudo tee /etc/init/docker.override
```

Docker Resource - https://docs.docker.com/engine/installation/linux/linux-postinstall/
