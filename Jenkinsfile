pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *')
    }

    environment {
        IMAGE_NAME = 'pradip-web-app'
        CONTAINER_NAME = 'webcontainer'
        SONAR_PROJECT_KEY = 'pradip-html-app'  // Your SonarQube project key
        SONAR_SCANNER = 'sonarqube'         // Name configured under Jenkins Tools
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/CodeNewbie-pradip/jenkins-sonarqube-docker.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {  // Replace with your SonarQube server name
                    sh "${tool SONAR_SCANNER}/bin/sonar-scanner " +
                        "-Dsonar.projectKey=${SONAR_PROJECT_KEY} " +
                        "-Dsonar.sources=. " +
                        "-Dsonar.host.url=$SONAR_HOST_URL " +
                        "-Dsonar.login=$SONAR_AUTH_TOKEN"
                }
            }
        }


        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Stop and Remove Old Container') {
            steps {
                script {
                    sh "docker stop ${CONTAINER_NAME} || true"
                    sh "docker rm ${CONTAINER_NAME} || true"
                }
            }
        }

        stage('Run New Container') {
            steps {
                script {
                    sh "docker run -d --name ${CONTAINER_NAME} -p 80:80 ${IMAGE_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "Deployment successful!"
        }
        failure {
            echo "Something went wrong with the deployment."
        }
    }
}
