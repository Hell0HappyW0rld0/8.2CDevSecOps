pipeline {
    agent any

    // I messed up and implement a working script instead. I've chosen to remove some of the working tool to accomodate the changes.

    environment {
        // Define any environment variables
        DIRECTORY_PATH = '/code'
        TESTING_ENVIRONMENT = 'testing-environment'
        PRODUCTION_ENVIRONMENT = 'production-environment'
        // Other Jenkins tools variables that might be needed - i.e. Maven and SonarQube name, credentials, etc...
        // ...
    }

    stages {
        // Stage 1: Clone the repository from GitHub
        stage('Checkout') {
            steps {
                // Using Git to fetch the source code from the GitHub repository
                git branch: 'main', url: 'https://github.com/Hell0HappyW0rld0/8.2CDevSecOps.git'
                echo "Fetching source code from: ${env.DIRECTORY_PATH}"
                // Build automation can be done using Maven
            }
        }

        // Stage 2: Install dependencies using npm
        stage('Install Dependencies') {
            steps {
                // Using npm (Node Package Manager) to install dependencies from package.json
                sh 'npm install'
            }
        }

        // Stage 3: Run unit tests, which should also send notification email
        stage('Run Tests') {
            steps {
                sh 'npm test || true' // Using npm to allows pipeline to continue despite test failures
                
                // Using Maven to run unit and integration tests seperately
                //echo "Running unit tests"
                //sh "${MAVEN_HOME}/bin/mvn test"
                //echo "Running integration tests"
                //sh "${MAVEN_HOME}/bin/mvn verify -DskipUnitTests=true"
            }

            post {
                always {
                    // Using the Email Extension Plugin to send an email with status and attach the log
                    emailext (
                        subject: "Test Stage: ${currentBuild.currentResult}",
                        body: "The 'Run Tests' stage finished. Status: ${currentBuild.currentResult}",
                        to: 'nguyenhaanr00034@gmail.com',
                        attachLog: true
                    )
                }
            }
        }

        // Stage 4: Generate coverage report
        stage('Generate Coverage Report') {
            steps {
                // Using npm to ensure coverage report exists, even if it fail
                sh 'npm run coverage || true'

                // Running code analysis using SonarQube, stored credentials in Jenkins
                //withCredentials([string(credentialsId: 'SONARQUBE_TOKEN', variable: 'SONARQUBE_TOKEN')]) {
                //    sh "${MAVEN_HOME}/bin/mvn sonar:sonar -Dsonar.login=${SONARQUBE_TOKEN}"
                //}
            }
        }

        // Stage 5: Perform security scan, which should also send notification email
        stage('NPM Audit (Security Scan)') {
            steps {
                // using npm audit to scan dependencies for known vulnerabilities
                sh 'npm audit || true' // This will show known CVEs in the output

                // Security Scan using OWASP Dependency Check - using NVD API (Can take up to an hour)
                //sh "${MAVEN_HOME}/bin/mvn org.owasp:dependency-check-maven:check"
            }

            post {
                always {
                    // Using the Email Extension Plugin to send an email with status and attach the log
                    emailext (
                        subject: "Security Scan Stage: ${currentBuild.currentResult}",
                        body: "The 'Security Scan' stage finished. Status: ${currentBuild.currentResult}",
                        to: 'nguyenhaanr00034@gmail.com',
                        attachLog: true
                    )
                }
            }
        }

        // Other stages include deploy to staging, integration test on staging, and deploy to production 
        // using AWS EC2 instance or locally
    }
}
