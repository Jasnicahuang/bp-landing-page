pipeline {
    
    environment {
        imagename_prod = "jasnicahuang/landingpage"
        imagename_stage = "jasnicahuang/landingpage-stage"
        registryCredential = 'docker_hub_login'
        dockerImage_prod = ''
        dockerImage_stage = ''
    }

    agent any 

    stages {
        
        stage('Prepare workspace') {
            steps {
                echo 'Prepare workspace'

                // Clean workspace
                step([$class: 'WsCleanup'])

                // Checkout git
                checkout scm
            }
        }
       
        stage('Cloning Git') { 
            steps {
                    git credentialsId: 'github_key', url: 'https://github.com/Jasnicahuang/bp-landing-page.git'
            }
        }

        stage('Building Image Staging') { 
            when {
                branch 'staging'
            }
            steps {
                script {
                        dockerImage_stage = docker.build imagename_stage
                    
                }

            }     
        }

        stage('Building Image Production') { 
            when {
                branch 'master'
            }
            steps {
                script {
                        dockerImage_prod = docker.build imagename_prod   
                }

            }     
        }
        
        stage('Push Image Staging to DockerHub') { 
            when {
                branch 'staging'
            }
            steps {
                script {
                        docker.withRegistry('', registryCredential) 
                        {
                            dockerImage_stage.push("$BUILD_NUMBER")
                            dockerImage_stage.push('latest')
                        }   
                }
            }
        }

        stage('Push Image Production to DockerHub') { 
            when {
                branch 'master'
            }
            steps {
                script {
                        docker.withRegistry('', registryCredential) 
                        {
                            dockerImage_prod.push("$BUILD_NUMBER")
                            dockerImage_prod.push('latest')
                        }   
                }
            }
        }

//        stage('Remove Unused Images'){
//            steps {
//                if (env.BRANCH_NAME == 'staging') {
//                    sh "docker rmi $imagename_stage:$BUILD_NUMBER"
//                    sh "docker rmi $imagename_stage:latest"
//                }
//                else if (env.BRANCH_NAME == 'origin/master') {
//                    sh "docker rmi $imagename_stage:$BUILD_NUMBER"
//                    sh "docker rmi $imagename_stage:latest"
//                }
//                
//            }
//        }
//        if (env.BRANCH_NAME == 'staging') {
//            stage('Deploy to Staging'){
//                sh "kubectl set image deployment.apps/landing-page-deployment landing-page-deployment=jasnicahuang/$imagenam_stage:latest -n staging"
//                sh "kubectl delete pod --all -n staging"
//            }
//        }
//        else if (env.BRANCH_NAME == 'origin/master'){
//            stage('Deploy to Production'){
//                sh "kubectl set image deployment.apps/landing-page-deployment landing-page-deployment=jasnicahuang/$imagenam_prod:latest -n production"
//                sh "kubectl delete pod --all -n production"
//            }
//
//        }       
    }
}

