# 500 - Build

1. Clone this repo, e.g. via `git clone git@github.com:rancher/k3d.git` or `go get github.com/rancher/k3d/v4@main`
2. Inside the repo run
   - 'make install-tools' to make sure required go packages are installed
3. Inside the repo run one of the following commands
   - `make build` to build for your current system
   - `go install` to install it to your `GOPATH` (**Note**: this will give you unreleased/bleeding-edge changes)
   - `make build-cross` to build for all systems
