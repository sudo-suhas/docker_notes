## Installation
Can be done with `curl`, `wget` or `apt-get`. `apt-get` is preferred.

```bash

# Older versions of Docker were called docker or docker-engine. If these are installed, uninstall them
$ sudo apt-get remove docker docker-engine

# Update the apt package index
$ sudo apt-get update

# Install the linux-image-extra-* packages, which allow Docker to use the aufs storage drivers.
$ sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual

#-- Set up the Docker repository--

# Install packages to allow apt to use a repository over HTTPS
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common \
    jq

# Add Docker's official GPG key
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Set up the stable repository
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

# Update the apt package index
$ sudo apt-get update

# Install the latest version of Docker
$ sudo apt-get install docker-ce
```

Docker Resource - https://docs.docker.com/engine/installation/linux/ubuntu/

For Other OS - https://docs.docker.com/engine/installation/#platform-support-matrix
