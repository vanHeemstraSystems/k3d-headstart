k3d-headstart
# k3d - Headstart

Based on "k3d" at https://github.com/rancher/k3d

k3d creates containerized k3s clusters. 

This means, that you can spin up a multi-node k3s cluster on a single machine using docker.

***Note***: k3s is the lightweight Kubernetes distribution by Rancher: rancher/k3s 

See also https://github.com/vanHeemstraSystems/k3s-dind-headstart

## 100 - Learning

- Website with documentation: [k3d.io](https://k3d.io/)

- [Rancher Meetup - May 2020 - Simplifying Your Cloud-Native Development Workflow With K3s, K3c and K3d (YouTube)](https://www.youtube.com/watch?v=hMr3prm9gDM)
- - k3d demo repository: [iwilltry42/k3d-demo](https://github.com/iwilltry42/k3d-demo)

## 200 - Requirements

- Docker, see https://github.com/vanHeemstraSystems/docker-quick-start-headstart

## 300 - Releases

## 400 - Get

Use the install script to grab the latest release:

```
curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash
```

## 500 - Build

## 600 - Usage

Check out what you can do via k3d help or check the docs @ k3d.io

Example Workflow: Create a new cluster and use it with kubectl

1. k3d cluster create CLUSTER_NAME to create a new single-node cluster (= 1 container running k3s + 1 loadbalancer container)
2. [Optional, included in cluster create] k3d kubeconfig merge CLUSTER_NAME --kubeconfig-switch-context to update your default kubeconfig and switch the current-context to the new one
3. execute some commands like kubectl get pods --all-namespaces
4. k3d cluster delete CLUSTER_NAME to delete the default cluster

## 700 - Connect

## 800 - History

## 900 - Related Projects

- [k3x](https://github.com/inercia/k3x): GUI (Linux) to k3d
- [vscode-k3d](https://github.com/inercia/vscode-k3d): vscode plugin for k3d
- [AbsaOSS/k3d-action](https://github.com/AbsaOSS/k3d-action): fully customizable GitHub Action to run lightweight Kubernetes clusters.

## 1000 - Contributing

## 1100 - Contributors

