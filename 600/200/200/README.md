# 200 - without docker-compose

Let’s create a simple cluster with 1 loadbalancer and 1 node (with role of server and agent) with name “dev”:

```
$ k3d cluster create dev --port 8080:80@loadbalancer --port 8443:443@loadbalancer
INFO[0000] Prep: Network                                
INFO[0000] Created network 'k3d-dev' (140208124f2f8299b42435ef8a85804adcb97f164d7b4ee751f7f5c24eee8519) 
INFO[0000] Created volume 'k3d-dev-images'              
INFO[0001] Creating node 'k3d-dev-server-0'             
INFO[0003] Pulling image 'docker.io/rancher/k3s:v1.20.6-k3s1' 
INFO[0021] Creating LoadBalancer 'k3d-dev-serverlb'     
INFO[0023] Pulling image 'docker.io/rancher/k3d-proxy:v4.4.3' 
INFO[0030] Starting cluster 'dev'                       
INFO[0030] Starting servers...                          
INFO[0030] Starting Node 'k3d-dev-server-0'             
INFO[0084] Starting agents...                           
INFO[0084] Starting helpers...                          
INFO[0084] Starting Node 'k3d-dev-serverlb'             
INFO[0131] (Optional) Trying to get IP of the docker host and inject it into the cluster as 'host.k3d.internal' for easy access 
INFO[0241] Successfully added host record to /etc/hosts in 2/2 nodes and to the CoreDNS ConfigMap 
INFO[0241] Cluster 'dev' created successfully!          
INFO[0241] --kubeconfig-update-default=false --> sets --kubeconfig-switch-context=false 
INFO[0252] You can now use it like this:                
kubectl config use-context k3d-dev
kubectl cluster-info
```

```docker ps``` will show the underlying containers created by this command:

```
$ docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS         PORTS                                                                                                     NAMES
69096ab4a139   rancher/k3d-proxy:v4.4.3   "/bin/sh -c nginx-pr…"   7 minutes ago   Up 6 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp, 0.0.0.0:8443->443/tcp, :::8443->443/tcp, 0.0.0.0:33168->6443/tcp   k3d-dev-serverlb
6a6b7776b5de   rancher/k3s:v1.20.6-k3s1   "/bin/k3s server --t…"   8 minutes ago   Up 7 minutes                                                                                                             k3d-dev-server-0
```

We can now use the new cluster by first switching to the new context 'k3d-dev':

```               
$ kubectl config use-context k3d-dev
WARN[2021-05-14T14:07:54.750535974Z] Unable to read /etc/rancher/k3s/k3s.yaml, please start server with --write-kubeconfig-mode to modify kube config permissions 
error: error loading config file "/etc/rancher/k3s/k3s.yaml": open /etc/rancher/k3s/k3s.yaml: permission denied
```

Change the permission of /etc/rancher/k3s/k3s.yaml:

```
$ cd /etc/rancher/k3s/
-rw-------. 1 root root 2961 May 14 13:09 k3s.yaml
$ sudo chmod 644 /etc/rancher/k3s/k3s.yaml
$ ls -la
-rw-r--r--. 1 root root 2961 May 14 13:10 k3s.yaml
```

Now try again switching to the new context:

```
$ kubectl config use-context k3d-dev
```

Next, get the cluster info:

```
$ kubectl cluster-info

```

Check the current status with the previoulsy installed [Kubernetes Dashboard](https://github.com/vanHeemstraSystems/k3d-headstart/blob/main/600/100/100/README.md):

more ...
