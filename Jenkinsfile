pipeline {
    agent any
    options {timestamps()}
  	
    stages {
        stage('IMAGE'){
            steps{
                sh'''
		ssh -T git@github.com
                docker-compose build
                git tag 1.0${BUILD_NUMBER}
                git push --tags
                docker tag ghcr.io/alvarodcr/hello-2048/hello2048:v1 ghcr.io/alvarodcr/hello-2048/hello2048:1.0.${BUILD_NUMBER}
            '''
            }
        }  
        
        stage('GIT_LOGIN'){
            steps{ 
                withCredentials([string(credentialsId:'ghrc-token', variable: 'GIT_TOKEN')]) {
                    sh 'echo $GIT_TOKEN | docker login ghcr.io .u alvarodcr --password-stdin'
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

