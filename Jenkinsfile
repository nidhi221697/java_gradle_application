
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
                                 docker build -t 44.207.6.86:8083/springapp:${VERSION} .
                                 docker login -u admin -p $docker_pass 44.207.6.86:8083 
                                 docker push  44.207.6.86:8083/springapp:${VERSION}
                                 docker rmi 44.207.6.86:8083/springapp:${VERSION}
                             '''
                     }
                 }
             }
         }  
    }
}
// docker login -u admin -p nexus 44.207.6.86:8083