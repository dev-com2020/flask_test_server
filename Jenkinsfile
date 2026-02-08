pipeline {
    // Użyj agenta z etykietą 'python-agent', na którym jest zainstalowany Python
    agent any

    environment {
        // Definiujemy nazwę katalogu dla naszego wirtualnego środowiska
        VENV_DIR = 'venv'
    }

    stages {
        stage('Setup Virtual Environment') {
            steps {
                echo 'Setting up Python virtual environment...'
                // Sprawdź, czy katalog venv już istnieje, jeśli tak, usuń go, aby zapewnić czyste środowisko
                sh 'rm -rf ${VENV_DIR}'
                // Utwórz nowe wirtualne środowisko
                sh 'python3 -m venv ${VENV_DIR}'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing Python dependencies...'
                // Aktywuj wirtualne środowisko i zainstaluj zależności
                // Używamy jednego bloku sh, aby polecenia wykonywały się w tej samej sesji powłoki
                sh '''
                    source ${VENV_DIR}/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests...'
                // Uruchom pytest w kontekście wirtualnego środowiska
                sh '''
                    source ${VENV_DIR}/bin/activate
                    pytest --junitxml=test-report.xml
                '''
            }
            post {
                always {
                    junit 'test-report.xml'
                }
            }
        }

        stage('Deploy') {
            when {
                expression { currentBuild.result == null || currentBuild.result == 'SUCCESS' }
            }
            steps {
                echo 'Tests passed successfully. Deploying the application...'
                // W tym miejscu mogłoby nastąpić wdrożenie, np. przez skopiowanie plików
                // lub uruchomienie aplikacji na serwerze docelowym przy użyciu Ansible/SSH.
                sh '''
                    source ${VENV_DIR}/bin/activate
                    echo "Simulating deployment of Flask app..."
                    # Przykład: uruchomienie aplikacji w tle (w prawdziwym scenariuszu użyłbyś np. Gunicorn + systemd)
                    # nohup flask run --host=0.0.0.0 &
                '''
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished. Cleaning up virtual environment...'
            // Posprzątaj po sobie, usuwając wirtualne środowisko
            sh 'rm -rf ${VENV_DIR}'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        unstable {
            echo 'Pipeline is unstable, some tests failed.'
        }
    }
}