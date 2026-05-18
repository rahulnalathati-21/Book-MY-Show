pipeline {

    agent any

    tools {
        jdk 'jdk21'
        nodejs 'node18'
    }

    environment {
        SONAR_HOME = tool 'sonar-scanner'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/your/repo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {

                    sh """
                    ${SONAR_HOME}/bin/sonar-scanner \
                    -Dsonar.projectName=bms \
                    -Dsonar.projectKey=bms \
                    -Dsonar.sources=.
                    """
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t rahulf121/bms:latest .'
            }
        }

        stage('Docker Login') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockercred',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    sh '''
                    echo $DOCKER_PASS | docker login \
                    -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push rahulf121/bms:latest'
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                sh 'kubectl apply -f deployment.yml'
                sh 'kubectl apply -f service.yml'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get svc'
            }
        }
    }

    post {

        success {
            emailext(
                subject: "BUILD SUCCESS",
                body: "Deployment successful",
                to: 'rahulsiddharth2.0@gmail.com'
            )
        }

        failure {
            emailext(
                subject: "BUILD FAILED",
                body: "Deployment failed",
                to: 'rahulsiddharth2.0@gmail.com'
            )
        }
    }
}
