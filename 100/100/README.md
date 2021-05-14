# 100 - What is k3d/k3s

## 100 - k3s

### What is k3s?

- ***[k3s](https://rancher.com/docs/k3s/latest/en/)*** is a very efficient and lightweight fully compliant Kubernetes distribution.

- K3s is a lightweight, CNCF-certified Kubernetes distribution and Sandbox project. Designed for low-resource environments, K3s is distributed as a single binary that uses under 512MB of RAM. To learn more about K3s, head over to the [documentation](https://rancher.com/docs/k3s/latest/en/) or check out this [blog post](https://rancher.com/blog/2019/2019-02-26-introducing-k3s-the-lightweight-kubernetes-distribution-built-for-the-edge/) or [video](https://www.youtube.com/watch?v=hMr3prm9gDM). 

K3s includes:

- [Flannel](https://github.com/coreos/flannel#flannel): a very simple L2 overlay network that satisfies the Kubernetes requirements. This is a [CNI plugin](https://github.com/containernetworking/cni#3rd-party-plugins) (Container Network Interface), such as [Calico](http://docs.projectcalico.org/), [Romana](http://romana.io/), [Weave-net](https://www.weave.works/products/weave-net/).
  ***Note***: Flannel doesn’t support Kubernetes Network Policy, but it can be replaced by Calico (see next sections).

- [CoreDNS](https://coredns.io/): a flexible, extensible DNS server that can serve as the Kubernetes cluster DNS

- [Traefik](https://docs.traefik.io/) is a modern HTTP reverse proxy and load balancer.
 In a next section, I will also show how to replace it either by Traefik v2 or Nginx

- [Klipper Load Balancer](https://github.com/rancher/klipper-lb): Service load balancer that uses available host ports.

- [SQLite3](https://sqlite.org/): The storage backend used by default (also support MySQL, Postgres, and etcd3)

- [Containerd](https://containerd.io/): is a runtime container like Docker without the image build part

The choices of these components were made to have the most lightweight distribution. But as we will see later in this blog, k3s is a modular distribution where components can easily be replaced.

Recently k3s has joined the [Cloud Native Computing Foundation](https://sokube.atlassian.net/wiki/spaces/TEC/pages/218234881/NewsRoom) (CNCF) at the [sandbox level](https://www.cncf.io/sandbox-projects/) as first Kubernetes Distribution (raising a lot of [debates whether or not k3s should be a kubernetes sub-project instead](https://github.com/cncf/toc/pull/447)).

## 200 - k3d

### What is k3d?

- ***[k3d](https://k3d.io/)*** is a utility designed to easily run k3s in Docker. It provides a simple CLI to create, run, delete a fully compliance Kubernetes cluster with 1 to n nodes.

- k3d is a small program made for running a [K3s](https://k3s.io/) cluster in Docker. 

- k3d uses a [Docker image](https://hub.docker.com/r/rancher/k3s/tags) built from the [K3s repository](https://github.com/rancher/k3s) to spin up multiple K3s nodes in Docker containers on any machine with Docker installed. That way, a single physical (or virtual) machine (let’s call it Docker Host) can run multiple K3s clusters, with multiple server and agent nodes each, simultaneously.

### What can k3d do?

As of k3d version v4.0.0, released in January 2021, k3d’s abilities boil down to the following features:

- create/stop/start/delete/grow/shrink K3s clusters (and individual nodes)
- -- via command line flags
- -- via configuration file
- manage and interact with container registries that can be used with the cluster
- manage Kubeconfigs for the clusters
- import images from your local Docker daemon into the container runtime running in the cluster

Obviously, there’s way more to it and you can tweak everything in great detail.

### What is k3d used for?

- The main use case for k3d is local development on Kubernetes with little hassle and resource usage. The intention behind the initial development of k3d was to provide developers with an easy tool that allowed them to run a lightweight Kubernetes cluster on their development machine, giving them fast iteration times in a production-like environment (as opposed to running docker-compose locally vs. Kubernetes in production).

- Over time, k3d also evolved into a tool used by operations to test some Kubernetes (or, specifically K3s) features in an isolated environment. For example, with k3d you can easily create multi-node clusters, deploy something on top of it, simply stop a node and see how Kubernetes reacts and possibly reschedules your app to other nodes.

- Additionally, you can use k3d in your continuous integration system to quickly spin up a cluster, deploy your test stack on top of it and run integration tests. Once you’re finished, you can simply decommission the cluster as a whole. No need to worry about proper cleanups and possible leftovers.

- We also provide a [k3d-dind](https://github.com/vanHeemstraSystems/k3s-dind-headstart) image (similar to dreams within dreams in the movie Inception, we’ve got containers within containers within containers.) With that, you can create a docker-in-docker environment where you run k3d, which spawns a K3s cluster in Docker. That means that you only have a single container (k3d-dind) running on your Docker host, which in turn runs a whole K3s/Kubernetes cluster inside.
