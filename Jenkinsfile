pipeline {
    agent any
    stages {
        stage('Clone Frontend') {
            steps {
                dir('frontend') {
                    git url: 'https://github.com/JirawatChn/ecms-frontend.git', branch: 'master', credentialsId: 'jirawatchn'
                }
            }
        }

        stage('Clone Backend') {
            steps {
                dir('backend') {
                    git url: 'https://github.com/Khanchai-pat/Project-CSI401.git', branch: 'main', credentialsId: 'jirawatchn'
                }
            }
        }

        stage('Docker Compose Up') {
            steps {
                bat 'docker-compose down || exit 0'
                bat 'docker-compose up -d --build'
                echo 'Frontend & Backend started Compose'
            }
        }

        stage('Clone Test Repo') {
            steps {
                dir('test') {
                    git url: 'https://github.com/JirawatChn/ecms-autotest.git', branch: 'main', credentialsId: 'jirawatchn'
                }
            }
        }

        stage('Run EMP Test') {
            steps {
             catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                    dir('test') {
                        bat "pip install robotframework"
                        bat "pip install robotframework-seleniumlibrary"
                        bat "robot -d reports/emp emp-ecms.robot"
                    }
                }
            }
            post {
                always {
                    publishHTML(target: [
                        reportDir: 'test/reports/emp',
                        reportFiles: 'report.html',
                        reportName: 'EMP Test Report',
                        keepAll: true,
                        alwaysLinkToLastBuild: true
                    ])
                }
            }
        }

        stage('Run HR Test') {
            steps {
                catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                    dir('test') {
                        bat "robot -d reports/hr hr-ecms.robot"
                    }
                }
            }
            post {
                always {
                    publishHTML(target: [
                        reportDir: 'test/reports/hr',
                        reportFiles: 'report.html',
                        reportName: 'HR Test Report',
                        keepAll: true,
                        alwaysLinkToLastBuild: true
                    ])
             }
            }
        }
    }
}
