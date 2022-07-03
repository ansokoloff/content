pipeline {
    agent none
    environment {
    registry = "arrcticc/skillfactory"
    registryCredential = credentials('Dockerhub')
    dockerImage = ''
    } 
  stages {
        stage('Clone repo') { 
        agent { label 'main' }  
            steps {
                git branch: 'main', credentialsId: 'GitHUB', url: 'https://github.com/ansokoloff/content.git'            
          }
        }
        // stage('Build by docker') { 
        // when { branch 'develop' }      
        //     agent{ 
        //         docker {
        //             image 'maven:3.8.5-jdk-11-slim'
        //             args '-v /$HOME/.m2:/root/.m2'                    
        //             reuseNode true
        //             label 'dockerfarm'
        //         }
        //     }    
        //     steps{
        //         sh 'mvn clean install'
        //     }  
        // }       
        stage('Build docker image') { 
        agent { label 'main' }        
            steps {
                      sh "/usr/bin/docker build -t ${registry}:${env.BUILD_ID} --no-cache ."
            }
        }
        // stage('Run test environment') {
        // agent { label 'dockerfarm' }  
        // when { branch 'develop' }
        //     steps {
        //             sh "echo $PWD"
        //               sh "/usr/bin/docker run -d --rm -p 80:8080 ${registry}:${env.BUILD_ID}"
        //             }
        //     }
        // stage('Test') {
        // agent { label 'dockerfarm' }  
        // when { branch 'develop' }
        //     steps {
        //               sh 'sleep 5'
        //               sh 'curl -s --output content http://localhost:80/project/'
        //               sh 'curl -s -I --output code http://localhost:80/project/'
        //               sh 'cat content'
        //               sh 'if [ $(cat code | grep 200 | grep HTTP | wc -l) -lt 1 ]; then exit 2; fi'
        //               sh 'if [ $(cat content | grep title | wc -l) -lt 1 ]; then exit 2; fi'
        //               sh 'rm content'
        //               sh 'rm code'
        //             }
        //     }
        stage('Deploy image to hub.docker') {
        agent { label 'main' }  
            steps{
                      sh 'echo $registryCredential_PSW | docker login -u $registryCredential_USR --password-stdin'
                      sh 'docker push $registry:$BUILD_NUMBER'
                      sh 'docker tag $registry:$BUILD_NUMBER $registry:latest'
                      sh 'docker push $registry'
                }
        }
        stage('Cleaning up') {
        agent { label 'main' }  
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }       
      stage ('Push value for Helm') {
        agent { label 'main' } 
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHUB', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                    sh 'git -C /var/lib/jenkins/value/ pull https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/value.git'
                    sh 'echo "image:\n  tag: $BUILD_NUMBER" > /var/lib/jenkins/value/value.yaml'
                    sh 'git -C /var/lib/jenkins/value/ add value.yaml'
                    sh 'git -C /var/lib/jenkins/value/ commit -m "Revision $BUILD_NUMBER" '
                    sh 'git -C /var/lib/jenkins/value/ push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/value.git'
                                
                } 
        }
      } 
          stage ('Receive helm chart') {
            agent { label 'kuber' } 
            steps {
                sh "uname -a"
                withCredentials([usernamePassword(credentialsId: 'Github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                sh 'git -C /var/lib/jenkins/value/ pull https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/value.git'                
            } 
          }
        }
          
          stage ('Receive data of artifact') {
            agent { label 'kuber' } 
            steps {
                sh "uname -a"
                withCredentials([usernamePassword(credentialsId: 'Github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                sh 'git -C /var/lib/jenkins/value/ pull https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/ansokoloff/value.git'                
            } 
          }
        }
        stage ('Deploy to prod') {
            agent { label 'kuber' }
            steps {
                sh 'helm upgrade --install epam /var/lib/jenkins/epam --values /var/lib/jenkins/value/value.yaml '
            }
        }        
 
    }
}