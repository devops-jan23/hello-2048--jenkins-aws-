pipeline {
    agent any
    options {timestamps()}
  	
    stages {
        stage('IMAGE'){
            steps{
                
                sh '''
                docker-compose build
                git tag 1.0${BUILD_NUMBER}
                docker tag ghcr.io/alvarodcr/hello-2048/hello2048:v1 ghcr.io/alvarodcr/hello-2048/hello2048:1.0.${BUILD_NUMBER}
                '''
                sshagent(['GITHUB']) {
                    sh('git push git@github.com:alvarodcr/hello-2048.git --tags')
                }               
            }
                 
        }  
        
        stage('GIT_LOGIN'){
            steps{ 


		    sh 'echo $GIT_TOKEN | docker login ghcr.io --username alvarodcr --password-stdin'
                    sh 'docker push ghcr.io/alvarodcr/hello-2048/hello2048:1.0.${BUILD_NUMBER}'
		}
            }
        }
       

        stage('SSH_AWS') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId:'ssh-amazon', keyFileVariable: 'AWS_SSH_KEY')]) {
             
                    sh "ssh -i $AWS_SSH_KEY ec2-user@18.203.102.209 'docker pull ghcr.io/alvarodcr/hello-2048/hello2048:v1 && docker run -td --rm -p 80:80 ghcr.io/alvarodcr/hello-2048/hello2048:v1'"
             
                }
            }
        }
    }     
}



