pipeline {
    agent any

    stages {
        stage('Checkout Project') {
            steps {
                git 'https://github.com/MR8anem/vulnerable-demo.git'
                sh "mkdir -p reports"
            }
        }

        stage('Static Security Scanning') {
            agent { label 'agent-static' }
            steps {
                echo "--- Running Static Analysis on agent-static ---"
                sh '''
                    docker run --rm \
                        -v $PWD:/app \
                        -w /app \
                        python:3.9-slim \
                        bash -c "pip install bandit && bandit -r . -f json -o reports/bandit.json"
                '''
            }
        }

        stage('Semgrep Security Scanning') {
            agent { label 'agent-dynamic' }
            steps {
                echo "--- Running Semgrep Scan on agent-dynamic ---"
                sh '''
                    docker run --rm \
                        -v $PWD:/app \
                        -w /app \
                        returntocorp/semgrep \
                        semgrep --config auto --json > reports/semgrep.json
                '''
            }
        }
    }

    post {
        always {
            echo 'Archiving scan results...'
            archiveArtifacts artifacts: 'reports/*.json', allowEmptyArchive: true
            cleanWs()
        }
    }
}
