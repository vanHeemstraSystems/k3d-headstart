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

Change the permission of /etc/rancher/k3s/k3s.yaml (***note***: read ahead to find that below action is actually not required):

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
error: no context exists with the name: "k3d-dev"
```

***--- About kubeconfig ---***

Source: https://rancher.com/learning-paths/how-to-manage-kubernetes-with-kubectl/

In order to access your Kubernetes cluster, kubectl uses a configuration file. The default kubectl configuration file is located at ```~/.kube/config``` and is referred to as the kubeconfig file.

kubeconfig files organize information about clusters, users, namespaces, and authentication mechanisms. The kubectl command uses these files to find the information it needs to choose a cluster and communicate with it.

The loading order follows these rules:

- If the --kubeconfig flag is set, then only the given file is loaded. The flag may only be set once and no merging takes place.
- If the $KUBECONFIG environment variable is set, then it is parsed as a list of filesystem paths according to the normal path delimiting rules for your system.
- Otherwise, the ${HOME}/.kube/config file is used and no merging takes place.
- 
If you see a message similar to the following, kubectl is not configured correctly or is not able to connect to a Kubernetes cluster.

```The connection to the server <server-name: port> was refused - did you specify the right host or port?```

***---***

The content of ```~/.kube/config``` is as follows:

```
$ vi ~/.kube/config
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJkekNDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdGMyVnkKZG1WeUxXTmhRREUyTWpFd01EQTFPREl3SGhjTk1qRXdOVEUwTVRNMU5qSXlXaGNOTXpFd05URXlNVE0xTmpJeQpXakFqTVNFd0h3WURWUVFEREJock0zTXRjMlZ5ZG1WeUxXTmhRREUyTWpFd01EQTFPREl3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFScHZKNHU1cVlSS1ZFbE5zd3VpWTA4bGlmcDVzclZLL3RnMmN0SUNIL0QKNHZpNk94WGhqTExSdEkvaCtjTGVwajNjNVNWZGxsbFpMaEdlNzdYa2VMMDFvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVTg2OUR5cVFSTklOK0FDVytMeE1TCmVISTROK3N3Q2dZSUtvWkl6ajBFQXdJRFNBQXdSUUloQU5WZlJBOGF3TGRRQUd2WHRVSkxEVGZpOHd0blJ2czMKcC9wTlRnM1ZncjN5QWlBQ1pJQTQycy9DMGJ3T01WS1JaaHFKT0l1MG1hclBLL2tMam9PNi9DUExhUT09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    server: https://0.0.0.0:33168
  name: k3d-dev
contexts:
- context:
    cluster: k3d-dev
    user: admin@k3d-dev
  name: k3d-dev
current-context: k3d-dev
kind: Config
preferences: {}
users:
- name: admin@k3d-dev
  user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJrVENDQVRlZ0F3SUJBZ0lJS2U1ckJ5alhLbWd3Q2dZSUtvWkl6ajBFQXdJd0l6RWhNQjhHQTFVRUF3d1kKYXpOekxXTnNhV1Z1ZEMxallVQXhOakl4TURBd05UZ3lNQjRYRFRJeE1EVXhOREV6TlRZeU1sb1hEVEl5TURVeApOREV6TlRZeU1sb3dNREVYTUJVR0ExVUVDaE1PYzNsemRHVnRPbTFoYzNSbGNuTXhGVEFUQmdOVkJBTVRESE41CmMzUmxiVHBoWkcxcGJqQlpNQk1HQnlxR1NNNDlBZ0VHQ0NxR1NNNDlBd0VIQTBJQUJFd3hQVit6dHNyMDhic0YKTkl4V1VYdlRPUHFFUjQveTg0NFZHUVFHN0JEY3pWMVdqQytyYXZpbHZuS081ZkdtSkJnQnJTL0RxQ2w0cCtaYwpCOHp0bVh5alNEQkdNQTRHQTFVZER3RUIvd1FFQXdJRm9EQVRCZ05WSFNVRUREQUtCZ2dyQmdFRkJRY0RBakFmCkJnTlZIU01FR0RBV2dCUXpIQ0dsaXVRWTFJTlBmUHlKaytLQU1mZjNUREFLQmdncWhrak9QUVFEQWdOSUFEQkYKQWlFQXhDTFY1dzYwSER1eHdYY09RQXF1aDRjY0FRcStsR3p3Skw2T1E2dlA3YzBDSUVWYnpPVHJJZDF5RDIxbwpxaHdSR0RXTlhqSFRHN2pTREM2YXZ5ck9xdmc5Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0KLS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUJlRENDQVIyZ0F3SUJBZ0lCQURBS0JnZ3Foa2pPUFFRREFqQWpNU0V3SHdZRFZRUUREQmhyTTNNdFkyeHAKWlc1MExXTmhRREUyTWpFd01EQTFPREl3SGhjTk1qRXdOVEUwTVRNMU5qSXlXaGNOTXpFd05URXlNVE0xTmpJeQpXakFqTVNFd0h3WURWUVFEREJock0zTXRZMnhwWlc1MExXTmhRREUyTWpFd01EQTFPREl3V1RBVEJnY3Foa2pPClBRSUJCZ2dxaGtqT1BRTUJCd05DQUFTSHQ4dXVJS0IvQ3BNRU9WNEFCUWZHL2JNR0pmS2JlQllnek8wRTN6bzIKQ3VSL1ZjYVRDV0FTelhoc1E0QTJmeWJDSUUyWE1ESGhjRmZlOWl1dGtsNUlvMEl3UURBT0JnTlZIUThCQWY4RQpCQU1DQXFRd0R3WURWUjBUQVFIL0JBVXdBd0VCL3pBZEJnTlZIUTRFRmdRVU14d2hwWXJrR05TRFQzejhpWlBpCmdESDM5MHd3Q2dZSUtvWkl6ajBFQXdJRFNRQXdSZ0loQUxWY2gyb2JpeXVNSDJCcG9DeDRCL0ZRbnc1YlpKWkMKUXZFZ1B3dDFGUEZRQWlFQWpJbHlCZkp4Nk5EaU03dFVId2JPOWRlaWFoeTMrVWcyb2M2YW1jWXhhSVE9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    client-key-data: LS0tLS1CRUdJTiBFQyBQUklWQVRFIEtFWS0tLS0tCk1IY0NBUUVFSUJ6TjB2aWNIZVRtWTZiNnVrZHdja3VDeUVFcG9mSFdtOUNiaUFYYkpmOXVvQW9HQ0NxR1NNNDkKQXdFSG9VUURRZ0FFVERFOVg3TzJ5dlR4dXdVMGpGWlJlOU00K29SSGovTHpqaFVaQkFic0VOek5YVmFNTDZ0cQorS1crY283bDhhWWtHQUd0TDhPb0tYaW41bHdIek8yWmZBPT0KLS0tLS1FTkQgRUMgUFJJVkFURSBLRVktLS0tLQo=
```

You can see from above config file that the context 'k3s-dev' is known and that it is the current context:

```
...
contexts:
- context:
    cluster: k3d-dev
    user: admin@k3d-dev
  name: k3d-dev
current-context: k3d-dev
...
```
~/.kube/config

In sum, we must set the kubectl config command to look for ```~/.kube/config```, not ```/etc/rancher/k3s/k3s.yaml```

Now try again switching to the new context, with the ```--kubeconfig``` flag set to ```~/.kube/config```:

```
$ kubectl config use-context k3d-dev --kubeconfig ${HOME}/.kube/config
Switched to context "k3d-dev".
```

Success!!

Next, get the cluster info:

```
$ kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:6443
CoreDNS is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
Metrics-server is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

Validate the Docker processes:

```
$ docker ps
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                                                                                                     NAMES
69096ab4a139   rancher/k3d-proxy:v4.4.3   "/bin/sh -c nginx-pr…"   52 minutes ago   Up 50 minutes   0.0.0.0:8080->80/tcp, :::8080->80/tcp, 0.0.0.0:8443->443/tcp, :::8443->443/tcp, 0.0.0.0:33168->6443/tcp   k3d-dev-serverlb
6a6b7776b5de   rancher/k3s:v1.20.6-k3s1   "/bin/k3s server --t…"   52 minutes ago   Up 52 minutes                                                                                                             k3d-dev-server-0
```

The current cluster looks like in below picture:

![k3s-dev-img-63848328462834632](https://user-images.githubusercontent.com/12828104/118288400-b18ff480-b4d4-11eb-9c10-64b7a2465603.png)

## Port Mappings

- ```--port 8080:80@loadbalancer``` will add a mapping of local host port 8080 to loadbalancer port 80, which will proxy requests to port 80 on all agent nodes

- ```--api-port 6443``` : by default, no API-Port is exposed (no host port mapping).
 It’s used to have k3s‘s API-Server listening on port 6443 with that port mapped to the host system. So that the load balancer will be the access point to the Kubernetes API, so even for multi-server clusters, you only need to expose a single api port. The load balancer will then take care of proxying your requests to the appropriate server node.

- ```-p "32000-32767:32000-32767@loadbalancer"```
 You may as well expose a NodePort range (if you want to avoid the Ingress Controller).

## Kubeconfig

- By default it will directly switch the default kubeconfig's current-context to the new cluster's context so that your ```~/.kube/config``` is automatically updated. You can check it with ```$ kubectl config current-context```

- You can disable this behaviour by using flag ```--update-default-kubeconfig=false```, so it will need to create a kubeconfig file and export the KUBECONFIG var as follows:

```
$ export KUBECONFIG=$(k3d kubeconfig write dev)
```

- Removing the cluster will also delete the entry in the kubeconfig file.

- k3d provides some commands to easily manipulate the kubeconfig, as follows:

```
# get kubeconfig from cluster dev
$ k3d kubeconfig get dev
 
# create a kubeconfig file in $HOME/.k3d/kubeconfig-dev.yaml 
$ k3d kubeconfig write dev
 
# get kubeconfig from cluster(s) and
# merge it/them into a file in $HOME/.k3d or another file
$ k3d kubeconfig merge ...
```

## Lifecycle

Stopping a cluster is very easy:

```
$ k3d cluster stop dev
INFO[0000] Stopping cluster 'dev'  
```

Then Restarting and restoring the state of the cluster as it was before stopping:

```
$ k3d cluster start dev
INFO[0000] Starting cluster 'dev'                       
INFO[0000] Starting servers...                          
INFO[0000] Starting Node 'k3d-dev-server-0'             
INFO[0029] Starting agents...                           
INFO[0029] Starting helpers...                          
INFO[0029] Starting Node 'k3d-dev-serverlb' 
```

Deleting a cluster is as simple as:

```$ k3d cluster delete dev```

## Test with a simple nginx container application

Once the cluster running, execute the following commands to test with a simple nginx container:

```
$ kubectl create deployment nginx --image=nginx
deployment.apps/nginx created
```

```
$ kubectl create service clusterip nginx --tcp=80:80
service/nginx created
```

To test, browse to (will not show a page yet as there is no site yet, but NGinX is running):

```http://localhost:8080/```

Alternatively (will not show a page yet as there is no site yet, but NGinX is running):

Source: https://serverfault.com/questions/716180/how-to-access-web-page-via-terminal/893987

```
$ curl -I -L localhost:8080
curl: (52) Empty reply from server
```
___

Check the current status with the previously installed [Kubernetes Dashboard](https://github.com/vanHeemstraSystems/k3d-headstart/blob/main/600/100/100/README.md):

more ...
