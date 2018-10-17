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
                    //junit 'target/surefire-reports/**/*.xml'
                }
                failure {
                    sh "echo Error"
                }
            }
        }

        stage ('QA') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn sonar:sonar  -Dsonar.projectVersion=${BUILD_ID} -DskipTests '
                }

            }
        }

	stage ("SonarQube Quality Gate") { 
		//steps {
		//	script {
        	//		timeout(time: 1, unit: 'HOURS') { 
        	//   			def qg = waitForQualityGate() 
        	//   			if (qg.status != 'OK') {
        	//     				error "Pipeline aborted due to quality gate failure: ${qg.status}"
        	//   			}
        	//		}
		//	}
		//}

		steps {
			timeout(time: 1, unit: 'MINUTES') {
				waitForQualityGate abortPipeline: true
			}
		}

	}	

        stage ('Archive') {
            steps {
                sh "mvn package deploy -DskipTests"
            }
        }
        
	stage ('Deploy Wildfly') {
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
