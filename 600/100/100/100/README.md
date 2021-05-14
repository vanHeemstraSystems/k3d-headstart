# 100 - Deploying the Kubernetes Dashboard

## 100 - Set the GITHUB_URL environment variable

```
$ GITHUB_URL=https://github.com/kubernetes/dashboard/releases
```

## 200 - Set the VERSION_KUBE_DASHBOARD environment variable

```
$ VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
```

## 300 - Create the Kubernetes Dashboard

```
$ sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml
```
