pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building with Maven...'
                bat 'mvn clean package -DskipTests'
            }
        }
        
        stage('JaCoCo Code Coverage') {
            steps {
                echo 'Generating JaCoCo code coverage report...'
                bat 'mvn jacoco:report'
            }
        }
        
        stage('Test') {
            steps {
                echo 'Running unit tests...'
                bat 'mvn test'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline finished.'
        }
        success {
            echo 'Build and tests completed successfully!'
            publishHTML([
                reportDir: 'target/site/jacoco',
                reportFiles: 'index.html',
                reportName: 'JaCoCo Code Coverage Report'
            ])
        }
        failure {
            echo 'Build failed. Check console output for details.'
        }
    }
}