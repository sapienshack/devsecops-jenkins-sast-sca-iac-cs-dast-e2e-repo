pipeline {
    agent any

    environment {
        JAVA_HOME = "C:\\Program Files\\Java\\jdk-21" // Update this path if different
        PATH = "${JAVA_HOME}\\bin;${env.PATH}"
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        SNYK_TOKEN = credentials('SNYK_TOKEN')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Compile and Run SonarQube Analysis') {
            steps {
                bat '''
                    mvn -Dmaven.test.failure.ignore verify sonar:sonar ^
                    -Dsonar.login=%SONAR_TOKEN% ^
                    -Dsonar.projectKey=easybuggy ^
                    -Dsonar.host.url=http://localhost:9000/
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                    script {
                        app = docker.build("asecurityguru/testeb")
                    }
                }
            }
        }


        stage('Run DAST using ZAP') {
            steps {
                bat '''
                    C:\\ZAP\\zap.sh -port 9393 -cmd -quickurl https://www.example.com ^
                    -quickprogress -quickout C:\\zap\\ZAP_2.12.0_Crossplatform\\ZAP_2.12.0\\Output.html
                '''
            }
        }


    }

    post {
        always {
            echo 'Pipeline execution complete.'
        }
        failure {
            echo 'Pipeline failed. Check console output for details.'
        }
    }
}
