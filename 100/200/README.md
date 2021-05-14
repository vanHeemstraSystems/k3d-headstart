# 200 - What's new with k3d v3

In one year, the k3d team did a great job and completely rewrote k3d v3. It is therefore not a simple major version, they have implemented new concepts and structures to make it an evolving tool with very practical and interesting features.

- New terminology of k3d and k3s: \n To be as inclusive to the community as possible, "Server" and "Agent" words are now used to design "master" and "worker" node.

- Every cluster you create will now spawn at least 2 containers: 1 load balancer and 1 “server” node.
- -- The load balancer will be the access point to the Kubernetes API, so even for multi-server clusters, you only need to expose a single api port. The load balancer will then take care of proxying your requests to the correct server node. (can be disabled with the --no-lb flag)

- Adoption of the “NOUN VERB” syntax:
- -- This breaking change makes it easier to add new nouns (i.e. k3d managed objects) and is similar to many other cloud-native CLIs (e.g. gcloud, awscli, azure cli, ...) and also provides a cleaner CLI hierarchy.

- Support of multi-server clusters (dqlite) with hot-reloads configuration when a new server node is being added to the cluster

- Handling nodes independently from clusters: k3d create/start/stop/delete node mynode

- Shell completion via k3d completion [zsh | bash | psh | fish]

- Basic plugin system support (k3d my-plugin)

- …
