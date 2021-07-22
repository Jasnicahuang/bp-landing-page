pipeline {
    
    environment {
        imagename = "jasnicahuang/landingpage"
        registryCredential = 'docker_hub_login'
        dockerImage = ''
    }

    agent any 
    stages {
        stage('Cloning Git') { 
            steps {
                git credentialsId: 'github_key', url: 'https://github.com/Jasnicahuang/bp-landing-page.git'
            }
        }
        stage('Building Image') { 
            steps {
                script {
                    dockerImage = docker.build imagename 
                }     
            }
        }
        stage('Push Image to DockerHub') { 
            steps {
                script {
                    docker.withRegistry)('', registryCredential) {
                        dockerImage.push("$BUILD_NUMBER")
                        dockerImage.push('latest')
                    }
                }
            }
        }
        stage('Remove Unused Images'){
            steps {
                sh "docker rmi $imagename:$BUILD_NUMBER"
                sh "docker rmi $imagename:latest"
            }
        }
    }
}
