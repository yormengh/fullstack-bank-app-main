pipeline {
    agent any

    tools {
        jdk 'jdk17'
        nodejs 'nodejs16'
    }

    environment {
        SCANNER_HOME = tool name: 'sonar'
    }

    stages {
        stage('git pull') {
            steps {
                git branch: 'main', url: 'https://github.com/yormengh/fullstack-bank-app-main.git'
            }
        }

        stage('Owaps fs scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('trivy fs scan') {
            steps {
                sh 'trivy fs . > trivyfs.txt'
            }
        }

        stage('sonarqube analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=full-stack-bank \
                    -Dsonar.projectKey=full-stack-bank '''
                }
            }
        }

        stage('install nodejs dependencies backend') {
            steps {
                dir('/var/lib/jenkins/workspace/fullstack-bank-app/app/backend/') {
                    sh 'npm install'

                }
            }
        } 

        stage('install nodejs dependencies frontend') {
            steps {
                dir('/var/lib/jenkins/workspace/fullstack-bank-app/app/frontend/') {
                    sh 'npm install'

                }
            }
        }

        stage('docker container deploy') {
            steps {
                sh 'npm run compose:up -d'
            }
        }

        stage('run command to tag local images') {
            steps {
                sh 'docker tag app_backend yormengh/fullstackbank_backend:latest'
                sh 'docker tag app_frontend yormengh/fullstackbank_frontend:latest'
                sh 'docker tag postgres:15.1 yormengh/full-stack-bank:database'
            }
        }

        stage("TRIVY"){
            steps{
                sh "trivy image yormengh/fullstackbank_backend:latest > trivyimage.txt"
                sh "trivy image yormengh/fullstackbank_frontend:latest > trivyimage.txt"
                sh "trivy image yormengh/full-stack-bank:database > trivyimage.txt" 
            }
        }

        stage('run command to push images') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'fullstack-bank-app-id', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                        sh 'docker push yormengh/fullstackbank_backend:latest'
                        sh 'docker push yormengh/fullstackbank_frontend:latest'
                        sh 'docker push yormengh/full-stack-bank:database'
                    }
                }   
            }
        }  
        
    }
}
