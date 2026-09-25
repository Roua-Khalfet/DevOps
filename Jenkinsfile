pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven3'
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Récupération du code') {
            steps {
                echo 'Récupération du code depuis GitHub...'
                checkout scm
            }
        }

        stage('Tests unitaires (Backend)') {
            steps {
                echo 'Lancement des tests unitaires backend...'
                dir('backend') {
                    sh 'mvn test'
                }
            }
            post {
                always {
                    junit '**/backend/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Build du livrable (Backend)') {
            steps {
                echo 'Construction du livrable backend...'
                dir('backend') {
                    sh 'mvn package -DskipTests'
                }
            }
            post {
                success {
                    archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true
                }
            }
        }
    }

    post {
        failure {
            emailext(
                subject: "ÉCHEC du build : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>Le build du backend a échoué.</p>
                    <p><b>Job :</b> ${env.JOB_NAME}</p>
                    <p><b>Build :</b> #${env.BUILD_NUMBER}</p>
                    <p><b>Logs :</b> <a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></p>
                """,
                to: 'abdrita444@gmail.com',
                mimeType: 'text/html'
            )
        }
        success {
            echo 'Build du backend réussi !'
        }
    }
}