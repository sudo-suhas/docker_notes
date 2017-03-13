## Private Registry
Allows to use own registry instead of Docker Hub

### Push and Pull
The Docker image needs to be tagged with the host IP or domain of the registry server.

```bash
# Tag iimage and specify the registry host
$ docker tag <image id> myserver.com:5000/my-app:1.0

# Push image to registry
$ docker push myserver.com:5000/my-app:1.0

# Pull image from registry
$ docker pull myserver.com:5000/my-app:1.0
```

<details>
<summary>Example</summary>

```bash
# Not Production suitable. Needs configuration.
# Okay for testing and demo.
$ docker run -d -p 5001:5000 --name registry registry:2
c2f0e51a325ad45735e1401892abe6214b89176032ba2650276de29b3c19dd58

$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
c2f0e51a325a        registry:2          "/entrypoint.sh /e..."   55 seconds ago      Up 54 seconds       0.0.0.0:5001->5000/tcp   registry

$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
registry                2                   047218491f8c        2 days ago          33.2 MB
hello-world             latest              48b5124b2768        7 weeks ago         1.84 kB

$ docker tag 48b5124b2768 localhost:5001/myhello-world:1.0

$ docker images
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
registry                       2                   047218491f8c        2 days ago          33.2 MB
hello-world                    latest              48b5124b2768        7 weeks ago         1.84 kB
localhost:5001/myhello-world   1.0                 48b5124b2768        7 weeks ago         1.84 kB

$ docker push localhost:5001/myhello-world:1.0
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
# Configure docker daemon insecure HOSTS to allow non-TLS connection to registry
# DOCKER_OPTS="--insecure-registry <HOST IP/DOMAIN>:5001"
$ docker pull <HOST IP/DOMAIN>:5001/myhello-world:1.0
```
</details>

### Amazon EC2 Container Registry
[ECR](https://aws.amazon.com/ecr/) is probably a good option for using private registry
To use ECR, we need to
  - Setup `awscli` on host machines
  - Login into Docker using awscli

#### Setup awscli
AWS cli needs to be installed using pip and configured.

```bash
# If pip is not installed
$ sudo apt-get install python-pip

# This might throw an error for libyaml but works fine
$ pip install --upgrade --user awscli
```

**Add awscli to $PATH** <br>
Add `~/.local/bin` to `$PATH`. Add the line `export PATH=~/.local/bin:$PATH` to `~/.profile`. <br>
Run `source ~/.profile` to load the updated `$PATH`<br>
http://docs.aws.amazon.com/cli/latest/userguide/awscli-install-linux.html#awscli-install-linux-path

Reference - http://docs.aws.amazon.com/cli/latest/userguide/installing.html

**Configure `awscli`**  <br>
Run `aws configure` and add the access key, secret key and default region into the aws cli.

#### Login into Docker
Retrieve the docker login command that you can use to authenticate your Docker client to your registry:

```bash
$ aws ecr get-login --region ap-southeast-1
docker login -u AWS -p <SOME_REALLY_LONG_PASSWORD> -e none https://<REGISTRY ID>.dkr.ecr.ap-southeast-1.amazonaws.com

# Copy and run command
# -e option for email is deprecated and no longer needed
$ docker login -u AWS -p <SOME_REALLY_LONG_PASSWORD> https://<REGISTRY ID>.dkr.ecr.ap-southeast-1.amazonaws.com
Login succeeded

# Create new repository - http://docs.aws.amazon.com/cli/latest/reference/ecr/create-repository.html
$ aws ecr create-repository --repository-name project-name/docker-image-name

# Logout
$ docker logout https://<REGISTRY ID>.dkr.ecr.ap-southeast-1.amazonaws.com
```

#### Amazon ECR Docker Credential Helper
Docker login is valid only for 12 hours and a re-login is needed after that.
Since this is a pain to manage with CI/CD, it is easier to use credential helper.
This will use `~/.aws/credentials` file.

  - Clone the repo:
    ```bash
    $ mkdir ~/repos
    $ cd ~/repos
    $ git clone https://github.com/awslabs/amazon-ecr-credential-helper.git

    ```
  - The utility is written in GO and needs to be compiled on our machine.
    This can be done without installing GO by running `make docker` inside the cloned repo.
    But this needs docker-engine to be installed on the local machine.
    ```bash
    $ cd ~/repos/amazon-ecr-credential-helper/
    $ make docker
    docker run --rm \
            -e TARGET_GOOS= \
            -e TARGET_GOARCH= \
            -v /home/suhas.karanth/repos/amazon-ecr-credential-helper/bin:/go/src/github.com/awslabs/amazon-ecr-credential-helper/bin \
            sha256:30b17bb2c60585cc173a5b65c10adcccbf4960faf8a6272ac41986b6aca2a895
    . ./scripts/shared_env && ./scripts/build_binary.sh ./bin/local
    Built ecr-login

    ```
  - The binary will be available at `$PROJECT_ROOT/bin/local/docker-credential-ecr-login`
    which in our case would be `~/repos/amazon-ecr-credential-helper/bin/local/docker-credential-ecr-login`.
    This needs to be present on the `PATH`. If you already added `~/.local/bin` to the `PATH` while configuring
    `aws-cli`, then copying the binary to `~/.local/bin` will do the trick.
    ```bash
    $ cp ~/repos/amazon-ecr-credential-helper/bin/local/docker-credential-ecr-login ~/.local/bin/

    ```
  - Replace the contents of `~/.docker/config.json` with the following:
    ```json
    {
        "credsStore": "ecr-login"
    }

    ```
  - Test the `ecr-login` with a simple [describe repositories](http://docs.aws.amazon.com/cli/latest/reference/ecr/describe-repositories.html) cmd
    ```bash
    $ aws ecr describe-repositories

    {
        "repositories": [
            {
                "registryId": "<REGISTRY ID>",
                "repositoryName": "redbus",
                "repositoryArn": "arn:aws:ecr:ap-southeast-1:<REGISTRY ID>:repository/redbus",
                "createdAt": 1489140431.0,
                "repositoryUri": "<REGISTRY ID>.dkr.ecr.ap-southeast-1.amazonaws.com/redbus"
            },
            {
                "registryId": "<REGISTRY ID>",
                "repositoryName": "pilgrimages/odyssey",
                "repositoryArn": "arn:aws:ecr:ap-southeast-1:<REGISTRY ID>:repository/pilgrimages/odyssey",
                "createdAt": 1489154059.0,
                "repositoryUri": "<REGISTRY ID>.dkr.ecr.ap-southeast-1.amazonaws.com/pilgrimages/odyssey"
            }
        ]
    }

    ```

References:
  - https://aws.amazon.com/blogs/compute/authenticating-amazon-ecr-repositories-for-docker-cli-with-credential-helper/
  - http://docs.aws.amazon.com/cli/latest/reference/ecr/index.html#cli-aws-ecr
