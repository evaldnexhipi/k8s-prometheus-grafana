def remote = [:]
remote.name = "ubuntu"
remote.allowAnyHosts = true
remote.host = "IP-ja juaj"
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
            stage("1"){
                
            }  
        }
    }
}
