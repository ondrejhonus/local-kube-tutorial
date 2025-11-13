# Kubernetes in Docker: Local Installation on Arch Linux

## Req

- <b>Docker</b> installed

## 1. Install kubectl and kind (KubernetesINDocker)

```sh
sudo pacman -S kubectl
```
```
paru -S kind
```
or
```
yay -S kind
```
## 2. Create a Kubernetes Cluster

```sh
kind create cluster
```

## 3. Get Dashboard

Install the dashboard:

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

## 4. Create admin user for dashboard

create file [dashboard-admin.yml](./dashboard-admin.yml)
```yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```
and file [dashboard-cluster.yml](./dashboard-cluster.yml)
```yaml
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kubernetes-dashboard
```

Forward the dashboard port:

```sh
kubectl proxy
```

Access it at: [http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)

## 7. Delete cluster

```sh
kind delete cluster
```

---

**References:**
- [kind documentation](https://kind.sigs.k8s.io/)
- [Kubernetes official docs](https://kubernetes.io/docs/)