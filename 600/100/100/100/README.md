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

***Note***: You can install ```k3s``` as a service, like so:

```
curl -sfL https://get.k3s.io | sh -
[sudo] password for cloud_user: 
[INFO]  Finding release for channel stable
[INFO]  Using v1.20.6+k3s1 as release
[INFO]  Downloading hash https://github.com/k3s-io/k3s/releases/download/v1.20.6+k3s1/sha256sum-amd64.txt
[INFO]  Downloading binary https://github.com/k3s-io/k3s/releases/download/v1.20.6+k3s1/k3s
[INFO]  Verifying binary download
[INFO]  Installing k3s to /usr/local/bin/k3s
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: d36uatko69830t.cloudfront.net
 * epel: mirrors.coreix.net
 * extras: d36uatko69830t.cloudfront.net
 * nux-dextop: mirror.li.nux.ro
 * updates: d36uatko69830t.cloudfront.net
Package yum-utils-1.1.31-54.el7_8.noarch already installed and latest version
Nothing to do
Loaded plugins: fastestmirror
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: d36uatko69830t.cloudfront.net
 * epel: mirrors.coreix.net
 * extras: d36uatko69830t.cloudfront.net
 * nux-dextop: mirror.li.nux.ro
 * updates: d36uatko69830t.cloudfront.net
rancher-k3s-common-stable                                        | 2.9 kB  00:00:00     
rancher-k3s-common-stable/primary_db                             | 2.2 kB  00:00:00     
Resolving Dependencies
--> Running transaction check
---> Package k3s-selinux.noarch 0:0.3-0.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

========================================================================================
 Package           Arch         Version           Repository                       Size
========================================================================================
Installing:
 k3s-selinux       noarch       0.3-0.el7         rancher-k3s-common-stable        14 k

Transaction Summary
========================================================================================
Install  1 Package

Total download size: 14 k
Installed size: 82 k
Downloading packages:
warning: /var/cache/yum/x86_64/7/rancher-k3s-common-stable/packages/k3s-selinux-0.3-0.el7.noarch.rpm: Header V4 RSA/SHA1 Signature, key ID e257814a: NOKEY
Public key for k3s-selinux-0.3-0.el7.noarch.rpm is not installed
k3s-selinux-0.3-0.el7.noarch.rpm                                 |  14 kB  00:00:01     
Retrieving key from https://rpm.rancher.io/public.key
Importing GPG key 0xE257814A:
 Userid     : "Rancher (CI) <ci@rancher.com>"
 Fingerprint: c8cf f216 4551 26e9 b9c9 18be 925e a29a e257 814a
 From       : https://rpm.rancher.io/public.key
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : k3s-selinux-0.3-0.el7.noarch                                         1/1 
  Verifying  : k3s-selinux-0.3-0.el7.noarch                                         1/1 

Installed:
  k3s-selinux.noarch 0:0.3-0.el7                                                        

Complete!
[INFO]  Creating /usr/local/bin/kubectl symlink to k3s
[INFO]  Creating /usr/local/bin/crictl symlink to k3s
[INFO]  Skipping /usr/local/bin/ctr symlink to k3s, command exists in PATH at /bin/ctr
[INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
[INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
[INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
[INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
[INFO]  systemd: Enabling k3s unit
Created symlink from /etc/systemd/system/multi-user.target.wants/k3s.service to /etc/systemd/system/k3s.service.
[INFO]  systemd: Starting k3s
Job for k3s.service failed because the control process exited with error code. See "systemctl status k3s.service" and "journalctl -xe" for details.
```

Check:

```
$ k3s --version
k3s version v1.20.6+k3s1 (8d043282)
go version go1.15.10
```

Now let's try deploying the Kubernetes Dashboard again, with k3s as a service now available:

```
$ sudo k3s kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml

```
