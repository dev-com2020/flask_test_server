pipeline {
    agent {
        docker {
            image 'python:3.9-slim'
            args '-u root' // Uruchom jako root, aby móc instalować pakiety
        }
    }

    stages {
        stage('Install Dependencies') {
            steps {
                echo 'Installing Python dependencies...'
                sh 'pip install -r requirements.txt'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                // Uruchom pytest i generuj raport w formacie JUnit XML
                sh 'pytest --junitxml=test-report.xml'
            }
            post {
                always {
                    // Zawsze publikuj wyniki testów
                    junit 'test-report.xml'
                }
            }
        }

        stage('Deploy') {
            // Ten etap wykona się tylko, jeśli poprzednie etapy zakończyły się sukcesem
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Tests passed successfully. Deploying the application...'
                // W prawdziwym scenariuszu, tutaj znalazłyby się kroki wdrożeniowe, np.:
                // sh './deploy.sh'
                // sh 'docker build -t my-app:latest .'
                // sh 'docker push my-registry/my-app:latest'
            }
        }
    }

    post {
        // Ten blok wykonuje się na końcu całego potoku
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        unstable {
            // Dzieje się tak, gdy testy nie powiodą się, ale nie przerywają potoku
            echo 'Pipeline is unstable, some tests failed.'
        }
    }
}