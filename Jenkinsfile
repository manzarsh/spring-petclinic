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
                sh 'mvn -B clean test jacoco:report'
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml'
                    archiveArtifacts artifacts: 'target/site/jacoco/**', allowEmptyArchive: true
                    script {
                        try {
                            jacoco execPattern: '**/target/jacoco.exec',
                                   classPattern: '**/target/classes',
                                   sourcePattern: '**/src/main/java',
                                   changeBuildStatus: true
                        } catch (e) {
                            echo "JaCoCo plugin not available - using archived HTML report."
                        }
                    }
                }
            }
        }

        stage('Package Artifact') {
            steps { sh 'mvn -B -DskipTests package' }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
    }
}
