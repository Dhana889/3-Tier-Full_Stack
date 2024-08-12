pipeline {
    agent any
    
    tools {
        nodejs 'node21'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Check Out') {
            steps {
                git branch: 'main', credentialsId: 'git-key', url: 'https://github.com/Dhana889/3-Tier-Full_Stack.git'
            }
        }
        
        stage('Install Package Dependency') {
            steps {
                sh "npm install"
            }
        }
        
       stage('Unit Tests') {
            steps {
                sh "npm test"
            }
        }
        
        stage('Trivy FS Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Campground -Dsonar.projectName=Campground" 
                }
            }
        }
        
        stage('Docker Build & Tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t 8889docker/campgrnd:latest ."
                    }
                }
            }
        }
        
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format table -o trivy-image-report.html 8889docker/campgrnd:latest"
            }
        }
        
        stage('Docker Push Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push 8889docker/campgrnd:latest"
                    }
                }
            }
        }
        
        stage('Deploy To Dev') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker run -d -p 3000:3000 8889docker/campgrnd:latest"
                    }
                }
            }
        }
    }
}

