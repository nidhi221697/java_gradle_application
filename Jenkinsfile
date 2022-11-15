
pipeline{
    agent any 
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("sonar quality check"){
            agent {
                docker {
                    image 'maven'
                }
            }
            steps{
                script{
                    
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        
                        sh 'mvn clean package sonar:sonar'
                    }
                   }
            }
        }
        stage('Quality Gate Status'){
                
                steps{
                    
                    script{
                        
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                    }
                }
            }
         stage("docker build & docker push"){
             steps{
                 script{
                    withCredentials([string(credentialsId: 'docker_password', variable: 'docker_pass')]) {
                              sh '''

                            docker image build -t springapp:${VERSION} .
                            docker image tag springapp:v1.${VERSION} 44.207.6.86:8083/springapp:v1.${VERSION}
                            docker image tag springapp:v1.${VERSION} 44.207.6.86:8083/springapp:latest
               
                            docker login -u admin -p $docker_pass 44.207.6.86:8083
                            docker image push 44.207.6.86:8083/springapp:v1.${VERSION}
                            docker image push 44.207.6.86:8083/$JOB_NAME:latest

                            docker image rmi 44.207.6.86:8083/springapp:v1.${VERSION}
                            docker image rmi 44.207.6.86:8083/springapp:latest
                             '''
                     }
                 }
             }
         }  
    }
}
// docker login -u admin -p nexus 44.207.6.86:8083