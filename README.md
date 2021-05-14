k3d-headstart
# k3d - Headstart

Based on "k3d" at https://github.com/rancher/k3d

Based on "K3S + K3D = K8S : a new perfect match for dev and test" at https://www-sokube-ch.cdn.ampproject.org/c/s/www.sokube.ch/amp/k3s-k3d-k8s-a-new-perfect-match-for-dev-and-test

k3d creates containerized k3s clusters. 

This means, that you can spin up a multi-node k3s cluster on a single machine using docker.

***Note***: k3s is the lightweight Kubernetes distribution by Rancher: rancher/k3s 

See also https://github.com/vanHeemstraSystems/k3s-dind-headstart

## 100 - Learning

- Website with documentation: [k3d.io](https://k3d.io/)

- [Rancher Meetup - May 2020 - Simplifying Your Cloud-Native Development Workflow With K3s, K3c and K3d (YouTube)](https://www.youtube.com/watch?v=hMr3prm9gDM)
- - k3d demo repository: [iwilltry42/k3d-demo](https://github.com/iwilltry42/k3d-demo)

See [README.md](./100/README.md)

## 200 - Requirements

See [README.md](./200/README.md)

## 300 - Releases

See [README.md](./300/README.md)

## 400 - Get & Install

See [README.md](./400/README.md)

## 500 - Build

See [README.md](./500/README.md)

## 600 - Usage

Check out what you can do via k3d help or check the docs @ k3d.io

Example Workflow: Create a new cluster and use it with kubectl

1. k3d cluster create CLUSTER_NAME to create a new single-node cluster (= 1 container running k3s + 1 loadbalancer container)
2. [Optional, included in cluster create] k3d kubeconfig merge CLUSTER_NAME --kubeconfig-switch-context to update your default kubeconfig and switch the current-context to the new one
3. execute some commands like kubectl get pods --all-namespaces
4. k3d cluster delete CLUSTER_NAME to delete the default cluster



Create a simple kubernetes cluster on your local machine

Create a multi-server (masters) and multi-agent (workers) kubernetes cluster on your local machine

Create a cluster with a specific Kubernetes version

How to replace the default CNI plugin of k3s

How to replace the default ingress controller of k3s

How to use a dedicated registry to download images with k3s

What are the other Alternatives

## 700 - Connect

## 800 - History

## 900 - Related Projects

- [k3x](https://github.com/inercia/k3x): GUI (Linux) to k3d
- [vscode-k3d](https://github.com/inercia/vscode-k3d): vscode plugin for k3d
- [AbsaOSS/k3d-action](https://github.com/AbsaOSS/k3d-action): fully customizable GitHub Action to run lightweight Kubernetes clusters.

## 1000 - Contributing

## 1100 - Contributors

