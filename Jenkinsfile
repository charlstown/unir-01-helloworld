pipeline {
    agent any
    stages {
        stage('Setup') {
            steps {
                // Add the safe.directory and clone the repository
                sh 'git config --global --add safe.directory /var/jenkins_home/workspace/O24/test-1'
                git 'https://github.com/charlstown/unir-cp01-a.git'
            }
        }
        stage('Build') {
            steps {
                // No build needed
                echo '[Build] No build needed'
                sh '''
                ls -la
                echo $WORKSPACE
                '''
            }
        }
        stage('Tests') {
            parallel {
                stage('Unit') {
                    environment {
                        PYTHONPATH="/var/jenkins_home/workspace/O24/cp1-1-dev"
                    }
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'Failure'){
                            // Run Pytest unit tests
                            sh 'python3 -m pytest --junitxml=result-unit.xml test/unit'
                        }
                        
                    }
                }
                stage('Rest') {
                    environment {
                        PYTHONPATH="/var/jenkins_home/workspace/O24/cp1-1-dev"
                    }
                    steps {
                        // Run flask app
                        catchError(buildResult: 'UNSTABLE', stageResult: 'Failure'){
                            sh'''
                            export FLASK_APP=app/api.py
                            echo "Simulating slow flask startup..."
                            sleep 30 &&
                            flask run &
                            python3 -m pytest --junitxml=result-rest.xml test/rest
                            '''
                        }
                    }
                }
            }
        }
        stage('Results') {
            steps {
                // Get results
                junit 'result*.xml'
            }
        }
    }
}