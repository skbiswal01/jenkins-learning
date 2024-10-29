pipeline {
    agent any

    stages {
        stage("Checkout") {
            steps {
                script {
                    sh "ls"  // Lists the initial files in the workspace (if any)
                    git branch: 'main', url: 'https://github.com/skbiswal01/jenkins-learning.git'
                    sh "ls"  // Lists files again, showing the contents of the repository after checkout
                }
            }
        }

        stage("Build") {
            steps {
                sh "./mvnw package"  // Runs the Maven package command to build the project
            }
        }

        stage("Run Tests") {
            parallel {
                stage("Tests A") {
                    steps {
                        sh "echo test set A"
                        sleep 2
                        sleep 3
                    }
                }
                stage("Tests B") {
                    steps {
                        sh "echo test set B"
                        sleep 4
                    }
                }
                stage("Tests C") {
                    steps {
                        sleep 1
                        // Uncomment below line to fail this stage intentionally
                        // sh "false"
                    }
                }
            }
            failFast true
        }

        stage("Capture") {
            steps {
                archiveArtifacts artifacts: '**/target/*.jar'
                jacoco()  // Generates code coverage reports using JaCoCo
                junit keepProperties: true, keepTestNames: true, testResults: '**/target/surefire-reports/TEST*.xml'
            }
        }
    }

    post {
        always {
            emailext(
                body: "${env.BUILD_URL}\n${currentBuild.absoluteUrl}",
                to: 'always@foo.bar',
                recipientProviders: [[$class: 'DevelopersRecipientProvider']],
                subject: "${currentBuild.currentResult}: Job ${env.JOB_NAME} [${env.BUILD_NUMBER}]"
            )
        }
    }
}
