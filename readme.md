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
shkojme tek cdo "NFS Server IP" , ku vendosim IP-ne e serverit tone

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

### I bejme Deploy tre file-ve te mesiperme me komanden:
```bash
kubectl create -f deployment.yaml class.yaml rbac.yaml
```

## Instalimi i helm
```bash
(will be updated soon :))
```

## Instalimi i Prometheus
#### Krijimi i service account tiller
```bash
kubectl -n kube-system create service account tiller
```

#### Krijimi i clusterrolebinding
```bash
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
 ```
 #### Inicializimi
 ```bash
 helm init --service-account tiller
 ```
 
 #### <> Duhet kryer nje funksion qe te realizoje pritjen
 
 #### Konfigurimi i vlerave paraprake te Prometheus perpara instalimit:
 ```bash
 helm inspect values stable/prometheus > /tmp/prometheus.values
 ```
 me vlerat
 ```bash
    nodePort:32322
    type: NodePort (ne vend te ClusterIP)
 ```
 #### Instalimi i Prometheus
 ```bash
 helm install stable/prometheus --name prometheus --values /tmp/prometheus.values --namespace prometheus
 ```
 
 #### <<>> Kerkohet nje tjeter funksion wait
 #### Kontrolli mbi instalimin me sukses te Prometheus
 ```bash
 kubectl get all -n prometheus
 ```
 
 
 
 
