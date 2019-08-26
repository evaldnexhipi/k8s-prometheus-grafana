def remote = [:]
remote.name = "ubuntu"
remote.allowAnyHosts = true
remote.host = "IP-ja"
def ID
def IP
node{
    withCredentials([sshUserPrivateKey(credentialsId: 'aID', keyFileVariable: 'identity', passphraseVariable: '', usernameVariable: 'userName')]) {
        remote.user = userName
        remote.identityFile = identity
        withCredentials(
        [[
            $class: 'AmazonWebServicesCredentialsBinding',
            accessKeyVariable: 'AWS_ACCESS_KEY_ID',
            credentialsId: '877d75d7-ee4e-4339-bd48-2615c7a5b349',  // ID of credentials in kubernetes
            secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
        ]]) 
        {
            stage ("Verifications"){
                sh 'kubectl cluster-info'
                sh 'kubectl get nodes'
                sh 'kubectl version --short'
            }
            stage ("Deployment.yaml Configuration"){
                sh 'touch deployment.yaml'
                sh 'echo "kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: nfs-client-provisioner
spec:
  replicas: 1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: nfs-client-provisioner
    spec:
      serviceAccountName: nfs-client-provisioner
      containers:
        - name: nfs-client-provisioner
          image: quay.io/external_storage/nfs-client-provisioner:latest
          volumeMounts:
            - name: nfs-client-root
              mountPath: /persistentvolumes
          env:
            - name: PROVISIONER_NAME
              value: example.com/nfs
            - name: NFS_SERVER
              value: <<NFS Server IP>>
            - name: NFS_PATH
              value: /srv/nfs/kubedata
      volumes:
        - name: nfs-client-root
          nfs:
            server: <<NFS Server IP>>
            path: /srv/nfs/kubedata" >> deployment.yaml'
            
            sh 'sed -i "s/<<NFS Server IP>>/${remote.host}/g" deployment.yaml'
            }
            stage ("Class.yaml Configuration"){
                sh 'touch class.yaml'
                sh 'echo "apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
 name: managed-nfs-storage
 annotations: storageclass.kubernetes.io/is-default-class=true
provisioner: example.com/nfs
parameters:
 archiveOnDelete: \"false\"" >> class.yaml'
            }
            stage ("rback.yaml Configuration"){
                sh 'git clone https://github.com/evaldnexhipi/yamlFile.git'
            }
            stage ("Deployment of the 3 files"){
                sh 'kubectl create -f deployment.yaml class.yaml yamlFile/rbac.yaml'
            }
            stage ("Helm Installation"){
                sh 'curl -LO https://git.io/get_helm.sh'
                sh 'chmod 700 get_helm.sh'
                sh './get_helm.sh'
            }
            stage ("Prometheus pre-Installation"){
                sh 'kubectl -n kube-system create service account tiller'
                sh 'kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller'
                sh 'helm init --service-account tiller'
            }
            stage ("Waiting function"){
                /*
                    TODO - Not done yet
                */
            }
            stage ("Prometheus Configuration"){
                sh 'helm inspect values stable/prometheus > /tmp/prometheus.values'
                sh 'sed -i "s/type:ClusterIP/type:NodePort\nnodePort:32322/g" prometheus.values'

            }
            stage ("Prometheus Installation"){
                sh 'helm install stable/prometheus --name prometheus --values /tmp/prometheus.values --namespace prometheus'
                sh 'kubectl get all -n prometheus'
            }

            stage ("Grafana Configuration"){
                sh 'helm inspect values stable/grafana > /tmp/grafana.values'
                /*
                    TODO editimi i vlerave dhe passwordi dhe persistence
                */
            }
            stage ("Grafana Installation"){
                sh 'helm install stable/grafana --name grafana --values /tmp/grafana.values --namespace grafana'
            }
        }  
    }
}
