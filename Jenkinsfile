pipeline {
    agent any
    
    stages {

        stage('Get Source'){
            steps {
                git url: 'https://gabriel_alegra@bitbucket.org/gabriel_alegra/teste_sonarqube.git', branch: 'main'
            }

        }
        stage('Docker Build') {
            steps {
                script{
                    dockerapp = docker.build("gabrielalegra/pedelogo-catalogo:${env.BUILD.ID}", '-f ./src/PedeLogo.Catalogo.Api/Dockerfile .')
                }
            }
        }
        stage('Docker push image'){
            steps{
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    dockerapp.push('latest')
                    dockerapp.push("${env.BUILD.ID}")
                    }
                }
            }
        }
}

}