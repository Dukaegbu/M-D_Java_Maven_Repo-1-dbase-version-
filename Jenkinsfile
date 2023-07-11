pipeline {
    agent any
 tools {
    maven 'Maven'
 }

    environment {
        DOCKER_CMD= 'docker run -d -p 8080:8080 dukaegbu/dbase-repo:jma-3.0 '
    }
    stages {
        stage ('Build App') {
            steps {
                script {
                    echo "Building the jar file"
                    sh 'mvn clean install'
                }
            }
         }

        stage ('test') {
            steps{
                script {
                echo "Testing application"
                sh 'mvn test'
                }
             }
        }

        stage ('Build Image') {
            steps {
                script{
                echo "Building Docker Image"
                withCredentials([userPassword(credentialsId:'docker-credentials', usernameVariable:'USER',passwordvariable:'PASS')]) {
                    sh "docker login -u $USER -p $PASS"
                    sh 'docker build -t dukaegbu/dbase-repo:jma-3.0 .'
                    sh 'docker push dukaegbu/dbase-repo:jma-3.0'
                    }
                }
            }
        }
        stage ('Deploy to Dev Env') {
            steps {
                
                sshagent(['dev-key']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@3.139.62.70 dukaegbu/dbase-repo:jma-3.0"
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@3.139.62.70 $DOCKER_CMD"
                }
            }
        }
    }
}