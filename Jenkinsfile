@Library('google-chat-repo@1.0')
def GOOGLE_CHAT_URL = 'https://chat.googleapis.com/v1/spaces/AAAAC0fQyiA/messages?key=AIzaSyDdI0hCZtE6vySjMm-WEfRq3CPzqKqqsHI&token=kW_raPlXqUnVgQDBfymf8A0IhPtaHwQNfoekzNPrIN8%3D'

pipeline {
    agent any
    
    stages {

        stage('Get Source'){
            steps {
                git url: 'https://github.com/gabsalegra/pedelogo-catalogo.git', branch: 'main'
            }

        }
        stage('Docker Build') {
            steps {
                script{
                    dockerapp = docker.build("gabrielalegra/pedelogo-catalogo:${env.BUILD_ID}", '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }
        stage('Docker push image'){
            steps{
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }
         stage('Deploy Kubernetes'){
            agent {
                kubernetes {
                    cloud 'kubernetes'
                }
            }
            steps {
                kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'kube')
            }
        }
}
post {
        success {
              sendGoogleChat("This is a _simple_ text message " +
                "with a <https://github.com/mkutz/jenkins-google-chat-notification|link>" +
                "\nand a line break, " +
                "which might be interesting to <users/all> users in the Group.") 
}
}

}