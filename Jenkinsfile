pipeline {
    agent any

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/yassinehamadi/student-management.git'
            }
        }

        stage('Build + Tests + JaCoCo') {
            steps {
                sh 'mvn clean test jacoco:report package'
            }
        }

        stage('Analyse Qualité - SonarQube') {
            steps {
                withSonarQubeEnv('sq1') {
                    withCredentials([string(credentialsId: 'jenkins-sonor', variable: 'SONAR_AUTH_TOKEN')]) {
                        sh """
                            mvn sonar:sonar \
                            -Dsonar.projectKey=student-management \
                            -Dsonar.host.url=http://192.168.33.11:9000 \
                            -Dsonar.login=$SONAR_AUTH_TOKEN \
                            -Dsonar.java.binaries=target/classes \
                            -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
                        """
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t student-management-app ."
                sh "docker tag student-management-app:latest yassinehamadi/student-management-app:latest"
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker push yassinehamadi/student-management-app:latest"
                }
            }
        }
    }
}
