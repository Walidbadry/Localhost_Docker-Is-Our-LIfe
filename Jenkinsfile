pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', credentialsId: 'github_credentials', url: 'https://github.com/Walidbadry/Localhost_Docker-Is-Our-LIfe.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        
        stage('File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BoardGame -Dsonar.projectKey=BoardGame \
                            -Dsonar.java.binaries=.'''
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                script {
                  waitForQualityGate abortPipeline: false, credentialsId: 'sonar' 
                }
            }
        }
        
        stage('Build') {
            steps {
               sh "mvn package"
            }
        }
        
        stage('Publish To Nexus') {
            steps {
               withMaven(globalMavenSettingsConfig: 'global-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh "mvn deploy"
                }
            }
        }
        
        stage('Build & Tag Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker build -t walid123321/java_app_12:latest ."
                    }
               }
            }
        }
        
        stage('Docker Image Scan') {
            steps {
                sh "trivy image --format table -o trivy-image-report.html walid123321/java_app_12:latest"
            }
        }
        
        stage('Push Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh "docker push walid123321/java_app_12:latest"
                    }
               }
            }
        }
        
        stage('GitOps - Update Image Tag') {
            steps {
                script {
                    def newTag = "walid123321/java_app_12:latest"
                    
                    sh '''
                    # Configure Git
                    git config --global user.email "walid882001@gmail.com"
                    git config --global user.name "Jenkins"

                    # Clone the repository
                    git clone https://github.com/Walidbadry/Localhost_Docker-Is-Our-LIfe.git gitops-repo
                    cd gitops-repo

                    # Update the image tag in the deployment file
                    sed -i 's|image: walid123321/java_app_12:.*|image: ''' + newTag + '''|' deployment-service.yaml

                    # Commit and push changes
                    git add k8s/deployment.yaml
                    git commit -m "Update image to ''' + newTag + '''"
                    git push walidbadry:${GIT_CREDENTIALS}@github.com/Walidbadry/Localhost_Docker-Is-Our-LIfe.git main
                    '''
                }
            }
        }
    }

    post {
        success {
            emailext (
                to: 'walid882001@gmail.com',
                subject: "Jenkins Build Success - ${JOB_NAME}",
                body: """
                    ✅ Build Successful!  
                    - Job: ${JOB_NAME}  
                    - Build: #${BUILD_NUMBER}  
                    - Git Commit: ${GIT_COMMIT}  
                    - Docker Image: walid123321/java_app_12:latest  
                    
                    Check Jenkins for details: ${BUILD_URL}
                """
            )
        }

        failure {
            emailext (
                to: 'walid882001@gmail.com',
                subject: "Jenkins Build Failed - ${JOB_NAME}",
                body: """
                    ❌ Build Failed!  
                    - Job: ${JOB_NAME}  
                    - Build: #${BUILD_NUMBER}  
                    - Git Commit: ${GIT_COMMIT}  
                    
                    Check Jenkins for details: ${BUILD_URL}
                """
            )
        }
    }
}
