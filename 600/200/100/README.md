# 100 - Create a simple cluster on your local machine with docker-compose (preferred)

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
$ export K3S_VERSION=4
```

Check it:

```
$ echo $K3S_VERSION
4
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

The kubeconfig.yaml file is written to the curent directory:

```
$ pwd
/home/cloud_user/git/k3d-headstart/containers/k3s
$ ls -la
-rw-rw-rw-. 1 root       root       2961 May 14 08:36 kubeconfig.yaml
```

See the content of kubeconfig.yaml:

```
$ vi kubeconfig.yaml
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJkekNDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdGMyVnkKZG1WeUxXTmhRREUyTWpBNU9ERXpPVGt3SGhjTk1qRXdOVEUwTURnek5qTTVXaGNOTXpFd05URXlNRGd6TmpNNQpXakFqTVNFd0h3WURWUVFEREJock0zTXRjMlZ5ZG1WeUxXTmhRREUyTWpBNU9ERXpPVGt3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFUbW5tM2xqaERJdkpubXhQd2JoL3dRVVVlR1g5dm5KSWZSbUdTSU9WRzkKY0VEb3ZyaGFPRFdwWEI0WUZUYUZFWEFyQ0IwSjBVOTd5TzZyQldIeUJ6U3NvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVVdBYzJYSE5YVGxlSWRwdkczYnNvClM2bXRXQU13Q2dZSUtvWkl6ajBFQXdJRFNBQXdSUUloQU1pWGpXZFJaZnZMUkkyZGhPQnVCdVczaUh3dElyRkwKYTgzenFERTV5c0NGQWlBVklHcUd1eVhOUjNyT0hFY1czVUVwYW9iY2lYeVVHVHptVDN3eHd5enVUUT09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://127.0.0.1:6443
  name: default
contexts:
- context:
    cluster: default
    user: default
  name: default
current-context: default
kind: Config
preferences: {}
users:
- name: default
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJrVENDQVRlZ0F3SUJBZ0lJTi90TWp1eWg0NlV3Q2dZSUtvWkl6ajBFQXdJd0l6RWhNQjhHQTFVRUF3d1kKYXpOekxXTnNhV1Z1ZEMxallVQXhOakl3T1RneE16azVNQjRYRFRJeE1EVXhOREE0TXpZek9Wb1hEVEl5TURVeApOREE0TXpZek9Wb3dNREVYTUJVR0ExVUVDaE1PYzNsemRHVnRPbTFoYzNSbGNuTXhGVEFUQmdOVkJBTVRESE41CmMzUmxiVHBoWkcxcGJqQlpNQk1HQnlxR1NNNDlBZ0VHQ0NxR1NNNDlBd0VIQTBJQUJKZDA0eGNhdkVNU0RsWnEKdG1SSDluSkFsUnJCVkpTTjZIVnpvbWNYV0tVUlhodjFjSUpRYXNkVWxUamo4K29IZjk3MmdDY01mM2ptZFlTZwpHQlRRWWJDalNEQkdNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBakFmCkJnTlZIU01FR0RBV2dCVDVjdVM2Skp0cjB0ajJDTEhSS0F1SkJIbncwakFLQmdncWhrak9QUVFEQWdOSUFEQkYKQWlCYUl3ZzhTQXVNSjdKNnFsMFVTWGwvN29wb1hFWlU4Q2h3V1lEbVNWYy9Od0loQU9NbHpXeGhWcUJ5Qll2Lwp3amJ0cHFTMitOWEM2Yy8wQUtqRDVwQTgwbnQzCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KLS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJkekNDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdFkyeHAKWlc1MExXTmhRREUyTWpBNU9ERXpPVGt3SGhjTk1qRXdOVEUwTURnek5qTTVXaGNOTXpFd05URXlNRGd6TmpNNQpXakFqTVNFd0h3WURWUVFEREJock0zTXRZMnhwWlc1MExXTmhRREUyTWpBNU9ERXpPVGt3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFRd2EyRTdTRUNEYlovUEdqSGtLQnRQTUxqd1JocnFad1JsQnB6c0l1dEQKb0JRZHpQR09TS3hHajhBaHI2MCszK3ZlZFpaUWcweEVyNnZRWTE3VmdPdGlvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVStYTGt1aVNiYTlMWTlnaXgwU2dMCmlRUjU4Tkl3Q2dZSUtvWkl6ajBFQXdJRFNBQXdSUUlnVkdKSlZiZTBPc0tsR1JmTTA2cnhhMWtwWDdHS3Ewb04KTkpsQkh2V0J5UmdDSVFDcXUzdXp2OGNqVzBYM0lsdFhsWlJOd0pHSnhrYi9KQlg1SGxxVXMvdEl4dz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    client-key-data: LS0tLS1CRUdJTiBFQyBQUklWQVRFIEtFWS0tLS0tCk1IY0NBUUVFSUNMSDhGRmE0RVE3dlNiVnQyM2d5YnlZR0VJR2JDelBJeGFuTWZzS2ZsNHZvQW9HQ0NxR1NNNDkKQXdFSG9VUURRZ0FFbDNUakZ4cThReElPVm1xMlpFZjJja0NWR3NGVWxJM29kWE9pWnhkWXBSRmVHL1Z3Z2xCcQp4MVNWT09QejZnZC8zdmFBSnd4L2VPWjFoS0FZRk5CaHNBPT0KLS0tLS1FTkQgRUMgUFJJVkFURSBLRVktLS0tLQo=
```

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

Hence, no clusters have yet been created/are running.

Check which docker images got downloaded and created:

```
$ docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
k3s_server    latest    a7abdf1941d5   2 weeks ago    172MB
rancher/k3s   latest    a7abdf1941d5   2 weeks ago    172MB
k3s_agent     latest    a7abdf1941d5   2 weeks ago    172MB
```

Check the current status with the previoulsy installed [Kubernetes Dashboard](https://github.com/vanHeemstraSystems/k3d-headstart/blob/main/600/100/100/README.md):


more ...
