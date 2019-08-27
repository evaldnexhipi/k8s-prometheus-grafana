def remote = [:]
remote.name = "ubuntu"
remote.allowAnyHosts = true
remote.host = "IP"
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
            
            stage ("Deployment of files"){
                sh 'git clone https://github.com/evaldnexhipi/yamlDirectory.git'
                sh "sed -i \"s/<<NFS Server IP>>/\"${remote.host}\"/g\" yamlDirectory/deployment.yaml"
            }
            stage ("Deployment of the 3 files"){
                sh 'kubectl create -f yamlDirectory/deployment.yaml' 
                sh 'kubectl create -f yamlDirectory/class.yaml --validate=false'
                sh 'kubectl create -f yamlDirectory/rbac.yaml'
            }
            
            stage ("Helm Installation"){
                sh 'curl -LO https://git.io/get_helm.sh'
                sh 'chmod 700 get_helm.sh'
                sh './get_helm.sh'
            }
        
            stage ("Prometheus pre-Installation"){
               sh 'kubectl -n kube-system create serviceaccount tiller'
                sh 'kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller'
                sh 'kubectl get serviceaccounts -n kube-system'
                sh 'helm init'
                sh 'helm init --service-account tiller --wait'
            }
            stage ("Waiting function"){
                  sh 'kubectl -n kube-system get pods'
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
