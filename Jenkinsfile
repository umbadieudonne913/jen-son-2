pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('sonar-token')
        SONAR_HOST = 'http://localhost:9000'
        PROJECT_KEY = 'jen-son-1'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/umbadieudonne913/jen-son-1.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'sonar-scanner'
                    withSonarQubeEnv('SonarQube') {
                        sh """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=$PROJECT_KEY \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=$SONAR_HOST \
                        -Dsonar.login=$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage('Check Quality Gate') {
            steps {
                script {
                    // On attend 5 secondes pour que l'analyse soit disponible
                    sleep 5

                    // Appel de l'API SonarQube pour récupérer le statut
                    def status = sh(
                        script: """curl -s -u $SONAR_TOKEN: $SONAR_HOST/api/qualitygates/project_status?projectKey=$PROJECT_KEY | jq -r '.projectStatus.status'""",
                        returnStdout: true
                    ).trim()

                    echo "Quality Gate Status: ${status}"

                    if (status != 'OK') {
                        error "Pipeline failed: Quality Gate status is ${status}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'echo Build OK'
            }
        }
    }
}
