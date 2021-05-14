# 400 - Get & Install

# 100 - Get
Use the install script to grab the latest release:

```
$ curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash
Preparing to install k3d into /usr/local/bin
[sudo] password for cloud_user: 
k3d installed into /usr/local/bin/k3d
Run 'k3d --help' to see what you can do with it.
```

You have several more options there:

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

# 200 - Install
Installation is very easy and available through many installers: wget, curl, Homebrew, Aur, … and supports all well known OSes (linux, darwin, windows) and processor architectures (386, amd64) !

Note that you only need to install the k3d client, which will create a k3s cluster using the right Docker image.

Once installed, configure the completion with your preferred shell (bash, zsh, powershell), for instance with zsh:

k3d completion zsh > ~/.zsh/completions/_k3d
 source .zshrc
