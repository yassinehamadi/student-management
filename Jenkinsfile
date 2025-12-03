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