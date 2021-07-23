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
                script{
                    try {
                        sh 'docker rmi -f $(docker images -q -f dangling=true)'
                    }
                    catch(Exception e){
                        echo 'No dangling images found. '
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
                script{
                    try {
                        sh 'docker rmi -f $(docker images -q -f dangling=true)'
                    }
                    catch(Exception e){
                        echo 'No dangling images found. '
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
                sh 'sudo -u ubuntu -H sh -c "kubectl apply -f kube-landing-page/staging-landing-page-deploy.yaml -n staging"'
                
            }
        }
        
        stage('Deploy To Production') {
            when {
                branch 'master'
            }
            steps {
                sh 'sudo -u ubuntu -H sh -c "kubectl apply -f kube-landing-page/production-landing-page-deploy.yaml -n production"'            
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

