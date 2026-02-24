pipeline {
    agent any
    
    tools {
        // This tells Jenkins to use the Maven installation you configured
        maven 'Maven'  // Must match the name you gave in Global Tool Configuration
        jdk 'JDK 21'   // Must match the JDK name in Global Tool Configuration
    }
    
    triggers {
        // Trigger every 5 minutes on Mondays
        pollSCM('H/2 * * * *')
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub...'
                git branch: 'main',
                    url: 'https://github.com/DannyFu9993/spring-petclinic.git'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Building the project...'
                bat 'mvn clean compile -DskipTests=true'
            }
        }
        
        stage('Test with JaCoCo') {
            steps {
                echo 'Running tests with JaCoCo code coverage...'
                bat 'mvn test jacoco:report'
            }
            post {
                always {
                    // Publish JUnit test results
                    junit allowEmptyResults: true, testResults: 'target/surefire-reports/**/*.xml'
                    
                    // Publish JaCoCo coverage report
                    jacoco(
                        execPattern: 'target/jacoco.exec',
                        classPattern: 'target/classes',
                        sourcePattern: 'src/main/java',
                        exclusionPattern: 'src/test*'
                    )
                    
                    // Archive the JaCoCo HTML report
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'target/site/jacoco',
                        reportFiles: 'index.html',
                        reportName: 'JaCoCo Coverage Report'
                    ])
                }
            }
        }
        
        stage('Package') {
            steps {
                echo 'Packaging the application...'
                bat 'mvn package -DskipTests=true'
            }
            post {
                success {
                    // Archive the generated artifact
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline execution completed!'
        }
        success {
            echo 'Build succeeded! Artifact and JaCoCo report generated.'
        }
        failure {
            echo 'Build failed. Check the console output for errors.'
        }
    }
}
