def remote = [:]
remote.name = "ubuntu"
remote.allowAnyHosts = true
remote.host = "IP-JA E INSTANCES NE AWS"
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
            sh 'rm -r yamlDirectory'
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
                sh 'helm init --service-account tiller --wait'
            }
            stage ("Waiting function"){
                  sh 'kubectl -n kube-system get pods'
                  sh 'sleep 30'
            }

            stage ("Prometheus Deployment"){
                //sh 'helm del --purge prometheus'
                sh 'helm repo update'
                sh 'helm install stable/prometheus --namespace monitoring --name prometheus --set server.service.type=LoadBalancer --set server.service.servicePort=8082'
            }

            stage("Defining the grafana data sources"){
                //sh 'helm del --purge grafana'
                sh 'kubectl apply -f https://raw.githubusercontent.com/evaldnexhipi/yamlFiles/master/monitoring/grafana/config.yml'
                sh 'kubectl get configmaps -n monitoring'
            }
            stage("Override Grafana value"){
                sh 'helm install stable/grafana --namespace monitoring --name grafana --set service.type=LoadBalancer --set service.port=8081 --set persistence.enabled=true'
                sh 'kubectl get pods -n monitoring'
            }
            stage("Get the Grafana Password"){
                sh 'kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}"| base64 --decode ; echo'
            }
            stage ("Testing"){
                sh 'kubectl describe service prometheus -n monitoring'
                sh 'kubectl describe service grafana -n monitoring'
            }
        }  
    }
}
