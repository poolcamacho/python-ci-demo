pipeline {
    agent any
    
    environment {
        SONAR_SCANNER_HOME = tool 'SonarScanner'
        SONAR_TOKEN = credentials('sonarcloud-token')
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', credentialsId: 'github-token', url: 'https://github.com/poolcamacho/python-ci-demo.git'
            }
        }
        
        stage('Setup Python') {
            steps {
                sh '''
                set -e
                python3 -m venv venv
                . venv/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                set -e
                . venv/bin/activate
                PYTHONPATH=$(pwd) pytest tests/ --maxfail=3 --disable-warnings -v
                '''
            }
        }
        
        stage('SonarCloud Analysis') {
            steps {
                sh '''
                set -e
                . venv/bin/activate
                ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                    -Dproject.settings=sonar-project.properties \
                    -Dsonar.organization=poolcamacho \
                    -Dsonar.projectKey=python-ci-demo \
                    -Dsonar.sources=src \
                    -Dsonar.host.url=https://sonarcloud.io \
                    -Dsonar.login=${SONAR_TOKEN}
                '''
            }
        }
    }

    post {
        always {
            sh 'deactivate || true'
        }
    }
}