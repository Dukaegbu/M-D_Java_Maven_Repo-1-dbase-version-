pipeline {
    agent any
 tools {
    maven 'Maven'
 }
    Stages {
        Stage ('build App') {
            steps {
                script {
                    echo "Building the jar file"
                    sh 'mvn clean install'
                }
            }
         }
        Stage ('test') {
            steps{
                echo "Testing application"
             sh 'mvn test'
             }
        }
        Stage ('Build Image') {
            steps {
                echo "Building Docker Image"
                withCredentials([userPassword(credentialsId:'docker-credentials', usernameVariable:'USER',passwordvariable:'PASS')]){
                    sh "docker echo $PASS login -u $USER --password-stdin"
                    sh 'docker build .-t dukaegbu/dbase-repo:jma-3.0'
                    sh 'docker push dukaegbu/dbase-repo:jma-3.0'
                }
            }
        }
        Stage ('Deploy to Dev Env') {
            steps {
                def dockercmd = 'docker run -d -p 8080:8080 dukaegbu/dbase-repo:jma-3.0'
                sshagent(['dev-key']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu3.139.62.70 $dockercmd"
                }
            }
        }
    }
}