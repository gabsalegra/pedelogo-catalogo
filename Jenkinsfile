//Declarative Pipeline
pipeline {
    environment {
        TAG_VERSION = "${env.BUILD_ID}"
//chat-test    
        GOOGLE_CHAT_URL = 'https://chat.googleapis.com/v1/spaces/AAAAC0fQyiA/messages?key=AIzaSyDdI0hCZtE6vySjMm-WEfRq3CPzqKqqsHI&token=kW_raPlXqUnVgQDBfymf8A0IhPtaHwQNfoekzNPrIN8%3D'
//chat-zabbix GOOGLE_CHAT_URL = 'https://chat.googleapis.com/v1/spaces/AAAAtVKh0NY/messages?key=AIzaSyDdI0hCZtE6vySjMm-WEfRq3CPzqKqqsHI&token=EXLRHKKpL8xUbYHVRX3s-QfbdMjhFaXmTzlum9fh6qs%3D'
//chat-devs      GOOGLE_CHAT_URL = 'https://chat.googleapis.com/v1/spaces/AAAA1XvQYEU/messages?key=AIzaSyDdI0hCZtE6vySjMm-WEfRq3CPzqKqqsHI&token=P4L8IUHFvHhv5t3lP4R7wzQAn-6V_17KlTELvgQjuls%3D'
    
    }
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
                sh 'sed -i "s/{{tag}}/$TAG_VERSION/g" ./k8s/api.yaml'
                sh 'cat ./k8s/api.yaml'
                kubernetesDeploy(configs: '**/k8s/**', kubeconfigId: 'kube')
            }
        }
}
post {
        success {
              sendGoogleChatBuildReport(Build: env.BUILD_TAG,
                    message: "Teste de mensagem de texto: Pipeline feita com sucesso." +
                             "<br>Teste de automação dos processos, desde buildar a imagem no dockerhub a deployar no k8s.")
}
}
}