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
- [kind docs](https://kind.sigs.k8s.io/)
- [kube docs](https://kubernetes.io/docs/)

# Kubernetes: Create and inject a ConfigMap
### Example configmap:
create [configmap.yml](./test-configmap.yml)
```yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: test-configmap
  namespace: default
data:
  app.properties: |
    key1=value1
    key2=value2
  config.json: |
    {
      "settingA": "foo",
      "settingB": "bar"
    }
```

## Apply the configmap:
```sh
kubectl apply -f configmap.yml
```

optionally check it:
```sh
kubectl get configmap test-configmap -o yaml
```

## Use the ConfigMap in a Pod
Mount as environment variables

create file [mount-as-env.yml](./mount-as-env.yml)
```yaml
envFrom:
    - configMapRef:
            name: test-configmap
```

### Mount it as files

create file [mount-as-files.yml](./mount-as-files.yml)
```yaml
volumes:
    - name: config-volume
        configMap:
            name: test-configmap
containers:
    - name: your-container
        volumeMounts:
            - name: config-volume
                mountPath: /etc/config
```

this will make the CM data available at ```/etc/config``` in the container.

### Update the ConfigMap
```sh
kubectl apply -f configmap.yml
```

**references:**  
- [kube configmaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)