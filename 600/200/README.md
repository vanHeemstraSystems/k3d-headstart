# 200 - Create a simple kubernetes cluster on your local machine

## 100 - with docker-compose (prefered)

Let’s create a simple cluster with 1 loadbalancer and 1 node (with role of server and agent) with name “???”:

```
$ cd containers/k3s/
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
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

## 200 - without docker-compose

Let’s create a simple cluster with 1 loadbalancer and 1 node (with role of server and agent) with name “dev”:

```$ k3d cluster create dev --port 8080:80@loadbalancer --port 8443:443@loadbalancer```

```docker ps``` will show the underlying containers created by this command:

```
$ docker ps

```
