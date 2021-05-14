# 200 - Create a simple kubernetes cluster on your local machine

## 100 - with docker-compose (preferred)

See also https://rancher.com/docs/k3s/latest/en/advanced/#running-k3d-k3s-in-docker-and-docker-compose

Let’s create a simple cluster with 1 loadbalancer and 1 node (with role of server and agent) with name “dev”:

First, check any running Docker processes:

```
$ cd containers/k3s/
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

Check the k3d and k3s versions installed (***note*** that you cannot just ask for the k3s version, it is included with k3d version command):

```
$ k3d version
k3d version v4.4.3
k3s version v1.20.6-k3s1 (default)
```

Optionally, set the k3s image version to be used as an environment variable K3S_VERSION (as it will be used in Dockerfile):

```
$ K3S_VERSION=4
```

Run docker-compose with a required randomized k3s token:

```
$ K3S_TOKEN=${RANDOM}${RANDOM}${RANDOM} docker-compose up -d
Building with native build. Learn about native build in Compose here: https://docs.docker.com/go/compose-native-build/
Creating network "k3s_default" with the default driver
Creating volume "k3s_k3s-server" with default driver
Building server
Sending build context to Docker daemon   5.12kB

Step 1/1 : FROM rancher/k3s:${K3S_VERSION:-latest}
latest: Pulling from rancher/k3s
df34e409201e: Pulling fs layer
f60fca5628bc: Pulling fs layer
9edc3d691c12: Pulling fs layer
f60fca5628bc: Verifying Checksum
f60fca5628bc: Download complete
9edc3d691c12: Download complete
df34e409201e: Verifying Checksum
df34e409201e: Download complete
df34e409201e: Pull complete
f60fca5628bc: Pull complete
9edc3d691c12: Pull complete
Digest: sha256:11c9629458f5f1dda920b201000ebc6b949737f7f07fc1c10a760a3f04ce1ab5
Status: Downloaded newer image for rancher/k3s:latest
 ---> a7abdf1941d5
Successfully built a7abdf1941d5
Successfully tagged k3s_server:latest
WARNING: Image for service server was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Building agent
Sending build context to Docker daemon   5.12kB

Step 1/1 : FROM rancher/k3s:${K3S_VERSION:-latest}
 ---> a7abdf1941d5
Successfully built a7abdf1941d5
Successfully tagged k3s_agent:latest
WARNING: Image for service agent was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Creating k3s_server_1 ... done
Creating k3s_agent_1  ... done
```

Check it with:

```
$ docker ps
CONTAINER ID   IMAGE        COMMAND             CREATED         STATUS         PORTS                                       NAMES
0d510640f3a3   k3s_agent    "/bin/k3s agent"    9 minutes ago   Up 8 minutes                                               k3s_agent_1
9c72c0059ee4   k3s_server   "/bin/k3s server"   9 minutes ago   Up 8 minutes   0.0.0.0:6443->6443/tcp, :::6443->6443/tcp   k3s_server_1
```

Success!!

See which options (commands and flags) the k3d cluster command has:

```
$ k3d cluster --help
Manage cluster(s)

Usage:
  k3d cluster [flags]
  k3d cluster [command]

Available Commands:
  create      Create a new cluster
  delete      Delete cluster(s).
  list        List cluster(s)
  start       Start existing k3d cluster(s)
  stop        Stop existing k3d cluster(s)

Flags:
  -h, --help   help for cluster

Global Flags:
      --timestamps   Enable Log timestamps
      --trace        Enable super verbose output (trace logging)
      --verbose      Enable verbose output (debug logging)

Use "k3d cluster [command] --help" for more information about a command.
```

Let's use the following command to list the cluster(s) we have created:

```
$ k3d cluster list
NAME   SERVERS   AGENTS   LOADBALANCER
```

Hence, no clusters have yet been created.

Check which docker images got downloaded and created:

```
$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
k3s_server    latest    a7abdf1941d5   2 weeks ago    172MB
rancher/k3s   latest    a7abdf1941d5   2 weeks ago    172MB
k3s_agent     latest    a7abdf1941d5   2 weeks ago    172MB
```

more ...

## 200 - without docker-compose

Let’s create a simple cluster with 1 loadbalancer and 1 node (with role of server and agent) with name “dev”:

```$ k3d cluster create dev --port 8080:80@loadbalancer --port 8443:443@loadbalancer```

```docker ps``` will show the underlying containers created by this command:

```
$ docker ps

```

more ...
