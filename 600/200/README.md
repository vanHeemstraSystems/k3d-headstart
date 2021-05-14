# 200 - Create a simple kubernetes cluster on your local machine

## 100 - with docker-compose (preferred)

See [README.md](./100/README.md)

## 200 - without docker-compose

Let’s create a simple cluster with 1 loadbalancer and 1 node (with role of server and agent) with name “dev”:

```$ k3d cluster create dev --port 8080:80@loadbalancer --port 8443:443@loadbalancer```

```docker ps``` will show the underlying containers created by this command:

```
$ docker ps

```

Check the current status with the previoulsy installed [Kubernetes Dashboard](https://github.com/vanHeemstraSystems/k3d-headstart/blob/main/600/100/100/README.md):

more ...
