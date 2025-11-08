pipeline {
    agent any

    stages {
        stage('Checkout Vulnerable Project') {
            steps {
                echo "Cloning vulnerable-demo from GitHub..."
                git branch: 'main', url: 'https://github.com/MR8anem/vulnerable-demo.git'
                
                // Ensure reports directory exists
                sh "mkdir -p reports"
            }
        }

        stage('Run Analysis Agents') {
            parallel {
                stage('Static Analysis (agent-static)') {
                    agent {
                        docker { image 'python:3.9-slim' }
                    }
                    steps {
                        echo "--- Running Bandit on agent-static ---"
                        // Scripts are in security-scans repo, clone it first or call via Jenkins workspace
                        sh "git clone https://github.com/MR8anem/security-scans.git ../security-scans-scripts"
                        sh "chmod +x ../security-scans-scripts/run_bandit.sh"
                        sh "../security-scans-scripts/run_bandit.sh"
                    }
                }

                stage('Dynamic Analysis (agent-dynamic)') {
                    agent {
                        docker { image 'python:3.9-slim' }
                    }
                    steps {
                        echo "--- Running Semgrep on agent-dynamic ---"
                        sh "git clone https://github.com/MR8anem/security-scans.git ../security-scans-scripts"
                        sh "chmod +x ../security-scans-scripts/run_semgrep.sh"
                        sh "../security-scans-scripts/run_semgrep.sh"
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Archiving all reports...'
            archiveArtifacts artifacts: 'reports/*.json', allowEmptyArchive: true
            cleanWs()
        }
    }
}
