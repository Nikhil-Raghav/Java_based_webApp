pipeline{
    agent any // decides which node to run the pipeline on
    
    tools {
        jdk 'java-17'
        maven 'maven'
    }
    environment {
         IMAGE_NAME = "nikhilraghav08/itkannadigaru-web:${GIT_COMMIT}"
}
stages {
    stage ('git-checkout') {
            steps {
            git url:'https://github.com/Nikhil-Raghav/Java_based_webApp.git', branch:'prod'
            }
    }
            stage ('Compile') {
                steps {
                    sh '''
                        mvn compile
                    '''
                }
            }
            stage ('packaging') {
                steps {
                    sh '''
                        mvn clean package
                    '''
                }
            }
            stage ('Docker build') {
                steps {
                    sh '''
                        printenv
                        docker build -t ${IMAGE_NAME} .
                    '''
                }
            }
            stage ('Docker testing') {
                steps {
                    sh '''
                    docker kill itkannadigaru-web-test
                    docker rm itkannadigaru-web-test
                        docker run -it -d --name itkannadigaru-web-test -p 9000:8080 ${IMAGE_NAME}
                    '''
                }
            }
            stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        // Login to Docker Hub
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
            
}
            stage ('Push to Docker Hub') {
                steps {
                    sh '''
                        docker push ${IMAGE_NAME}
                    '''
                }
            }
}
}
