pipeline {
    
    agent {
        label "mylinux"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/priyanshu-bhatt/Devops-LPU-Project.git'
                
                
            }
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t  priyanshu18/devops:${BUILD_TAG}  ."
                //sh 'whoami'
            }
            
        }
        stage('Push Image to Docker HUB') {
            steps {
                
                withCredentials([string(credentialsId: 'DOCKER_HUB_PWD', variable: 'DOCKER_HUB_PASS_CODE')]) {
                 sh "sudo docker login -u priyanshu18 -p $DOCKER_HUB_PASS_CODE"
}
               
               sh "sudo docker push priyanshu18/devops:${BUILD_TAG}"
            }
            
        }
            stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['QA_ENV_SSH_CRED']) {
                    
                    sh "ssh  -o  StrictHostKeyChecking=no ec2-user@3.108.218.66 sudo docker rm -f mydevopsapp"
                    sh "sudo yum install docker"
                    sh "sudo systemctl enable docker"
                    sh "ssh ec2-user@3.108.218.66 sudo docker run  -d  -p  8080:8080 --name mydevopsapp   priyanshu18/devops:${BUILD_TAG}"
                }

            }
            
        }
        stage('QAT Test') {
            steps {
                
               // sh 'curl --silent http://13.233.100.238:8080/java-web-app/ |  grep India'
                
                retry(10) {
                    sh 'curl --silent http://3.108.218.66:8080/java-web-app/ |  grep Priyanshu'
                }
            
               
            }
        }
                stage('approved') {
            steps {
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    emailext body: 'Hii sir This Mail is from the Jenkins ADMIN, The QA had  passed the test', subject: 'Pipeline Success Mail', to: 'yogeshkumar12t@gmail.com'
                    // do action
                } else {
                    // not do action
                    echo " The Job Aborted"
                }
            
                
            }
        }
        }
    }
}
