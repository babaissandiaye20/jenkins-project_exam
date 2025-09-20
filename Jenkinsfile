pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            agent {
                docker {
                    image 'maven:3.9.6-eclipse-temurin-17'
                    reuseNode true
                }
            }
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKER_EXAM', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        def appName = 'jenkins-project_exam' // Nom de l'application
                        def branchName = env.BRANCH_NAME ?: env.GIT_BRANCH ?: 'latest'
                        def safeTag = branchName.replaceAll('[^A-Za-z0-9._-]', '-')
                        def dockerImage = "${DOCKER_USER}/${appName}:${safeTag}"

                        sh """
                            set -e
                            echo "Building Docker image: ${dockerImage}"
                            docker build -t "${dockerImage}" .

                            echo "Logging into Docker Hub..."

                            echo "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin

                            echo "Pushing Docker image: ${dockerImage}"
                            docker push "${dockerImage}"
                        """
                    }
                }
            }
        }

        stage('Deploy to Render') {
            steps {
                withCredentials([string(credentialsId: 'RENDER_HOOK_EXAM', variable: 'RENDER_HOOK_URL')]) {
                    sh 'curl -X POST "$RENDER_HOOK_URL"'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
