# 400 - Get & Install

# 100 - Get
Use the install script to grab the latest release:

```
curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash
```

# 200 - Install
Installation is very easy and available through many installers: wget, curl, Homebrew, Aur, … and supports all well known OSes (linux, darwin, windows) and processor architectures (386, amd64) !

Note that you only need to install the k3d client, which will create a k3s cluster using the right Docker image.

Once installed, configure the completion with your preferred shell (bash, zsh, powershell), for instance with zsh:

k3d completion zsh > ~/.zsh/completions/_k3d
 source .zshrc
