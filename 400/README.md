# 400 - Get & Install

**HIGHLY RECOMMENDED READING**: "Docker and Proxy" at https://medium.com/@bennyh/docker-and-proxy-88148a3f35f7

## 100 - Get

Use the install script to grab the latest release of the k3d CLI:

```
$ curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash
Preparing to install k3d into /usr/local/bin
[sudo] password for cloud_user: 
k3d installed into /usr/local/bin/k3d
Run 'k3d --help' to see what you can do with it.
```

NOTE: If for some reason you cannot download above script (e.g. you are behind a corporate proxy), you can try the version of the ```install.sh``` script found at the root of this directory. Make sure it is executable, then run it as follows:

```
$ cd /
$ chmod +x install.sh
$ sudo ./install.sh
```

If above times out, you may still not have enough credentials to go through the proxy.

Alternatively, use the k3d CLI binary from the root of this repository (note: original can be found at https://github.com/k3d-io/k3d/releases), k3d-v5-linux-amd64.

```
$ sudo chmod +x k3d-v5-linux-amd64
$ sudo chown root k3d-v5-linux-amd64
$ sudo chgrp root k3d-v5-linux-amd64
$ sudo install k3d-v5-linux-amd64 /usr/local/bin/k3d
```

**Note**: You can check you linux architecture type with ```uname -a```. Here we are targeting k3d-linux-amd64, because the 64-Bit processor of Intel originally came from a company called Advanced Micro Devices (hence, AMD).

Once installed the k3d CLI binary, the following can be tested:

```
$ cd /usr/local/bin
$ sudo k3d --version
$ k3d version v5.4.4
$ k3s version v1.23.8-k3s1 (default)
```

As you are excuting this k3d CLI binary as root and it is owned by root, and of the root group, the following should also work:

```
$ cd ~
$ sudo k3d --version
$ k3d version v5.4.4
$ k3s version v1.23.8-k3s1 (default)
```

Hooray, you are ready for the next part!

## 200 - Install
Installation is very easy and available through many installers: wget, curl, Homebrew, Aur, … and supports all well known OSes (linux, darwin, windows) and processor architectures (386, amd64) !

### Several installation options

You have several installation options there:

- use the install script to grab the latest release:
  - wget: `wget -q -O - https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash`
  - curl: `curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash`
- use the install script to grab a specific release (via `TAG` environment variable):
  - wget: `wget -q -O - https://raw.githubusercontent.com/rancher/k3d/main/install.sh | TAG=v4.0.0 bash`
  - curl: `curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | TAG=v4.0.0 bash`

- use [Homebrew](https://brew.sh): `brew install k3d` (Homebrew is available for MacOS and Linux)
  - Formula can be found in [homebrew/homebrew-core](https://github.com/Homebrew/homebrew-core/blob/master/Formula/k3d.rb) and is mirrored to [homebrew/linuxbrew-core](https://github.com/Homebrew/linuxbrew-core/blob/master/Formula/k3d.rb)
- install via [MacPorts](https://www.macports.org): `sudo port selfupdate && sudo port install k3d` (MacPorts is available for MacOS)
- install via [AUR](https://aur.archlinux.org/) package [rancher-k3d-bin](https://aur.archlinux.org/packages/rancher-k3d-bin/): `yay -S rancher-k3d-bin`
- grab a release from the [release tab](https://github.com/rancher/k3d/releases) and install it yourself.
- install via go: `go install github.com/rancher/k3d` (**Note**: this will give you unreleased/bleeding-edge changes)
- use [Chocolatey](https://chocolatey.org/): `choco install k3d` (Chocolatey package manager is available for Windows)
  - package source can be found in [erwinkersten/chocolatey-packages](https://github.com/erwinkersten/chocolatey-packages/tree/master/automatic/k3d)

or...

Note that you only need to install the k3d client, which will create a k3s cluster using the right Docker image.

Once installed, configure the completion with your preferred shell (bash, zsh, powershell), for instance with zsh:

k3d completion zsh > ~/.zsh/completions/_k3d
 source .zshrc

Check installation with:

```
$ k3d --help
https://k3d.io/
k3d is a wrapper CLI that helps you to easily create k3s clusters inside docker.
Nodes of a k3d cluster are docker containers running a k3s image.
All Nodes of a k3d cluster are part of the same docker network.

Usage:
  k3d [flags]
  k3d [command]

Available Commands:
  cluster      Manage cluster(s)
  completion   Generate completion scripts for [bash, zsh, fish, powershell | psh]
  config       Work with config file(s)
  help         Help about any command
  image        Handle container images.
  kubeconfig   Manage kubeconfig(s)
  node         Manage node(s)
  registry     Manage registry/registries
  version      Show k3d and default k3s version

Flags:
  -h, --help         help for k3d
      --timestamps   Enable Log timestamps
      --trace        Enable super verbose output (trace logging)
      --verbose      Enable verbose output (debug logging)
      --version      Show k3d and default k3s version

Use "k3d [command] --help" for more information about a command.
```

### Setting up a not k3d-managed local registry

Based on "Setting up local k8s dev environment with k3d and skaffold" at https://devopsspiral.com/articles/k8s/k3d-skaffold/

See also "Docker Local Registry Setup | Docker Tutorial for Beginners" at https://www.youtube.com/watch?v=O_NMIZJ1qvw

**WARNING**: Although k3d since version 5 includes a local registry of its own, we have to set up a *Docker local registry* to provide the Docker images (```k3d-proxy``` and ```k3d-tools```) for k3d to get installed itself.

```
$ cd k3d-headstart
$ docker volume create local_registry
local_registry
```

Next, following https://k3d.io/v5.4.4/usage/registries/:

```
$ docker container run -d --name k3d-registry.localhost -v ${PWD}/local_registry:/var/lib/registry --restart always -p 5000:5000 my-docker-shared-images.my-artifactory.my-company.com/registry:2
Unable to find image 'my-docker-shared-images.my-artifactory.my-company.com/registry:2' locally
2: Pulling from registry
530afca65e2e: Pull complete
d450d4da0343: Pull complete
96277bea17b6: Pull complete
470ad04e03fb: Pull complete
bd3d4dc6e66f: Pull complete
Digest: sha256:c631a581c6152f5a4a141a974b74cf308ab2ee660287a3c749d88e0b536c0c20
Status: Downloaded newer image for my-docker-shared-images.my-artifactory.my-company.com/registry:2
7f18d7ce1985bbbffd85cf254b24b6f1d1996c7b8dd1891fd1974a3fa0b017dd
```

Verify the existence of the local registry:

```
$ docker images
REPOSITORY                                                                        TAG              IMAGE ID       CREATED        SIZE
my-docker-shared-images.my-artifactory.my-company.com/registry                    2                d1fe2eaf6101   35 hours ago   24.1MB
```

And see if it is running as a Docker container:

```
$ docker ps
CONTAINER ID   IMAGE                                                                  COMMAND                  CREATED              STATUS                   PORTS                                                                                         NAMES
7f18d7ce1985   my-docker-shared-images.my-artifactory.my-company.com/registry:2   "/entrypoint.sh /etc…"   About a minute ago   Up About a minute        0.0.0.0:5000->5000/tcp                                                                        k3d-registry.localhost
```

A new directory 'local_registry' should have been created by Docker in your local path (${PWD}), which will be empty as for now:

```
$ cd $PWD
$ ls
local_registry
```

Nice! All is good, lets continue...

Add ```k3d-registry.localhost``` to ```/etc/hosts``` file:

```
127.0.0.1   localhost k3d-registry.localhost
...
```
/etc/hosts

**NOTE**: Exit your current terminal session and enter a new terminal session for the changes to the ```hosts``` file to take effect.

Login to the registry at k3d-registry.localhost:5000

```
$ docker login k3d-registry.localhost:5000
Username: YOUR_USERNAME
Password: YOUR_PASSWORD
WARNING! Your password will be stored unencrypted in /home/${USER}/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

Capture the password from the config.json file as indicated above:

```
$ cat /home/${USER}/.docker/config.json
{
        "auths": {
                "k3d-registry.localhost:5000": {
                        "auth": "ENCRYPTED PASSWORD HERE"
                }
        }
}
```

Add the local registry to your ```registries.yaml```:

```
mirrors:
  "k3d-registry.localhost:5000":
    endpoint:
      - http://k3d-registry.localhost:5000
configs:
  "k3d-registry.localhost:5000":
    auth:
      username: == USERNAME OF AUTHORIZED USER ==
      password: == PASSWORD AS CAN BE FOUND IN /home/${USER}/.docker/config.json ==      
```
registries.yaml

**NOTE**: Make sure you include the **port number** (here: 5000) in the mirrors name as well as in the endpoint and the configs name.

See what is in the local registry:

```
$ curl -X GET http://k3d-registry.localhost:5000/v2/_catalog
{"repositories":[]}
```

As you can see from above statement, there are currently no entries in the local registry.

Add ```k3d-registry.localhost:5000``` to /etc/default/docker:

```
DOCKER_OPTS="$DOCKER_OPTS --insecure-registry k3d-registry.localhost:5000 [OPTIONALLY MORE INSECURE REGISTRIES HERE]"
```
/etc/default/docker

Add ```k3d-registry.localhost:5000``` to /etc/docker/daemon.json as an insecure-registry:

```
{
  "insecure-registries": ["127.0.0.0/8", "my-docker-shared-images.my-artifactory.my-company.com ", "my-proxy.my-company.com", "k3d-registry.localhost:5000"]
}
```
/etc/docker/daemon.json

Don't forget to restart Docker daemon after above changes:

```
$ sudo service docker stop
Redirecting to /bin/systemctl stop docker.service
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ sudo systemctl stop docker
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ ps -ef | grep -E 'docker(d| -d| daemon)\b' | grep -v grep
=== this will show an empty line, which is correct ===
$ sudo dockerd
$ sudo systemctl restart docker
```

Check if ```k3d-registry.localhost:5000``` is now listed as an insecure registry in Docker:

```
$ docker info
...
Insecure Registries:
  ...
  k3d-registry.localhost:5000
  ...
...
```

**GitHub container registry (ghcr.io)**.

**TRY BELOW ONLY IF NOT BLOCKED BY A CORPORATE PROXY, OTHERWISE JUMP DIRECTLY TO WORKAROUND**

See https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry

Creating a Personal Access Token (PAT).

1. Verify your email address, if it hasn't been verified yet. Login to github.com.
2. In the upper-right corner of any page at github.com, click your profile photo, then click **Settings**.
3. In the left sidebar, click **<> Developer settings**.
4. In the left sidebar, click **Personal access tokens**.
5. Click **Generate new token**.
6. Enter your GitHub password if prompted.
7. Give your token a descriptive name. e.g. "GitHub Container Registry (GHCR)"
8. To give your token an expiration, select the Expiration drop-down menu, then click a default or use the calendar picker. Here we choose "No expiration" for this Personal Access Token (PAT).
9. Select the scopes, or permissions, you'd like to grant this token. To use your token to access repositories from the command line, select **repo**. Here we select **repo**, **write:packages** and **delete:packages**.
10. Click **Generate token**.

Save your Personal Access Token (PAT), and export it now (replace YOUR_TOKEN):

```
$ setenv CR_PAT YOUR_TOKEN
```

You should be able to see your PAT again, when doing:

```
$ echo $CR_PAT
```

Now try to login to GitHub Container Registry (ghcr.io), where USERNAME should be your GitHub username (read: email address):

```
$ echo $CR_PAT | docker login ghcr.io -u USERNAME --password-stdin
Error response from daemon: Get "http://ghcr.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
```
If instead of **> Login Succeeded** you get above error message, this means that the corporate proxy is not letting you make a successful login attempt.

**TIP**: Further down this document we have created a repository in Artifactory that has a remote connection with ghcr.io. Its repository name is ```my-ghrc-shared-images.my-artifactory.my-company.com```. Try using that instead of ```ghcr.io```:

```
$ echo $CR_PAT | docker login my-ghcr-shared-images.my-artifactory.my-company.com -u USERNAME --password-stdin
WARNING! Your password will be stored unencrypted in /home/${USER}/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

Hooray, victory : )

You can now observe the new entry in /home/${USER}/.docker/config.json that was automatically created for you, includeing the password (in clear vision, so be careful):

```
$ cat /home/${USER}/.docker/config.json
...
{
        "auths": {
                "my-docker-shared-images.my-artifactory.my-company.com": {
                        "auth": "******************************"
                },
                "my-ghcr-shared-images.my-artifactory.my-company.com": {
                        "auth": "PASSWORD FOR ghcr.io ENTERED HERE AFTER LOGIN AUTOMATICALLY"
                },
                "k3d-registry.localhost:5000": {
                        "auth": "******************************"
                },
                "localhost:5000": {
                        "auth": "******************************"
                }
        },
        "proxies": {
                "default": {
                        "httpProxy": "http://my-proxy-username:***********************@my-proxy.my-company.com:8080/",
                        "httpsProxy": "http://my-proxy-username:***********************@my-proxy.my-company.com:8080/",
                        "noProxy": "127.0.0.0/8,localhost,k3d-registry.localhost,k3d-docker-io"
                }
        }
}
...
```

**NOTE**: As a workaround get the Docker images (ghcr.io/k3d-io/k3d-proxy:5.4.4 and ghcr.io/k3d-io/????) from a server where you do have access to ghcr.io and then copy them over to this server.

Add ```ghcr.io``` to /etc/default/docker:

```
DOCKER_OPTS="$DOCKER_OPTS --insecure-registry ghcr.io  [OPTIONALLY MORE INSECURE REGISTRIES HERE]"
```
/etc/default/docker

Add ```ghcr.io``` to /etc/docker/daemon.json as an insecure-registry:

```
{
  "insecure-registries": ["127.0.0.0/8", "my-docker-shared-images.my-artifactory.my-company.com ", "my-proxy.my-company.com", "ghcr.io"]
}
```
/etc/docker/daemon.json

Don't forget to restart Docker daemon after above changes:

```
$ sudo service docker stop
Redirecting to /bin/systemctl stop docker.service
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ sudo systemctl stop docker
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ ps -ef | grep -E 'docker(d| -d| daemon)\b' | grep -v grep
=== this will show an empty line, which is correct ===
$ sudo dockerd
$ sudo systemctl restart docker
```

Check if ```ghcr``` is now listed as an insecure registry in Docker:

```
$ docker info
...
Insecure Registries:
  ...
  ghcr.io
  ...
...
```

Success!!

Now login to the ```ghcr.io``` repository as follows (replace USERNAME and PASSWORD):

```
$ docker login ghcr.io
Username: [USERNAME]
Password: [PASSWORD]
```

Lets grab any Docker images which we will want to add to our local registry.

Grab the **loadbalancer** required by k3d (replace PROXY_USER and PROXY_PASSWORD):

```
$ sudo HTTP_PROXY=http://[PROXY_USER:[PROXY_PASSWORD]@my-proxy.my-company.com:8080/ docker pull ghcr.io/k3d-io/k3d-proxy:5.4.4
```

Grab the **tools** required by k3d (replace PROXY_USER and PROXY_PASSWORD):

```
$ sudo HTTP_PROXY=http://[PROXY_USER:[PROXY_PASSWORD]@my-proxy.my-company.com:8080/ docker pull ghcr.io/k3d-io/k3d-tools
```
---

**WORKAROUND OPTION A**: Should above fail, because of being blocked by a corporate proxy for example, fear not. Here is an alternative route.

On a server / PC / workstation with Docker and no blocking to the Internet (here: ghcr.io) pull the following Docker Image: 

```
$ docker pull rancher/k3s:v1.23.8-k3s1
```

Now save that Docker Image of **k3s**, as follows, to a *.tar* file:

```
$ docker save rancher/k3s > k3s_image.tar
```

Repeat for the next Docker Image:

```
$ docker pull ghcr.io/k3d-io/k3d-proxy:5.4.4
```

Now save that Docker Image of the **loadbalancer**, as follows, to a *.tar* file:

```
$ docker save ghcr.io/k3d-io/k3d-proxy > k3d-proxy_image.tar
```

Do the same for the following Docker Image:

```
$ docker pull ghcr.io/k3d-io/k3d-tools
```

Now save that Docker Image of the **tools**, as follows, to a *.tar* file:

```
$ docker save ghcr.io/k3d-io/k3d-tools > k3d-tools_image.tar
```

Moved all *.tar* files to the VM with the local registry.

Next, we are going to load these *.tar* files into our local registry.

```
$ docker load < k3s_image.tar
Loaded image: rancher/k3s:v1.23.8-k3s1
$ docker load < k3d-proxy_image.tar
Loaded image: ghcr.io/k3d-io/k3d-proxy:5.4.4
$ docker load < k3d-tools_image.tar
Loaded image: ghcr.io/k3d-io/k3d-tools:latest
```

Check that these Docker Images are now listed:

```
$ docker images
REPOSITORY                  TAG            IMAGE ID      CREATED       SIZE
...
rancher/k3s                 v1.23.8-k3s1   ...           ...           ...
ghcr.io/k3d-io/k3d-proxy    5.4.4          5a963719cb39  11 days ago   42.4MB
ghcr.io/k3d-io/k3d-tools    latest         741f01cb5093  11 days ago   18.7MB
...
```

Well done, they are there!

---
**WORKAROUND OPTION B**: Should above fail, because of being blocked by a corporate proxy for example, fear not. Here is an alternative route.

On the current server where we are installing k3d with Docker and access to a private artifact manager with proxied access to the Internet (here: artifactory) login and pull the following Docker Image (replace YOUR_USERNAME and YOUR_PASSWORD): 

Login for **registry-1.docker.io**:

```
$ docker login my-docker-shared-images.my-artifactory.my-company.com
Username: YOUR_USERNAME
Password: YOUR_PASSWORD
WARNING! Your password will be stored unencrypted in /home/${USER}/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

See the entry for ```my-docker-shared-images.my-artifactory.my-company.com``` in the Docker config.json file:

```
$ cat /home/${USER}/.docker/config.json
{
        "auths": {
                ...
                "my-docker-shared-images.my-artifactory.my-company.com": {
                        "auth": "BASE 64 ENCRYPTED PASSWORD"
                }
                ...
        }
}
```

Now pull the Docker Image ***k3s*** through ***artifactory*** from ***registry-1.docker.io***:

```
$ docker pull my-docker-shared-images.my-artifactory.my-company.com/rancher/k3s:v1.23.8-k3s1
v1.23.8-k3s1: Pulling from rancher/k3s
Digest: sha256:200013cf53a988f92566557b77e309dbbc7b7d715c79113710f8a56dd13edb5f
Status: Image is up to date for my-docker-shared-images.my-artifactory.my-company.com/rancher/k3s:v1.23.8-k3s1
my-docker-shared-images.my-artifactory.my-cpompany.com/rancher/k3s:v1.23.8-k3s1
```

Check that Docker Image ***k3s*** now exists:

```
$ docker images | grep k3s
REPOSITORY                                                          TAG              IMAGE ID       CREATED        SIZE
my-docker-shared-images.my-artifactory.my-company.com/rancher/k3s   v1.23.8-k3s1     e01f2f6c0dd7   4 weeks ago    212MB
```

Let's continue ...

**NOTE**: Make sure ```my-ghcr-shared-images.my-artifactory.my-company.com``` has been added to the /etc/hosts file with its IP address included.

```
...
172.30.1.87     my-artifactory my-docker-shared-images.my-artifactory.my-company.com my-docker-shared-images-local.my-artifactory.my-company.com my-docker-shared-images-remote.my-artifactory.my-company.com my-ghcr-shared-images.my-artifactory.my-company.com my-ghcr-shared-images-local.my-artifactory.my-company.com my-ghcr-shared-images-remote.my-artifactory.my-company.com
...
```
/etc/hosts

**NOTE**: Make sure ```my-ghcr-shared-images.my-artifactory.my-company.com``` has been added to the insecure-registries in Docker in /etc/default/docker.

```
...
DOCKER_OPTS="$DOCKER_OPTS --insecure-registry 127.0.0.0/8 --insecure-registry my-docker-shared-images-local.my-artifactory.my-company.com  --insecure-registry my-docker-shared-images-remote.my-artifactory.my-company.com  --insecure-registry my-docker-shared-images.my-artifactory.my-company.com --insecure-registry my-ghcr-shared-images-local.my-artifactory.my-company.com  --insecure-registry my-ghcr-shared-images-remote.my-artifactory.my-company.com  --insecure-registry my-ghcr-shared-images.my-artifactory.my-company.com"
...
```
/etc/default/docker

**NOTE**: Make sure ```my-ghcr-shared-images.my-artifactory.my-company.com``` has been added to the insecure-registries in Docker in /etc/docker/daemon.json.

```
{
    "insecure-registries": ["127.0.0.0/8", "my-docker-shared-images.my-artifactory.my-company.com", "my-ghcr-shared-images.my-artifactory.my-company.com", "my-proxy.my-company.com"]
}
```
/etc/docker/daemon.json

**NOTE**: Make sure to *restart* Docker deamon for above changes to take effect.

```
$ sudo service docker stop
Redirecting to /bin/systemctl stop docker.service
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ sudo systemctl stop docker
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ ps -ef | grep -E 'docker(d| -d| daemon)\b' | grep -v grep
=== this will show an empty line, which is correct ===
$ sudo dockerd
$ sudo systemctl restart docker
```

Next, login to ```my-ghcr-shared-images.my-artifactory.my-company.com``` as follows:

```
$ docker login my-ghcr-shared-images.my-artifactory.my-company.com
Username: YOUR_USERNAME (meaning your email address of GitHub)
Password: YOUR PASSWORD (meaning your Personal Access Token of GitHub)
WARNING! Your password will be stored unencrypted in /home/${USER}/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

See the entry for ```my-ghcr-shared-images.my-artifactory.my-company.com``` in the Docker config.json file:

```
$ cat /home/${USER}/.docker/config.json
{
        "auths": {
                ...
                "my-ghcr-shared-images.my-artifactory.my-company.com": {
                        "auth": "BASE 64 ENCRYPTED PASSWORD"
                }
                ...
        }
}
```

***WARNING***: Make sure in Artifactory for the repository ```my-ghcr-shared-images``` under the **Advanced** tab, you have **enabled** the following:

- Artifactory Requests Can Retrieve Remote Artifacts

Now pull the Docker Image ***k3d-proxy*** through artifactory:

```
$ docker pull my-ghcr-shared-images.my-artifactory.my-company.com/k3d-io/k3d-proxy:5.4.4
Error response from daemon: manifest for my-ghcr-shared-images.my-artifactory.my-company.com/k3d-io/k3d-proxy:5.4.4 not found: manifest unknown: The named manifest is not known to the registry.
```

Now pull the Docker Image ***k3d-tools*** through artifactory:

```
$ docker pull my-ghcr-shared-images.my-artifactory.my-company.com/k3d-io/k3d-tools:latest
Error response from daemon: manifest for my-ghcr-shared-images.my-artifactory.my-company.com/k3d-io/k3d-tools:latest not found: manifest unknown: The named manifest is not known to the registry.
```

WE ARE GOING TO FIX THE ABOVE ERROR! See https://linuxhandbook.com/manifest-not-found-error-docker/ and https://www.jfrog.com/jira/browse/RTFACT-23546

Check if your ```k3d-headstart/registries.yaml``` is the same in content as the one stored at ```/etc/rancher/k3s/registries.yaml```.

```
$ cat k3d-headstart/registries.yaml
mirrors:
  "docker.io":
    endpoint:
      - http://k3d-docker-io:5001
  "k3d-registry.localhost:5000":
    endpoint:
      - http://k3d-registry.localhost:5000
  ghcr.io:
    endpoint:
      - http://my-ghcr-shared-images.my-artifactory.my-company.com
  my-ghcr-shared-images.my-artifactory.my-company.com:
    endpoint:
      - http://my-ghcr-shared-images.my-artifactory.my-company.com
  my-docker-shared-images.my-artifactory.my-company.com:
    endpoint:
      - http://my-docker-shared-images.my-artifactory.my-company.com
configs:
  "k3d-registry.localhost:5000":
    auth:
      username: === LOCAL USERNAME === 
      password: === LOCAL PASSWORD ===
  ghcr.io:
    auth:
      username: === E-MAIL ADDRESS OF YOUR GITHUB.COM ACCOUNT ===
      password: === PERSONAL ACCESS TOKEN (PAT) OF YOUR GITHUB.COM ACCOUNT ===
  my-ghcr-shared-images.my-artifactory.my-company.com:
    auth:
      username: === E-MAIL ADDRESS OF YOUR GITHUB.COM ACCOUNT ===
      password: === PERSONAL ACCESS TOKEN (PAT) OF YOUR GITHUB.COM ACCOUNT ===
  my-docker-shared-images.my-artifactory.my-company.com:
    auth:
      username: === CORPORATE PROXY USERNAME ===
      password: === CORPORATE PROXY PASSWORD ===
```
k3d-headstart/registries.yaml

```
$ cat /etc/rancher/k3s/registries.yaml
mirrors:
  my-docker-shared-images.my-artifactory.my-company.com:
    endpoint:
      - http://my-docker-shared-images.my-artifactory.my-company.com
  docker.io:
    endpoint:
      - http://my-docker-shared-images.my-artifactory.my-company.com
  gcr:
      - http://my-docker-shared-images.my-artifactory.my-company.com
  ghcr.io:
      - http://my-docker-shared-images.my-artifactory.my-company.com
configs:
  my-docker-shared-images.my-artifactory.my-company.com:
    auth:
      username: my-proxy-username
      password: *******************
  docker.io:
    auth:
      username: my-proxy-username
      password: *******************
  gcr.io:
    auth:
      username: my-proxy-username
      password: *******************
  ghcr.io:
    auth:
      username: my-proxy-username
      password: *******************
```
/etc/rancher/k3s/registries.yaml

If not, copy the content of your ```k3d-headstart/registries.yaml``` into  ```/etc/rancher/k3s/registries.yaml```

You can view the **k3d-packages** here at https://github.com/orgs/k3d-io/packages

```
$ docker inspect my-ghcr-shared-images.my-artifactory.my-company.com/k3d-io/k3d-tools
[]
Error: No such object: my-ghcr-shared-images.my-artifactory.my-company.com/k3d-io/k3d-tools
```

Read : https://www.jfrog.com/confluence/display/JFROG/Advanced+Settings

- On Artifactory set **Remote Credentials** for ```my-ghcr-shared-images.my-artifactory.my-company.com```

- On Artifactory set **Network Settings** for ```my-ghcr-shared-images.my-artifactory.my-company.com```

See https://forums.docker.com/t/proxy-authentication-required-error-when-fetching-image/3862/3 and https://medium.com/@bennyh/docker-and-proxy-88148a3f35f7 and follow along next.

Create a file called ```http-proxy.conf``` in the following directory:

```
$ sudo touch /etc/systemd/system/docker.service.d/http-proxy.conf
```

Add the proxy entries inside this file ```http-proxy.conf```:

```
[Service]
Environment="HTTP_PROXY=http://PROXY_USERNAME:PROXY_PASSWORD@PROXY_FQDN:PROXY_PORT/"
Environment="HTTPS_PROXY=http://PROXY_USERNAME:PROXY_PASSWORD@PROXY_FQDN:PROXY_PORT/"
Environment="NO_PROXY=127.0.0.0/8,localhost,k3d-registry.localhost,k3d-docker-io,*.my-company.com"
```
/etc/systemd/system/docker.service.d/http-proxy.conf

Where:

- PROXY_USERNAME = The username that has permission to go through the proxy.
- PROXY_PASSWORD = The password that belongs with the username that has permission to go through the proxy. **Note**: On Windows the password must be URI-encoded.
- PROXY_FQDN = The fully qualified domain name (e.g.my-proxy.my-company.com) of the proxy
- PROXY_PORT = The port number (e.g. 8080) of the proxy

**Note**: Do not forget to add the ```*.YOUR_COMPANY_FQDN``` to the NO_PROXY entry above (here ```*.my-company.com```)

Follow on by restarting Docker daemon for the changes to take effect.

```
$ sudo systemctl daemon-reload
$ sudo systemctl stop docker
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ sudo systemctl start docker
```

Check is Docker is up:

```
$ sudo systemctl status docker 
.... SOME FEEDBACK ON STATUS HERE ....
```

Verify that the configuration has been loaded:

```
$ systemctl show docker --property Environment
Environment=
```

Not as expected ...... : (

Locate the ```docker.service``` file by running:

```
$ systemctl show -p FragmentPatch docker.service
FragmentPath=/usr/lib/systemd/system/docker.service
```

OK, now go to this file and edit it:

```
$ sudo vim /usr/lib/systemd/system/docker.service 
```

Enter the same entries of the ```Environment``` as we did just now, but this time inside ```/usr/lib/systemd/system/docker.service```:

```
...
[Service]
Environment="HTTP_PROXY=http://PROXY_USERNAME:PROXY_PASSWORD@PROXY_FQDN:PROXY_PORT/"
Environment="HTTPS_PROXY=http://PROXY_USERNAME:PROXY_PASSWORD@PROXY_FQDN:PROXY_PORT/"
Environment="NO_PROXY=127.0.0.0/8,localhost,k3d-registry.localhost,k3d-docker-io"
...
```
/usr/lib/systemd/system/docker.service

Follow on by restarting Docker daemon for the changes to take effect.

```
$ sudo systemctl daemon-reload
$ sudo systemctl stop docker
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ sudo systemctl start docker
```

Check is Docker is up:

```
$ sudo systemctl status docker 
.... SOME FEEDBACK ON STATUS HERE ....
```

Verify that the configuration has been loaded:

```
$ systemctl show docker --property Environment
Environment=Environment=HTTP_PROXY=http://my-proxy-username:*****************@my-proxy.my-company.com:8080/ HTTPS_PROXY=http://my-proxy-username:*****************@my-proxy.my-company.com:8080/ NO_PROXY=127.0....
lines 1-1/1 (END)
```

*TIP*: You can escape from above prompt by pressing CTRL and C at the same time.

Hooray!!! So now we seem to have told that we are using a proxy and provided it with valid credentials!!

=====> Have a Drink, then Continue 

Let's try to login to GitHub Container registry now, using our Personal Access Token (PAT) as our password.

```
$ docker login ghcr.io
Username: USE YOUR EMAIL ADDRESS OF YOUR GITHUB ACCOUNT HERE
Password: USE YOUR PERSONAL ACCESS TOKEN (PAT) HERE
WARNING! Your password will be stored unencrypted in /home/${USER}/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

Brilliant .... we successfully went through the proxy : )

Have a sneek peek at the configuration file:

```
$ cat /home/${USER}/.docker/config.json
{
        "auths": {
                "my-docker-shared-images.my-artifactory.my-company.com": {
                        "auth": "***********"
                },
                "my-ghcr-shared-images.my-artifactory.my-company.com": {
                        "auth": "***********"
                },
                "ghcr.io": {
                        "auth": "A VERY LONG TOKEN CREATED AFTER LOGIN TO GHCR.IO"
                },
                "k3d-registry.localhost:5000": {
                        "auth": "***********"
                },
                "localhost:5000": {
                        "auth": "***********"
                }
        },
        "proxies": {
                "default": {
                        "httpProxy": "http://my-proxy-username:***********@my-proxy.my-company.com:8080/",
                        "httpsProxy": "http://my-proxy-username:***********@my-proxy.my-company.com:8080/",
                        "noProxy": "127.0.0.0/8,localhost,k3d-registry.localhost,k3d-docker-io"
                }
        }
}
```
/home/${USER}/.docker/config.json

Nice!

Next, try if we can pull the ```k3d-proxy``` Docker image from **ghcr.io**:

```
$ docker pull ghcr.io/k3d-io/k3d-proxy:5.4.4
5.4.4: Pulling from k3d-io/k3d-proxy
Digest: sha256:25597759d34d7c4e25e569b023fe84b1ba3f6e4b5de3bc1005bd893e24a2e688
Status: Image is up to date for ghcr.io/k3d-io/k3d-proxy:5.4.4
ghcr.io/k3d-io/k3d-proxy:5.4.4
```

And the same for the ```k3d-tools``` Docker image from **ghcr.io**:

```
$ docker pull ghcr.io/k3d-io/k3d-tools:latest
latest: Pulling from k3d-io/k3d-tools
Digest: sha256:119390b61bd566b6cc330d4de75f26a8a34d09cc9a57e36a8192918fc92468eb
Status: Image is up to date for ghcr.io/k3d-io/k3d-tools:latest
ghcr.io/k3d-io/k3d-tools:latest
```

Hold your horses, let's see if these Docker images are now listed in our repository:

```
$ docker images | grep k3d-io
ghcr.io/k3d-io/k3d-proxy                  5.4.4            5a963719cb39   3 weeks ago     42.4MB
ghcr.io/k3d-io/k3d-tools                  latest           741f01cb5093   3 weeks ago     18.7MB
...
```

And the same for the ```k3s``` Docker image from **docker.io**:

```
$ docker pull rancher/k3s:v1.23.8-k3s1
v1.23.8-k3s1: Pulling from rancher/k3s
Digest: sha256:200013cf53a988f92566557b77e309dbbc7b7d715c79113710f8a56dd13edb5f
Status: Downloaded newer image for rancher/k3s:v1.23.8-k3s1
docker.io/rancher/k3s:v1.23.8-k3s1
```

Let's also see if this Docker image is now listed in our repository:

```
$ docker images | grep k3s
rancher/k3s                               v1.23.8-k3s1     e01f2f6c0dd7   5 weeks ago     212MB
...
```

Magic! It all works now.

Let's continue ...

Now **tag** the docker image ```k3s``` of **k3s** as follows, using localhost and registry port number (here: 5000). See https://www.youtube.com/watch?v=O_NMIZJ1qvw

```
$ docker tag rancher/k3s:v1.23.8-k3s1 k3d-registry.localhost:5000/rancher/k3s:v1.23.8-k3s1
```

Check that it exists:

```
$ docker images
REPOSITORY                                TAG            IMAGE ID       CREATED        SIZE
...
k3d-registry.localhost:5000/rancher/k3s   v1.23.8-k3s1   ...            ...            ...
...
```

Now **tag** the docker image ```k3d-proxy``` of the **loadbalancer** as follows, using localhost and registry port number (here: 5000). See https://www.youtube.com/watch?v=O_NMIZJ1qvw

```
$ docker tag ghcr.io/k3d-io/k3d-proxy:5.4.4 k3d-registry.localhost:5000/k3d-io/k3d-proxy:5.4.4
```

Check that it exists:

```
$ docker images
REPOSITORY                                     TAG     IMAGE ID       CREATED        SIZE
...
k3d-registry.localhost:5000/k3d-io/k3d-proxy   5.4.4   5a963719cb39   11 days ago    42.4MB
...
```

Now **tag** the docker image ```k3d-tools``` of the **tools** as follows, using localhost and registry port number (here: 5000). See https://www.youtube.com/watch?v=O_NMIZJ1qvw

```
$ docker tag ghcr.io/k3d-io/k3d-tools:latest k3d-registry.localhost:5000/k3d-io/k3d-tools:latest
```

Check that it exists:

```
$ docker images
REPOSITORY                        TAG     IMAGE ID       CREATED        SIZE
...
k3d-registry.localhost:5000/k3d-io/k3d-tools   latest  741f01cb5093   11 days ago    18.7MB
...
```

Next, let us try and push both Docker Images to our local registry:

```
$ docker push k3d-registry.localhost:5000/k3d-io/k3d-proxy:5.4.4
The push refers to repository [k3d-registry.localhost:5000/k3d-io/k3d-proxy]
Get "https://k3d-registry.localhost:5000/v2/": http: server gave HTTP response to HTTPS client
```

```
$ docker push k3d-registry.localhost:5000/k3d-io/k3d-tools:latest
The push refers to repository [k3d-registry.localhost:5000/k3d-io/k3d-tools]
Get "https://k3d-registry.localhost:5000/v2/": http: server gave HTTP response to HTTPS client
```

As you can see from above, the push is **failing** as Docker does not recognize this registry. We have to add it as an insecure registry to daemon.json

```
$ sudo vim /etc/docker/daemon.json
```

Add this entry to daemon.json:

```
{
    "insecure-registries":["k3d-registry.localhost:5000"]    
}
```
/etc/docker/daemon.json

Follow on by restarting Docker daemon for the changes to take effect.

```
$ sudo systemctl stop docker
Warning: Stopping docker.service, but it can still be activated by:
  docker.socket
$ sudo systemctl start docker
```

Check is Docker is up:

```
$ sudo systemctl status docker 
.... SOME FEEDBACK ON STATUS HERE ....
```

Look if the newly added insecure registry is listed:

```
$ docker info
...
Insecure Registries: 
  k3d-registry.localhost:5000
...
```

All good!

Now try to push again:

```
$ docker push k3d-registry.localhost:5000/k3d-io/k3d-proxy:5.4.4
received unexpected HTTP status: 500 Internal Server Error
```

```
$ docker push k3d-registry.localhost:5000/k3d-io/k3d-tools:latest
received unexpected HTTP status: 500 Internal Server Error
```

Check if the registry container is running.

```
$ docker ps
CONTAINER ID        IMAGE                                                                  COMMAND                  CREATED             STATUS              PORTS                    NAMES
a3751e407aa9        my-docker-shared-images.my-artifactory.my-company.com/registry:2   "/entrypoint.sh /etc…"   6 hours ago         Up 14 minutes       0.0.0.0:5000->5000/tcp   k3d-registry.localhost
```

So yes, it is running.

Looking in the logs of the container we see the following (replace CONTAINER ID):

```
$ docker logs CONTAINER ID
...
time="2022-07-25T07:59:34.468022536Z" level=error msg="response completed with error" err.code=unknown err.detail="filesystem: mkdir /var/lib/registry/docker: permission denied" err.message="unknown error" go.version=go1.16.15 http.request.host="k3d-registry.localhost:5000" http.request.id=e35b8d41-dfe8-4604-ae50-74b13868cff6 http.request.method=POST http.request.remoteaddr="172.30.123.11:49658" http.request.uri="/v2/k3d-io/k3d-proxy/blobs/uploads/" http.request.useragent="docker/20.10.16 go/go1.17.10 git-commit/f756502 kernel/3.10.0-1160.49.1.el7.x86_64 os/linux arch/amd64 UpstreamClient(Docker-Client/20.10.16 \(linux\))" http.response.contenttype="application/json; charset=utf-8" http.response.duration=1.734155ms http.response.status=500 http.response.written=164 vars.name="k3d-io/k3d-proxy"
```

So, as it states **permission denied** it looks like is a permission related issue : )

**TIP**: If you are running Docker on a system that uses SELinux you will need to issue the following:

```
$ cd k3d-headstart
$ sudo chcon -Rt svirt_sandbox_file_t local_registry
```

The above command changes the security context (```chcon```) of SELinux, with the following flags:
- R: operate on files and directories recursively
- t: set type TYPE in the target security context

Red Hat extends the libvirt-SELinux model to Docker. The Docker SELinux role and Docker SELinux types are based on libvirt. For example, by default, Docker has access to /usr/var/ and some other locations, but it has complete access to things that are labeled with ```svirt_sandbox_file_t```. See https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html/container_security_guide/docker_selinux_security_policy

Now try again to push again:

```
$ docker push k3d-registry.localhost:5000/k3d-io/k3d-proxy:5.4.4
The push refers to repository [k3d-registry.localhost:5000/k3d-io/k3d-proxy]
dc7d67bb5b33: Pushed
6e7fbd886b94: Pushed
8e54110bd5bf: Pushed
acedbeaf3473: Pushed
e19403a7f3a5: Pushed
4689e8eca613: Pushed
3480549413ea: Pushed
3c369314e003: Pushed
4531e200ac8d: Pushed
ed3fe3f2b59f: Pushed
b2d5eeeaba3a: Pushed
5.4.4: digest: sha256:a8ab0d22c0afe8f9aadf1aed0c8de820147c18602b4c77f8857cf50c8c9cdc73 size: 2607 
```

```
$ docker push k3d-registry.localhost:5000/k3d-io/k3d-tools:latest
The push refers to repository [k3d-registry.localhost:5000/k3d-io/k3d-tools]
55a97e0e02df: Pushed
656ea4832660: Pushed
20546c21ff83: Pushed
64242626f2eb: Pushed
latest: digest: sha256:ba152edda1ed4d8b4b7780de35a4ff5beb194f7a2f6d11567d167b39a4de032a size: 1156
```

Both pushes went successful!

Check if we can login to the local registry:

```
$ docker login k3d-registry.localhost:5000
Username: ********
Password: ********
WARNING! Your password will be stored unencrypted in /home/${USER}/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

Yes! So that went well also.

Have a look inside the newly created configuration file for the authorization part:

```
$ cat /home/${USER}/.docker/config.json
{
        "auths": {
                "k3d-registry.localhost:5000": {
                        "auth": "A BASE 64 ENCODED PASSWORD SHOWS HERE"
                }
        }
}
```

Check if the image is actually in the registry:

```
$ curl -X GET http://k3d-registry.localhost:5000/v2/_catalog
{"repositories":["k3d-io/k3d-proxy","k3d-io/k3d-tools"]}
```

So both images are now contained in the local registry.

### Verify that you current have no k3d clusters

```
$ k3d cluster list
NAME  SERVERS  AGENTS  LOADBALANCER
```

```
$ k3d node list
NAME  ROLE  CLUSTER STATUS
```

### Check all docker containers

```
$ docker ps -a
CONTAINER ID   IMAGE                                                                  COMMAND                  CREATED             STATUS                    PORTS                    NAMES
62efc0e17f0e   my-docker-shared-images.my-artifactory.my-company.com/registry:2   "/entrypoint.sh /etc…"   About an hour ago   Up 26 minutes             0.0.0.0:5000->5000/tcp   k3d-registry.localhost
...
```

### Check Active Internet connections (only servers)

```
$ sudo netstat -tlpn
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:4593            0.0.0.0:*               LISTEN      33668/docker-proxy
... etcetera
```

### Check nodes with kubectl (Optional, requires kubectl to have been installed)

```
$ kubectl get nodes -o wide
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

**WARNING**: Above error needs to be fixed .... have a look at https://discuss.kubernetes.io/t/the-connection-to-the-server-localhost-8080-was-refused-did-you-specify-the-right-host-or-port/1464/12?page=2

###  Creating single-node clusters

See also "K3D Getting Started Guide – detailed tutorial on running k3s in docker" at https://developpaper.com/k3d-getting-started-guide-detailed-tutorial-on-running-k3s-in-docker/ equal to https://www.suse.com/c/rancher_blog/introduction-to-k3d-run-k3s-in-docker/

Starting from the root of our registry (where the ```--verbose``` flag is optional, replace VERSION by the k3d mayor version you are using, e.g. ```v5```).

```
$ cd /k3d-headstart
$ k3d cluster create --config ${PWD}/mycluster_k3d_VERSION.yaml --verbose
...
ERRO[0060] Failed to run tools container for cluster 'mycluster'
ERRO[0060] failed to gather environment information used for cluster creation: failed to run k3d-tools node for cluster 'mycluster': failed to create node 'k3d-mycluster-tools': runtime failed to create node 'k3d-mycluster-tools': failed to create container for node 'k3d-mycluster-tools': docker failed to pull image 'ghcr.io/k3d-io/k3d-tools:5.4.4': docker failed to pull the image 'ghcr.io/k3d-io/k3d-tools:5.4.4': Error response from daemon: Get "http://ghcr.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
...
```

The **error** above show that it is trying to pull the ```k3d-tools``` from ```ghcr.io```, whereas we have set up a local registry that contains this ```k3d-tools``` now.

Follow this guideline at https://www.natarajmb.com/2021/10/kubernetes-local-development-k3d-docker/

As the docker registry is outside of the k8s cluster, we need to make the k3d aware of the registry. Make k3d aware of the registry by passing the details during cluster creation. We already created the file ```registries.yaml``` at ```k3d-headstart/registries.yaml```.

```
mirrors:
  "k3d-registry.localhost:5000":
    endpoint:
      - http://k3d-registry.localhost:5000
...
```
k3d-headstart/registries.yaml

**NOTE**: Make sure you include the **port number** (here: 5000) in the mirrors name as well as in the endpoint.

Look at network names using docker network ls.

```
$ docker network ls
NETWORK ID     NAME                                        DRIVER    SCOPE
95955d84f0a4   bridge                                      bridge    local
fd245e2df683   gateway-dev                                 bridge    local
afc53f7f8cd0   host                                        host      local
f56b45011e3d   k3d-k3s-default                             bridge    local
a5816d3415b2   k3d-mycluster                               bridge    local
21a06f729974   none                                        null      local
```

Connect local docker registry network ```k3d-registry.localhost``` to k8s network so that k8s can pull images from it. 

```
$ docker network connect k3d-mycluster k3d-registry.localhost
```

See https://k3d.io/v5.2.0/usage/registries/ section "Using your own (not k3d-managed) local registry".

**Note**: Skip the above step if the docker registry is hosted *externally* and can be reached with URL mentioned in k3d-headstart/registries.yaml

Add ```k3d-registry.localhost``` to the /etc/hosts file similar to ```localhost```:

```
...
127.0.0.1   localhost k3d-registry.localhost
...
```
/etc/hosts

**NOTE**: Exit your current terminal session and enter a new terminal session for the hosts file to take effect.

Create the cluster with the name ```mycluster``` as the name:

```
$ cd /k3d-headstart
$ k3d cluster create mycluster --port 9000:80@loadbalancer --registry-config /home/${USER}/git/k3d-headstart/registries.yaml -v /home/${USER}/git/k3d-headstart/registries.yaml:/etc/rancher/k3s/registries.yaml --image my-docker-shared-images.my-artifactory.my-company.com/rancher/k3s:latest
```
**NOTE**: Do not use the *relative* paths for volume

You may be prompted as follows:
```
WARN[0000] Seems like you're mounting a file at '/etc/rancher/k3s/registries.yaml' while also using a referenced registries config or k3d-managed registries: Your mounted file will probably be overwritten!
WARN[0000] No node filter specified
INFO[0000] portmapping '9000:80' targets the loadbalancer: defaulting to [servers:*:proxy agents:*:proxy]
INFO[0000] Prep: Network
INFO[0000] Re-using existing network 'k3d-mycluster' (a5816d3415b216104864911ef01c83c865a5ef793f2fb3f814c1356b50fa6ed9)
INFO[0000] Created image volume k3d-mycluster-images
INFO[0000] Starting new tools node...
INFO[0000] Pulling image 'ghcr.io/k3d-io/k3d-tools:5.4.4'
INFO[0000] Starting Node 'k3d-mycluster-tools'
INFO[0001] Creating node 'k3d-mycluster-server-0'
INFO[0001] Creating LoadBalancer 'k3d-mycluster-serverlb'
INFO[0001] Using the k3d-tools node to gather environment information
INFO[0001] HostIP: using network gateway 172.26.0.1 address
INFO[0001] Starting cluster 'mycluster'
INFO[0001] Starting servers...
ERRO[0001] Node k3d-mycluster-server-0: Failed executing preStartAction 'WriteFileAction': [WriteFileAction] Writing 1306 bytes to /etc/rancher/k3s/registries.yaml (mode -rw-r--r--): Write Registry Configuration: Failed to copy content to container 'f7d79940e131326b48552bb5a4c07ea2b216efeb020cafedd5e168505df758da': Error response from daemon: Error processing tar file(exit status 1): unlinkat /etc/rancher/k3s/registries.yaml: device or resource busy
INFO[0001] Starting Node 'k3d-mycluster-server-0'
INFO[0006] All agents already running.
INFO[0006] Starting helpers...
INFO[0006] Starting Node 'k3d-mycluster-serverlb'
INFO[0012] Injecting records for hostAliases (incl. host.k3d.internal) and for 3 network members into CoreDNS configmap...
INFO[0014] Cluster 'mycluster' created successfully!
INFO[0015] You can now use it like this:
kubectl cluster-info
```

**CONGRATS**: You have single-handedly created a Kubernetes Cluster!

The above command creates a single node cluster named ```mycluster``` and adds a loadbalancer and ingress controller traefik. Loadbalancer sits outside of the cluster as a docker container and routes traffic through to each node, in this case, just to one node. The above command also should add k8s context to your kubeconfig. If not, follow the command outputs to do the same.

In case you receive an error like "*Failed to find registry node 'k3d-registry.localhost': failed to translate container '/k3d-registry.localhost' details to node spec: container not managed by k3d: missing default label(s)*" see https://bytemeta.vip/repo/rancher/k3d/issues/763

In case you received another error, first follow below instructions on creating a registry proxy / pull-through registry before trying above instructions again.

### Creating a registry proxy / pull-through registry

**WARNING**: registry proxy / pull-through registry is only supported since k3d v5.4.3, so **not** available with k3d v4.4.8!

See https://k3d.io/v5.4.4/usage/registries/ and https://www.talos.dev/v1.1/talos-guides/configuration/pull-through-cache/

1. Create a pull-through registry

See all possibilities as follows:

```
$ k3d registry create --help
Create a new registry.

Usage:
  k3d registry create NAME [flags]

Flags:
      --default-network string    Specify the network connected to the registry (default "bridge")
  -h, --help                      help for create
  -i, --image string              Specify image used for the registry (default "docker.io/library/registry:2")
      --no-help                   Disable the help text (How-To use the registry)
  -p, --port [HOST:]HOSTPORT      Select which port the registry should be listening on on your machine (localhost) (Format: [HOST:]HOSTPORT)
                                   - Example: `k3d registry create --port 0.0.0.0:5111` (default "random")
      --proxy-password string     Specify the password of the proxied remote registry
      --proxy-remote-url string   Specify the url of the proxied remote registry
      --proxy-username string     Specify the username of the proxied remote registry
  -v, --volume [SOURCE:]DEST      Mount volumes into the registry node (Format: [SOURCE:]DEST

Global Flags:
      --timestamps   Enable Log timestamps
      --trace        Enable super verbose output (trace logging)
      --verbose      Enable verbose output (debug logging)
```

```
$ k3d registry create docker-io `# Create a registry named k3d-docker-io` \ 
  -p 5001 `# listening on local host port 5001` \ 
  --proxy-remote-url https://registry-1.docker.io `# let it mirror the Docker Hub registry` \ 
  -v ~/.local/share/docker-io-registry:/var/lib/registry `# also persist the downloaded images on the device outside the container` \
  --image my-docker-shared-images.my-artifactory.my-company.com/registry:2 `# use remote custom repository`
```

***NOTE***: If we do not have direct access to ```https://registry-1.docker.io```, but instead retrieve Docker images through a corporate proxy use the following instead including proxy username and password:

```
$ k3d registry create docker-io `# Create a registry named k3d-docker-io` \ 
  -p 5001 `# listening on local host port 5001` \ 
  --proxy-remote-url https://registry-1.docker.io `# let it mirror the Docker Hub registry` \ 
  --proxy-username PROXY_USERNAME `# username to access proxied repository` \
  --proxy-password "PROXY_PASSWORD" `# password to access proxied repository, use of quotes recommended` \
  -v ~/.local/share/docker-io-registry:/var/lib/registry `# also persist the downloaded images on the device outside the container` \
  --image my-docker-shared-images.my-artifactory.my-company.com/registry:2 `# use remote custom repository`
```

If all goes well you will be prompted as follows:

```
INFO[0000] Creating node 'k3d-docker-io'
INFO[0000] Successfully created registry 'k3d-docker-io'
INFO[0000] Starting Node 'k3d-docker-io'
INFO[0000] Successfully created registry 'k3d-docker-io'
# You can now use the registry like this (example):
# 1. create a new cluster that uses this registry
k3d cluster create --registry-use k3d-docker-io:5001
```

**WARNING**: Add the newly created registry name to /etc/hosts as a local entry (just like localhost):

```
$ sudo vim /etc/hosts
```

```
127.0.0.1    localhost k3d-docker-io # optional, any other hosts
...
```
/etc/hosts

Save and log out and back in to get a new terminal session for the change to take effect.

# 2. tag an existing local image to be pushed to the registry

Example only, not required:

```
$ docker tag nginx:latest k3d-docker-io:5001/mynginx:v0.1
```

# 3. push that image to the registry

Example only, not required:

```
$ docker push k3d-docker-io:5001/mynginx:v0.1
```

# 4. run a pod that uses this image

Example only, not required:

```
$ kubectl run mynginx --image k3d-docker-io:5001/mynginx:v0.1
```

*TIP*: Should you decide not to want this registry, you can delete it as follows:

```
$ k3d registry delete k3d-docker-io
```

But we are going to keep the registry for now!

See the list of registries:

```
$ k3d registry list
NAME            ROLE       CLUSTER   STATUS
k3d-docker-io   registry             running
```

Tag an existing local image of k3s to be pushed to the registry:

```
$ docker tag rancher/k3s:v1.23.8-k3s1 k3d-docker-io:5001/rancher/k3s:v1.23.8-k3s1
```

2. Create ```registries.yaml```

```
mirrors: 
  "docker.io": 
    endpoint: 
      - http://k3d-docker-io:5001
```
registries.yaml

3. Create a cluster and using the pull-through cache

```
$ k3d cluster create cluster01 --registry-use k3d-docker-io:5001 --registry-config registries.yaml
INFO[0000] Prep: Network
INFO[0000] Re-using existing network 'k3d-cluster01' (abcc5120598f7acd94770e3c6f92b7c231f3905fb30755d11a975181b095f6f5)
INFO[0000] Created image volume k3d-cluster01-images
INFO[0000] Container 'k3d-docker-io' is already connected to 'k3d-cluster01'
INFO[0000] Starting new tools node...
INFO[0000] Starting Node 'k3d-cluster01-tools'
INFO[0001] Creating node 'k3d-cluster01-server-0'
INFO[0001] Creating LoadBalancer 'k3d-cluster01-serverlb'
INFO[0001] Using the k3d-tools node to gather environment information
INFO[0001] HostIP: using network gateway 172.27.0.1 address
INFO[0001] Starting cluster 'cluster01'
INFO[0001] Starting servers...
INFO[0001] Starting Node 'k3d-cluster01-server-0'
INFO[0008] All agents already running.
INFO[0008] Starting helpers...
INFO[0008] Starting Node 'k3d-cluster01-serverlb'
INFO[0015] Injecting records for hostAliases (incl. host.k3d.internal) and for 3 network members into CoreDNS configmap...
INFO[0018] Cluster 'cluster01' created successfully!
INFO[0018] You can now use it like this:
kubectl cluster-info
```

**Note**: If above prompt shows an error in the likes of ```invalid IP address in add-host: "host-gateway"```, then most likely your **Docker version is lower than version 20**. Hence, Docker has a feature since v20.10.0+ for Linux (at least according to [Stackoverflow](https://stackoverflow.com/questions/48546124/what-is-linux-equivalent-of-host-docker-internal/62431165#62431165)) here you can add a special host ```--add-host=host.docker.internal:host-gateway```, which refers to the host system, so you can access their ports from a container.. See: https://github.com/containers/podman/issues/10878. Recommended to upgrade to at least Docker v20.10.5 (runc >= v1.0.0-rc93), as stated in the Requirements section of this repository earlier. Try ```$ docker version``` to see which version of Docker you are currently on. More details at https://medium.com/@TimvanBaarsen/how-to-connect-to-the-docker-host-from-inside-a-docker-container-112b4c71bc66

You can discover the missing IP address (which differs, and therefor needs to be retrieved first) and add it to your /etc/hosts file. Run the following command to get the static IP:

```
ip addr show | grep "\binet\b.*\bdocker0\b" | awk '{print $2}' | cut -d '/' -f 1
```
_____

**WARNING**: AS A LAST RESORT downgrade to ***k3d*** to version ```4.4.8```, to prevent above error from happening if you cannot upgrade ***Docker*** to ```v20.10.5``` (replace PROXY_USER, PROXY_PASSWORD, PROXY_FQDN, PROXY_PORT):

Use the k3d CLI binary from the root of this repository (note: original can be found at https://github.com/k3d-io/k3d/releases), k3d-v4-linux-amd64.

```
$ sudo chmod +x k3d-v4-linux-amd64
$ sudo chown root k3d-v4-linux-amd64
$ sudo chgrp root k3d-v4-linux-amd64
$ sudo install k3d-v4-linux-amd64 /usr/local/bin/k3d
$ k3d version
k3d version v4.4.8
k3s version v1.21.3-k3s1 (default)
```

Run the cluster creation command now for k3d version 4.4.8:

```
$ k3d cluster create cluster01 --registry-use k3d-docker-io:5001 --registry-config registries.yaml
INFO[0000] Prep: Network
INFO[0000] Created network 'k3d-cluster01' (1d2696aa0937563adbe2ae2540337f836f0f1ec7eddc40966fc5bb7b8d2fe234)
INFO[0000] Created volume 'k3d-cluster01-images'
INFO[0001] Creating node 'k3d-cluster01-server-0'
INFO[0008] Pulling image 'docker.io/rancher/k3s:v1.21.3-k3s1'
INFO[0013] Creating LoadBalancer 'k3d-cluster01-serverlb'
INFO[0014] Pulling image 'docker.io/rancher/k3d-proxy:4.4.8'
INFO[0016] Starting cluster 'cluster01'
INFO[0016] Starting servers...
INFO[0017] Starting Node 'k3d-cluster01-server-0'
INFO[0022] Starting agents...
INFO[0022] Starting helpers...
INFO[0022] Starting Node 'k3d-cluster01-serverlb'
INFO[0023] (Optional) Trying to get IP of the docker host and inject it into the cluster as 'host.k3d.internal' for easy access
INFO[0026] Successfully added host record to /etc/hosts in 2/2 nodes and to the CoreDNS ConfigMap
INFO[0027] Cluster 'cluster01' created successfully!
INFO[0027] --kubeconfig-update-default=false --> sets --kubeconfig-switch-context=false
INFO[0027] You can now use it like this:
kubectl config use-context k3d-cluster01
kubectl cluster-info
```

Run the command as recommended above:

```
$ kubectl config use-context k3d-cluster01
Switched to context "k3d-cluster01".
```
_____

Check it as follows:

```
$ kubectl cluster-info
Kubernetes control plane is running at https://0.0.0.0:40610
CoreDNS is running at https://0.0.0.0:40610/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://0.0.0.0:40610/api/v1/namespaces/kube-system/services/https:metrics-server:https/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

Looking good!

4. After cluster01 is ready, create another cluster with the same registry or rebuild the cluster, it will use the already locally cached images.

```
$ k3d cluster create cluster02 --registry-use k3d-docker-io:5001 --registry-config registries.yaml
```

You may be prompted like:

```
INFO[0000] Prep: Network
INFO[0000] Created network 'k3d-cluster02'
INFO[0000] Created image volume k3d-cluster02-images
INFO[0000] Starting new tools node...
INFO[0000] Starting Node 'k3d-cluster02-tools'
INFO[0001] Creating node 'k3d-cluster02-server-0'
INFO[0001] Creating LoadBalancer 'k3d-cluster02-serverlb'
INFO[0001] Using the k3d-tools node to gather environment information
INFO[0001] HostIP: using network gateway 172.28.0.1 address
INFO[0001] Starting cluster 'cluster02'
INFO[0001] Starting servers...
INFO[0002] Starting Node 'k3d-cluster02-server-0'
INFO[0006] All agents already running.
INFO[0006] Starting helpers...
INFO[0006] Starting Node 'k3d-cluster02-serverlb'
INFO[0012] Injecting records for hostAliases (incl. host.k3d.internal) and for 3 network members into CoreDNS configmap...
INFO[0015] Cluster 'cluster02' created successfully!
INFO[0016] You can now use it like this:
kubectl cluster-info
```

Check it:

```
$ kubectl cluster-info
Kubernetes control plane is running at https://0.0.0.0:40834
CoreDNS is running at https://0.0.0.0:40834/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://0.0.0.0:40834/api/v1/namespaces/kube-system/services/https:metrics-server:https/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

Purrrrrrrrfect...



++++++++++ WE ARE HERE ++++++++++




### Creating a registry proxy / pull-through registry via configfile

1. Create a config file, e.g. ```~/test-regcache.yaml```

```
apiVersion: k3d.io/v1alpha4 
kind: Simple 
metadata: 
  name: test-regcache 
registries: 
  create: 
    name: docker-io # name of the registry container 
  proxy: remoteURL: https://registry-1.docker.io # proxy DockerHub 
  volumes: 
    - /tmp/reg:/var/lib/registry # persist data locally in /tmp/reg 
  config: | # tell K3s to use this registry when pulling from DockerHub 
    mirrors: "docker.io": 
      endpoint: - http://docker-io:5000
```
~/test-regcache.yaml

2. Create cluster from config:

```
$ k3d cluster create -c ~/test-regcache.yaml
```

Alternatively, using Skaffold, see https://devopsspiral.com/articles/k8s/k3d-skaffold/

More here ....


OR ALL FROM COMMAND LINE:

```
$ cd /k3d-headstart
$ setenv K3D_IMAGE_LOADBALANCER ghcr.io/k3d-io/k3d-proxy:sha-85841a1
$ setenv K3D_IMAGE_TOOLS ghcr.io/k3d-io/k3d-tools:5.4.4
$ sudo k3d cluster create mycluster --api-port 127.0.0.1:6443 -p "8081:80@loadbalancer" --agents 2 --registry-config "${PWD}/registries.yaml" --image my-docker-shared-images.my-artifactory.my-company.com/rancher/k3s:latest -e HTTP_PROXY="http://my-proxy.my-company.com:8080" -e HTTPS_PROXY="http://my-proxy.my-company.com:8080" --verbose
```

EXPERIMENTAL:
```
$ sudo k3d cluster create mycluster --registry-config "${PWD}/registries.yaml" -e "HTTP_PROXY=http://my-proxy.my-company.com:8080" -e "HTTPS_PROXY=http://my-proxy.my-company.com:8080" -e "NO_PROXY=localhost,127.0.0.1,0.0.0.0" --image my-docker-shared-images.my-artifactory.my-company.com/rancher/k3s:latest --trace
```

See also "How to Create a Kubernetes Cluster and Load Balancer for Local Development" at https://www.youtube.com/watch?v=8Moz07-__9k
