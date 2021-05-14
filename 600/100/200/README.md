# 200 - Try an example workflow

Example Workflow: Create a new cluster and use it with kubectl

1. k3d cluster create CLUSTER_NAME to create a new single-node cluster (= 1 container running k3s + 1 loadbalancer container)
2. [Optional, included in cluster create] k3d kubeconfig merge CLUSTER_NAME --kubeconfig-switch-context to update your default kubeconfig and switch the current-context to the new one
3. execute some commands like kubectl get pods --all-namespaces
4. k3d cluster delete CLUSTER_NAME to delete the default cluster
