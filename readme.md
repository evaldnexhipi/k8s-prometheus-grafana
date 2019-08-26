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

--> [Zevendesimi i Stringut](https://askubuntu.com/questions/20414/find-and-replace-text-within-a-file-using-commands)

### Konfigurimi i class.yaml
nga linku:
[Class](https://github.com/justmeandopensource/kubernetes/blob/master/yamls/nfs-provisioner/class.yaml)
shtojme poshte :
```bash
name managed-nfs-storage
 ```
 frazen:
 ```bash
 annotations: storageclass.kubernetes.io/is-default-class=true
 ```
 
 ose mund ta merrni te gatshme:
 ```bash
 apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: managed-nfs-storage
  annotations: storageclass.kubernetes.io/is-default-class=true
provisioner: example.com/nfs
parameters:
  archiveOnDelete: "false"
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
curl -LO https://git.io/get_helm.sh
chmod 700 get_helm.sh
./get_helm.sh
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
sipas komandes:
```bash
sed -i 's/type:ClusterIP/type:NodePort\nnodePort:32322/g' prometheus.values
```

#### Instalimi i Prometheus
 ```bash
 helm install stable/prometheus --name prometheus --values /tmp/prometheus.values --namespace prometheus
 ```
 
 #### <> Kerkohet nje tjeter funksion wait
 #### Kontrolli mbi instalimin me sukses te Prometheus
 ```bash
 kubectl get all -n prometheus
 ```

 ## Instalimi i Grafana-s
 #### Konfigurimi i vlerave paraprake te Grafana-s perpara instalimit:
 ```bash
 helm inspect values stable/grafana > /tmp/grafana.values
 ```
 Duhet edituar:
 ```bash
 service:
    type: NodePort
    nodePort: 32333
 ```
 
 Duhet edituar:
 ```bash
 adminPassword (sipas deshires)
 ```
 
 Duhet edituar:
 ```bash
 persistence:
    enabled:true
 ```
 #### Instalimi i Grafana
 ```bash
 helm install stable/grafana --name grafana --values /tmp/grafana.values --namespace grafana
 ```
 #### Username-i default eshte admin ndersa passwordi ai qe konfigurat me siper!
 
 ### Fshirja (opsionale)
 ```bash
 helm delete prometheus --purge
 helm delete grafana --purge
 ```
