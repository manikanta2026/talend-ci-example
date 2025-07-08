/*  Jenkinsfile  –  CustomerLoggerJob CI/CD
    Repo:  https://github.com/manikanta2026/talend-ci-example.git
    Purpose:  Checkout, unzip Talend job, run it, and push logs to console.
*/

pipeline {
    agent any

    /* ---------- GLOBAL ENVIRONMENT ---------- */
    environment {
        JAVA_HOME = "C:\\Program Files\\Java\\jdk-21"   // adjust if different
        JOB_NAME  = "CustomerLoggerJob"                 // Talend job folder
    }

    /* ---------- RUNTIME PARAMS ---------- */
    parameters {
        string(name: 'CSV_FILE_PATH',
               defaultValue: 'C:/TalendStudio/customers.csv',
               description: 'Absolute path to customers.csv on the Jenkins node')
    }

    /* ---------- BUILD STAGES ---------- */
    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/manikanta2026/talend-ci-example.git'
                    // If the repo is private, add: credentialsId: 'github-creds'
            }
        }

        stage('Unzip Talend Job') {
            steps {
                /* Auto‑detect the ZIP (first file that ends *_Job_*.zip) */
                bat '''
                    for %%F in ("*Job_*.zip") do (
                        powershell -NoProfile -Command ^
                          "Expand-Archive -Force \"%%~nxF\" -DestinationPath job2"
                        goto :done
                    )
                    :done
                '''
            }
        }

        stage('Verify Contents (debug)') {
            steps {
                bat 'dir job2'
            }
        }

        stage('Run Talend Job') {
            steps {
                bat """
                    cd job2\\%JOB_NAME%
                    call %JOB_NAME%_run.bat --context=Default ^
                    --context_param CSV_FILE_PATH=%CSV_FILE_PATH% ^
                """
            }
        }
    }

    /* ---------- POST‑BUILD ---------- */
    post {
        always {
            // Clean workspace to avoid ZIP/version clashes next build.
            cleanWs()
        }
    }
}
