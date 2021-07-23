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
                script {
                    if ('docker images -f "dangling=true"') {
                        sh 'docker rmi $(docker images -f "dangling=true" -q)'
                    } else {
                        echo " ================ No Detect Dangling Image ================ "
                    }
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
                script {
                    if ('docker images -f "dangling=true"') {
                        sh 'docker rmi $(docker images -f "dangling=true" -q)'
                    } else {
                        echo " ================ No Detect Dangling Image ================ "
                    }
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

                            sh "docker rmi $imagename_stage:$BUILD_NUMBER"
                            sh "docker rmi $imagename_stage:latest"
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

                            sh "docker rmi $imagename_prod:$BUILD_NUMBER"
                            sh "docker rmi $imagename_prod:latest"
                        }   
                }
            }
        }
        
        stage('Deploy To Staging') {
            when {
                branch 'staging'
            }
            steps {
                kubernetesDeploy(
                kubeconfigId: 'kubeconfig',
                configs: 'kube-landing-page/staging-landing-page-deploy.yaml',
                enableConfigSubstitution: true,
                )
                
            }
        }
        
        stage('Deploy To Production') {
            when {
                branch 'master'
            }
            steps {
                kubernetesDeploy(
                kubeconfigId: 'kubeconfig',
                configs: 'kube-landing-page/production-landing-page-deploy.yaml',
                enableConfigSubstitution: true,
                )
                
            }
        }

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

