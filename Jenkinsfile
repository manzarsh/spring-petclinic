pipeline {
    agent any

    triggers {
        // Every 5 minutes on Mondays
        cron('H/5 * * * 1')
    }

    options { timestamps() }

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Test + JaCoCo Coverage') {
            steps {
                // JaCoCo HTML report will be generated in target/site/jacoco/
                sh 'mvn -B clean test jacoco:report'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                    archiveArtifacts artifacts: 'target/site/jacoco/**', allowEmptyArchive: true
                }
            }
        }

        stage('Package Artifact') {
            steps {
                sh 'mvn -B -DskipTests package'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
    }
}
