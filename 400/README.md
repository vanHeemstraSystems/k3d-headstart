# 400 - Get & Install

**HIGHLY RECOMMENDED READING**: "Docker and Proxy" at https://medium.com/@bennyh/docker-and-proxy-88148a3f35f7

## 100 - Get

Use the install script to grab the latest release of the k3d CLI:

```
$ curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash
Preparing to install k3d into /usr/local/bin
[sudo] password for cloud_user: 
k3d installed into /usr/local/bin/k3d
Run 'k3d --help' to see what you can do with it.
```

NOTE: If for some reason you cannot download above script (e.g. you are behind a corporate proxy), you can try the version of the ```install.sh``` script found at the root of this directory. Make sure it is executable, then run it as follows:

```
$ cd /
$ chmod +x install.sh
$ sudo ./install.sh
```

If above times out, you may still not have enough credentials to go through the proxy.

Alternatively, use the k3d CLI binary from the root of this repository (note: original can be found at https://github.com/k3d-io/k3d/releases), k3d-v5-linux-amd64.

```
$ sudo chmod +x k3d-v5-linux-amd64
$ sudo chown root k3d-v5-linux-amd64
$ sudo chgrp root k3d-v5-linux-amd64
$ sudo install k3d-v5-linux-amd64 /usr/local/bin/k3d
```

**Note**: You can check you linux architecture type with ```uname -a```. Here we are targeting k3d-linux-amd64, because the 64-Bit processor of Intel originally came from a company called Advanced Micro Devices (hence, AMD).

Once installed the k3d CLI binary, the following can be tested:

```
$ cd /usr/local/bin
$ sudo k3d --version
$ k3d version v5.4.4
$ k3s version v1.23.8-k3s1 (default)
```

As you are excuting this k3d CLI binary as root and it is owned by root, and of the root group, the following should also work:

```
$ cd ~
$ sudo k3d --version
$ k3d version v5.4.4
$ k3s version v1.23.8-k3s1 (default)
```

Hooray, you are ready for the next part!

## 200 - Install
Installation is very easy and available through many installers: wget, curl, Homebrew, Aur, … and supports all well known OSes (linux, darwin, windows) and processor architectures (386, amd64) !

### Several installation options

You have several installation options there:

- use the install script to grab the latest release:
  - wget: `wget -q -O - https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash`
  - curl: `curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash`
- use the install script to grab a specific release (via `TAG` environment variable):
  - wget: `wget -q -O - https://raw.githubusercontent.com/rancher/k3d/main/install.sh | TAG=v4.0.0 bash`
  - curl: `curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | TAG=v4.0.0 bash`

- use [Homebrew](https://brew.sh): `brew install k3d` (Homebrew is available for MacOS and Linux)
  - Formula can be found in [homebrew/homebrew-core](https://github.com/Homebrew/homebrew-core/blob/master/Formula/k3d.rb) and is mirrored to [homebrew/linuxbrew-core](https://github.com/Homebrew/linuxbrew-core/blob/master/Formula/k3d.rb)
- install via [MacPorts](https://www.macports.org): `sudo port selfupdate && sudo port install k3d` (MacPorts is available for MacOS)
- install via [AUR](https://aur.archlinux.org/) package [rancher-k3d-bin](https://aur.archlinux.org/packages/rancher-k3d-bin/): `yay -S rancher-k3d-bin`
- grab a release from the [release tab](https://github.com/rancher/k3d/releases) and install it yourself.
- install via go: `go install github.com/rancher/k3d` (**Note**: this will give you unreleased/bleeding-edge changes)
- use [Chocolatey](https://chocolatey.org/): `choco install k3d` (Chocolatey package manager is available for Windows)
  - package source can be found in [erwinkersten/chocolatey-packages](https://github.com/erwinkersten/chocolatey-packages/tree/master/automatic/k3d)

or...

Note that you only need to install the k3d client, which will create a k3s cluster using the right Docker image.

Once installed, configure the completion with your preferred shell (bash, zsh, powershell), for instance with zsh:

k3d completion zsh > ~/.zsh/completions/_k3d
 source .zshrc

Check installation with:

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
