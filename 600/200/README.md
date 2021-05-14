# 200 - Create a simple kubernetes cluster on your local machine

Let’s create a simple cluster with 1 loadbalancer and 1 node (with role of server and agent) with name “dev”:

```$ k3d cluster create dev --port 8080:80@loadbalancer --port 8443:443@loadbalancer```

```docker ps``` will show the underlying containers created by this command:

```
$ docker ps

```
