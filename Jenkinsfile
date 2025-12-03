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

    }
}