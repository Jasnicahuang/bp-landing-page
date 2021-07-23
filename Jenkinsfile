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
                if (env.BRANCH_NAME == 'staging') {
                    git branch: 'staging', credentialsId: 'github_key', url: 'https://github.com/Jasnicahuang/bp-landing-page.git'
                }
                else if (env.BRANCH_NAME == 'origin/master') {
                    git credentialsId: 'github_key', url: 'https://github.com/Jasnicahuang/bp-landing-page.git'
                }
            }
        }

//        stage('Building Image') { 
//            steps {
//                script {
//                    if (env.BRANCH_NAME == 'staging') {
//                        dockerImage_stage = docker.build imagename_stage
//                    
//                    }
//                    else if (env.BRANCH_NAME == 'origin/master') {
//                        dockerImage_prod = docker.build imagename
//                    }
//                }     
//            }
//        }
//        stage('Push Image to DockerHub') { 
//            steps {
//                script {
//                    if (env.BRANCH_NAME == 'staging') {
//                        docker.withRegistry('', registryCredential) 
//                        {
//                            dockerImage_stage.push("$BUILD_NUMBER")
//                            dockerImage_stage.push('latest')
//                        }   
//                    }
//                    else if (env.BRANCH_NAME == 'origin/master') {
//                        docker.withRegistry('', registryCredential) 
//                        {
//                            dockerImage_prod.push("$BUILD_NUMBER")
//                            dockerImage_prod.push('latest')
//                        } 
//                    }
//                }
//            }
//        }
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

