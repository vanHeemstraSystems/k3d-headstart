# 600 - Usage

Check out what you can do via ```k3d --help``` or check the docs @ k3d.io

```
$ k3d --help
https://k3d.io/
k3d is a wrapper CLI that helps you to easily create k3s clusters inside docker.
Nodes of a k3d cluster are docker containers running a k3s image.
All Nodes of a k3d cluster are part of the same docker network.

Usage:
  k3d [flags]
  k3d [command]

Available Commands:
  cluster      Manage cluster(s)
  completion   Generate completion scripts for [bash, zsh, fish, powershell | psh]
  config       Work with config file(s)
  help         Help about any command
  image        Handle container images.
  kubeconfig   Manage kubeconfig(s)
  node         Manage node(s)
  registry     Manage registry/registries
  version      Show k3d and default k3s version

Flags:
  -h, --help         help for k3d
      --timestamps   Enable Log timestamps
      --trace        Enable super verbose output (trace logging)
      --verbose      Enable verbose output (debug logging)
      --version      Show k3d and default k3s version

Use "k3d [command] --help" for more information about a command.
```

## 100 - Install the Kubernetes Dashboard and try an example workflow

See [README.md](./100/README.md)

## 200 - Create a simple kubernetes cluster on your local machine

See [README.md](./200/README.md)

## 300 - Create a multi-server (masters) and multi-agent (workers) kubernetes cluster on your local machine

See [README.md](./300/README.md)

## 400 - Create a cluster with a specific Kubernetes version

See [README.md](./400/README.md)

## 500 - How to replace the default CNI plugin of k3s

See [README.md](./500/README.md)

## 600 - How to replace the default ingress controller of k3s

See [README.md](./600/README.md)

## 700 - How to use a dedicated registry to download images with k3s

See [README.md](./700/README.md)
