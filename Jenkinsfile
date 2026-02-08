pipeline {
    agent {
        docker {
            image 'python:3.9-slim'
            args '-u root'
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
                // Używamy bloku try-catch, aby potok nie zatrzymał się w przypadku niepowodzenia testów
                // Zamiast tego, zmieniamy status budowania na UNSTABLE
                catchError(buildResult: 'UNSTABLE', stageResult: 'UNSTABLE') {
                    sh 'pytest --junitxml=test-report.xml'
                }
            }
            post {
                always {
                    junit 'test-report.xml'
                }
            }
        }

        stage('Deploy to STAGE') {
            // Ten etap wykona się tylko, jeśli budowanie zakończyło się sukcesem
            when {
                expression { currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'All tests passed. Deploying the application to STAGE environment...'
                // Tutaj umieściłbyś skrypty wdrażające na środowisko STAGE
                // np. sh './deploy-to-stage.sh'
            }
        }

        stage('Deploy to DEV') {
            // Ten etap wykona się tylko, jeśli budowanie jest niestabilne (testy nie powiodły się)
            when {
                expression { currentBuild.result == 'UNSTABLE' }
            }
            steps {
                echo 'Some tests failed. Deploying the application to DEV environment for debugging...'
                // Tutaj umieściłbyś skrypty wdrażające na środowisko DEV
                // np. sh './deploy-to-dev.sh'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Pipeline executed successfully and deployed to STAGE.'
        }
        unstable {
            echo 'Pipeline is unstable (tests failed) and deployed to DEV.'
        }
        failure {
            // Ten status wystąpi, jeśli potok zawiedzie z innego powodu niż testy (np. błąd składni, problem z agentem)
            echo 'Pipeline failed catastrophically!'
        }
    }
}