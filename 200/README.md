# 200 - Requirements

- Docker, see https://github.com/vanHeemstraSystems/docker-quick-start-headstart
- (Optional) docker-compose, see https://github.com/vanHeemstraSystems/docker-compose-quick-start-headstart

See also [Ranger k3s: Installation Requirements](https://rancher.com/docs/k3s/latest/en/installation/installation-requirements/)

- kubectl (Optional), see https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

- k3d v5.x.x requires at least Docker v20.10.5 (runc >= v1.0.0-rc93) to work properly.

```
$ docker version
```

## Get kubectl

Grab the latest release of kubectl:

```
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Download the kubectl checksum file:

```
$ curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```

Validate the kubectl binary against the checksum file:

```
$ echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

If valid, the output is:

```
kubectl: OK
```

If the check fails, sha256 exits with nonzero status and prints output similar to:

```
kubectl: FAILED
sha256sum: WARNING: 1 computed checksum did NOT match
```

**NOTE**: Download the same version of the binary and checksum.

NOTE: If for some reason you cannot download above binary (e.g. you are behind a corporate proxy), you can try the version of the binary found at the root of this repository (note: original can be found at https://dl.k8s.io/release/v1.24.0/bin/linux/amd64/kubectl), kubectl. Latest version can be seen at https://kubernetes.io/releases/

## Install kubectl

```
$ sudo chmod a+x kubectl
$ sudo chown root kubectl
$ sudo chgrp root kubectl
$ sudo install kubectl /usr/local/bin/kubectl
```

**Note**: You can check you linux architecture type with ```uname -a```. Here we are targeting amd64, because the 64-Bit processor of Intel originally came from a company called Advanced Micro Devices (hence, AMD).

**Note**: If you do not have root access on the target system, you can still install kubectl to the ```~/.local/bin``` directory:

```
$ chmod +x kubectl
$ mkdir -p ~/.local/bin
$ mv ./kubectl ~/.local/bin/kubectl
# and then append (or prepend) ~/.local/bin to $PATH
```

Once installed the kubectl binary, the following can be tested:

```
$ cd /usr/local/bin
$ sudo kubectl version --output=yaml
clientVersion:
  buildDate: "2022-05-03T13:46:05Z"
  compiler: gc
  gitCommit: 4ce5a8954017644c5420bae81d72b09b735c21f0
  gitTreeState: clean
  gitVersion: v1.24.0
  goVersion: go1.18.1
  major: "1"
  minor: "24"
  platform: linux/amd64
kustomizeVersion: v4.5.4

error: Get "http://localhost:8080/version?timeout=32s": read tcp 127.0.0.1:34658->127.0.0.1:8080: read: connection reset by peer - error from a previous attempt: read tcp 127.0.0.1:34654->127.0.0.1:8080: read: connection reset by peer
```

As you are executing this kubectl binary as root and it is owned by root, and of the root group, the following should also work:

```
$ cd ~
$ sudo kubectl version --output=yaml
clientVersion:
  buildDate: "2022-05-03T13:46:05Z"
  compiler: gc
  gitCommit: 4ce5a8954017644c5420bae81d72b09b735c21f0
  gitTreeState: clean
  gitVersion: v1.24.0
  goVersion: go1.18.1
  major: "1"
  minor: "24"
  platform: linux/amd64
kustomizeVersion: v4.5.4

error: Get "http://localhost:8080/version?timeout=32s": read tcp 127.0.0.1:34658->127.0.0.1:8080: read: connection reset by peer - error from a previous attempt: read tcp 127.0.0.1:34654->127.0.0.1:8080: read: connection reset by peer
```

**WARNING**: Above error needs to be fixed ....

Create ```k3s.yaml``` file at ```/etc/rancher/k3s/k3s.yaml``` with the following content:

```
.... Add details here ...!
```

Manual configuration **KUBECONFIG** environment variable

To settle temporarily : ```export KUBECONFIG=/etc/rancher/k3s/k3s.yaml```

Permanent solution :

Perform : ```vi /etc/profile```
Write content : ```export KUBECONFIG=`/etc/rancher/k3s/k3s.yaml` ```
Perform : ```source /etc/profile```

Hooray, you are ready for the next part!
