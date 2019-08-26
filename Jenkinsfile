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
            
            }
            stage ("stage 3"){
            
            }
            stage ("stage 4"){
            
            }
            stage ("stage 5"){
            
            }
            stage ("stage 6"){
            
            }
            stage ("stage 7"){
            
            }
            stage ("stage 8"){
            
            }
            stage ("stage 9"){
            
            }
            stage ("stage 10"){
            
            }
            stage ("stage 11"){
            
            }
            stage ("stage 12"){
            
            }
        }  
    }
}
