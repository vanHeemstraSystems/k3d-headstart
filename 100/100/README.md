# 100 - What is k3d/k3s

- ***[k3s](https://rancher.com/docs/k3s/latest/en/)*** is a very efficient and lightweight fully compliant Kubernetes distribution.
- ***[k3d](https://k3d.io/)*** is a utility designed to easily run k3s in Docker. It provides a simple CLI to create, run, delete a fully compliance Kubernetes cluster with 1 to n nodes.

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
