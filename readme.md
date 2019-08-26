# Monitorimi i Prometheus per Kubernetes Cluster dhe Visualizimi i Grafanes

## Verifikimet
```bash
kubectl cluster-info
kubectl get nodes
kubectl version --short
clear
```
## Konfigurimi i file-ve te nevojshme para instalimit

### Konfigurimi i deployment.yaml
nga linku : 
[Deployment](https://github.com/justmeandopensource/kubernetes/blob/master/yamls/nfs-provisioner/deployment.yaml)
shkojme tek cdo <<NFS Server IP>> ku vendosim IP-ne e serverit tone

### Konfigurimi i class.yaml
nga linku:
[Class](https://github.com/justmeandopensource/kubernetes/blob/master/yamls/nfs-provisioner/class.yaml)
shtojme poshte :
```bash
name managed-nfs-storage
 ```
 frazen:
 ```bash
 annotations storageclass.kubernetes.io/is-default-class=true
 ```
 
 ### Marrja e rbac.yaml
 nga linku : 
 [Rbac](https://github.com/justmeandopensource/kubernetes/blob/master/yamls/nfs-provisioner/rbac.yaml)
 Marrim file-in rbac.yaml

