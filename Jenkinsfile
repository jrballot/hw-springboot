pipeline {

    agent any
    
    tools {
        maven 'maven'
    }

    stages {

        stage ('Build && Test') {
            steps {
                sh "mvn clean package -DskipTests"
            }
            post {
                success {
                    sh "echo Collecting report"
                    junit 'target/surefire-reports/**/*.xml'
                }
                failure {
                    sh "echo Error"
                }
            }
        }
        stage ('QA') {
            steps {
                withSonarQubeEnv('SonarQube Server') {
                    sh 'mvn sonar:sonar  -Dsonar.projectVersion=${BUILD_ID} -DskipTests '
                }

            }
        }
        stage ('Archive') {
            steps {
                sh "mvn package deploy -DskipTests"
            }
        }
        stage ('Deploy') {
            steps {
                sh "mvn wildfly:deploy -DskipTests"
            }
        }
    }

    post {
        always {
            cleanDir()
        }
        success {
            mail to: "cursoci@localhost.localdomain", subject: "MSG", body: "Pipeline finalizado com sucesso"
        }
        failure {
            mail to: "cursoci@localhost.localdomain", subject: "MSG", body: "Pipeline finalizado com erro"

        }
    }

}
