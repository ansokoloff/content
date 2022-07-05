pipeline {
    agent none
    environment {
    registry = "arrcticc/skillfactory"
    registryCredential = credentials('Dockerhub')
    dockerImage = ''
    } 
  stages {
        // stage('Clone repo') { 
        // agent { label 'main' }  
        //     steps {
        //         git branch: 'main', credentialsId: 'GitHUB', url: 'https://github.com/ansokoloff/content.git'            
        //   }
        // }
        // stage('Build docker image') { 
        // agent { label 'main' }        
        //     steps {
        //               sh "/usr/bin/docker build -t ${registry}:${env.BUILD_ID} ."
        //     }
        // }
        // stage('Deploy image to hub.docker') {
        // agent { label 'main' }  
        //     steps{
        //               sh 'echo $registryCredential_PSW | docker login -u $registryCredential_USR --password-stdin'
        //               sh 'docker push $registry:$BUILD_NUMBER'
        //               sh 'docker tag $registry:$BUILD_NUMBER $registry:latest'
        //               sh 'docker push $registry'
        //         }
        // }
        // stage('Cleaning up') {
        // agent { label 'main' }  
        //     steps{
        //         sh "docker rmi $registry:$BUILD_NUMBER"
        //     }
        // }       
      stage ('Push value for Helm') {
        agent { label 'main' } 
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHUB', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    script {
                    if (fileExists('/var/lib/jenkins/helmchart/Chart.yaml')) {
                        sh 'git -C /var/lib/jenkins/helmchart/ pull https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/helmchart.git'
                        } else {
                        sh 'git -C /var/lib/jenkins/ clone https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/helmchart.git'
                        }
                    }
                    
                    // sh '[ -d "/var/lib/jenkins/content/" ] && echo "Directory /path/to/dir exists."'
                    // sh 'git -C /var/lib/jenkins/content/ pull https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/content.git'
                    // sh 'sed -i \'s/appVersion: "4"/appVersion: "$BUILD_NUMBER"/\' /var/lib/jenkins/content/Chart.yaml'
                    // sh 'git -C /var/lib/jenkins/value/ add .'
                    // sh 'git -C /var/lib/jenkins/value/ commit -m "Revision $BUILD_NUMBER" '
                    // sh 'git -C /var/lib/jenkins/value/ push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/content.git'
                                
                } 
            }    
        }
    //       stage ('Receive helm chart') {
    //         agent { label 'kuber' } 
    //         steps {
    //             sh "uname -a"
    //             withCredentials([usernamePassword(credentialsId: 'Github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
    //             sh 'git -C /var/lib/jenkins/value/ pull https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/value.git'                
    //         } 
    //       }
    //     }
          
    //       stage ('Receive data of artifact') {
    //         agent { label 'kuber' } 
    //         steps {
    //             sh "uname -a"
    //             withCredentials([usernamePassword(credentialsId: 'Github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
    //             sh 'git -C /var/lib/jenkins/value/ pull https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/value.git'                
    //         } 
    //       }
    //     }
    //     stage ('Deploy to prod') {
    //         agent { label 'kuber' }
    //         steps {
    //             sh 'helm upgrade --install epam /var/lib/jenkins/epam --values /var/lib/jenkins/value/value.yaml '
    //         }
    //     }        
 
    }
}