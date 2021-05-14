# 100 - Deploying the Kubernetes Dashboard

## 100 - Set the GITHUB_URL environment variable

```
$ GITHUB_URL=https://github.com/kubernetes/dashboard/releases
```

Check:

```
$ echo $GITHUB_URL
https://github.com/kubernetes/dashboard/releases
```

## 200 - Set the VERSION_KUBE_DASHBOARD environment variable

```
$ VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
```

Check:

```
$ echo $VERSION_KUBE_DASHBOARD
v2.2.0
```

## 300 - Create the Kubernetes Dashboard

```
$ sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml
[sudo] password for cloud_user: 
sudo: k3s: command not found
```

TO DO: Find out why it says ```k3s: command not found```
