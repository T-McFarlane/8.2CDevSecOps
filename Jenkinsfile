/*
 * SIT223/SIT753 - Credit Task
 * Part 1, Task 2: DevSecOps Basics (nodejs-goof)
 * Part 2, Task 2: Extended Email Notification Plugin
 *
 * Author: Tom McFarlane
 *
 * This pipeline clones the nodejs-goof project from "8.2CDevSecOps"
 * GitHub repo, installs its dependencies, runs its test script, generates a
 * coverage report, and runs `npm audit` as a security scan. It then extends
 * that pipeline (Part 2, Task 2) by emailing the status + attached console
 * log at the end of the Run Tests and NPM Audit (Security Scan) stages.
 */
 
pipeline {
    agent any

  tools {
        nodejs 'NodeJS-a'
    }
 
    environment {
        EMAIL_RECIPIENT = 's225644394@deakin.edu.au'
    }
 
    stages {
 
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/T-McFarlane/8.2CDevSecOps.git'
            }
        }
 
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
 
        stage('Run Tests') {
            steps {
                sh 'npm test || true' // Allows pipeline to continue despite test failures
            }
            post {
                always {
                    emailext(
                        subject: "Jenkins - ${env.JOB_NAME} #${env.BUILD_NUMBER} - Run Tests: ${currentBuild.currentResult}",
                        body: "The 'Run Tests' stage of ${env.JOB_NAME} build #${env.BUILD_NUMBER} finished with status: ${currentBuild.currentResult}.\n\nSee the attached console log for full details.\n\nBuild URL: ${env.BUILD_URL}",
                        to: "${EMAIL_RECIPIENT}",
                        attachLog: false
                    )
                }
            }
        }
 
        stage('Generate Coverage Report') {
            steps {
                // Ensure coverage report exists
                sh 'npm run coverage || true'
            }
        }
 
        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || true' // This will show known CVEs in the output
            }
            post {
                always {
                    emailext(
                        subject: "Jenkins - ${env.JOB_NAME} #${env.BUILD_NUMBER} - Security Scan: ${currentBuild.currentResult}",
                        body: "The 'NPM Audit (Security Scan)' stage of ${env.JOB_NAME} build #${env.BUILD_NUMBER} finished with status: ${currentBuild.currentResult}.\n\nSee the attached console log for the full list of vulnerabilities found.\n\nBuild URL: ${env.BUILD_URL}",
                        to: "${EMAIL_RECIPIENT}",
                        attachLog: true
                    )
                }
            }
        }
    }
 
    post {
        always {
            echo "Pipeline finished with status: ${currentBuild.currentResult}"
        }
    }
}
