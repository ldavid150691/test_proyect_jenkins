pipeline {
	agent any
    environment {
		PROJECT_NAME='INTEGRATION TEST JENKINS'
    }
    stages {
		stage('Etapa 0') {
			steps {
				echo "Proyecto ${PROJECT_NAME}"
				echo "Folder name for release: ${currentBuild.startTimeInMillis}"
			}
		}
        stage('Etapa 1 - Validaciones MAVEN') {
            steps {
	    	echo "***Version MAVEN"	
                bat "mvn -version"
		echo "***Validacion MAVEN"	
                bat "mvn validate"
            }
        }
	stage('Etapa 2 - Test solucion') {
            steps {
                script{
                    echo = bat (script:"mvn test", returnStdout: true)
                    
                }
            }
        }
	stage('Etapa 3 - Compilar solucion') {
            steps {
                script{		   
                    echo bat (script:"mvn compile", returnStdout: true)
                    
                }
            }
        }
        stage('Etapa 4 - Empaquetar solucion') {
            steps {
                bat "mvn package"
            }
        }
        stage('Etapa 5 - Creacion de huellas') {
            steps {
                script{
                    def JAR = findFiles(glob: '**/target/*.jar')
                    bat "certutil -hashfile ${JAR[0].path} SHA256 | findstr /v hash > print_compiler.txt"
                }
                
            }
        }
        stage('Etapa 6 - Distribucion a ambiente productivo') {
            steps {
                script{
                    def JAR = findFiles(glob: '**/target/*.jar')
		   bat("mkdir C:\\jenkins_route\\Prod\\${currentBuild.startTimeInMillis} & move /Y ${WORKSPACE}\\${JAR[0].path} C:\\jenkins_route\\Prod\\${currentBuild.startTimeInMillis}")			   
                    bat("xcopy print_compiler.txt C:\\jenkins_route\\Prod\\${currentBuild.startTimeInMillis} /Y")
                  }
            }
        }
        stage('Etapa 7 - Distribution a ambiente pre productivo') {
            steps {
	    	bat("mkdir C:\\jenkins_route\\PreProd\\${currentBuild.startTimeInMillis}")
                bat("xcopy ${WORKSPACE} C:\\jenkins_route\\PreProd\\${currentBuild.startTimeInMillis} /E /S /H /Y")	
            }
        }
    }
	post {
		always {
            echo "Resultado: ${currentBuild.currentResult}"
        }
        aborted {
            echo "BUILD ABORTED"
        }
        success {
            echo "BUILD SUCCESS"          
        }
        unstable {
            echo "BUILD UNSTABLE"
        }
        failure {
            echo "BUILD FAILURE"			
        }
	}
}
