# Install K8s Dashboard

These notes are an almost exact copy of notes from [Rancher.com](https://rancher.com/docs/k3s/latest/en/installation/kube-dashboard/).

------

This installation guide will help you to deploy and configure the [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) on K3s.

### Deploying the Kubernetes Dashboard

```bash
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
wget -O kubernetes-dashboard.yml https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml
kubectl create -f kubernetes-dashboard.yaml
```

### Dashboard RBAC Configuration

> **IMPORTANT:**The `admin-user` created in this guide will have administrative privileges in the Dashboard.

Create the following resource manifest files:

##### dashboard.admin-user.yml

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```



##### dashboard.admin-user-role.yml

```yaml
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



Deploy the `admin-user` configuration:

```bash
kubectl create -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
```

### Obtain the Bearer Token

```bash
kubectl -n kubernetes-dashboard describe secret admin-user-token | grep ^token
```

### Local Access to the Dashboard

To access the Dashboard you must create a secure channel to your K3s cluster:

```bash
kubectl proxy
```

The Dashboard is now accessible at:

- [http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/)
- `Sign In` with the `admin-user` Bearer Token



### Advanced: Remote Access to the Dashboard

Please see the Dashboard documentation: Using [Port Forwarding](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) to Access Applications in a Cluster.

### Upgrading the Dashboard

```bash
kubectl delete ns kubernetes-dashboard
GITHUB_URL=https://github.com/kubernetes/dashboard/releases
VERSION_KUBE_DASHBOARD=$(curl -w '%{url_effective}' -I -L -s -S ${GITHUB_URL}/latest -o /dev/null | sed -e 's|.*/||')
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/${VERSION_KUBE_DASHBOARD}/aio/deploy/recommended.yaml -f dashboard.admin-user.yml -f dashboard.admin-user-role.yml
```

### Deleting the Dashboard and admin-user configuration

```bash
kubectl delete ns kubernetes-dashboard
```

