pipeline {
    agent any

    stages {

        stage('Récupération du code depuis Git') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/yassinehamadi/student-management.git'
            }
        }

        stage('Lancer les tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Créer le livrable') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Analyse Qualité - SonarQube') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_AUTH_TOKEN')]) {
                        sh """
                            mvn sonar:sonar \
                            -Dsonar.projectKey=student-management \
                            -Dsonar.host.url=http://192.168.33.11:9000 \
                            -Dsonar.login=$SONAR_AUTH_TOKEN \
                            -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
                        """
                    }
                }
            }
        }



        stage('Vérification Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }



     stage('DOCKER-BUILD') {
                steps {
                    sh "docker build -t student-management-app ."
                    sh "docker tag student-management-app:latest yassinehamadi/student-management-app:latest"
                }
            }

            stage('DOCKER-PUSH') {
                steps {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push yassinehamadi/student-management-app:latest"
                    }
                }
     }
}
}