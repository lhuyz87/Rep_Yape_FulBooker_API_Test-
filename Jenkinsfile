import java.text.SimpleDateFormat

def defDateFormat = new SimpleDateFormat("yyyyMMddHHmm")
def defDate = new Date()
def defTimestamp = defDateFormat.format(defDate).toString()

pipeline {

    agent any
    
    tools {
        maven 'M3'
        jdk 'jdk8.221'
    }
    
     options {
        buildDiscarder(logRotator(numToKeepStr: '20'))
        disableConcurrentBuilds()
    }
    
    
    stages {
    
        stage ('Build') {
            steps {
                bat ("mvn clean verify")
            }
        }
        
        stage ('Ejecutar Pruebas') {
        	steps {
        	
        		withVault([configuration: configuration, vaultSecrets: secrets]) {
        		script {
        			try {
        				
        				bat ("mvn test -Dcucumber.options=\"src/test/resources/features/ --tags \'${ESCENARIO}\' --glue yape.test.definition\"")
        				bat ("mvn serenity:aggregate")
        				echo 'Ejecucion de pruebas sin errores...'
        			}
        			catch (ex) {
        				echo 'Finalizo ejecucion con fallos...'
        				error ('Failed')
                    }
                    }
                }
            }
        }
        
        stage ('Reporte') {
        	steps {
        		script {
                     try {
                    	//bat ("echo ${WORKSPACE}")
                    	bat ("echo ${defTimestamp}")
                    	publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, keepAll: true, reportDir: "${WORKSPACE}/target/site/serenity", reportFiles: 'index.html', reportName: 'Evidencias de Prueba', reportTitles: 'Reporte de Pruebas'])
                    	//publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: "${WORKSPACE}/target/site/serenity${defTimestamp}", reportFiles: 'index.html', reportName: 'Evidencias de Prueba', reportTitles: ''])
                    	//publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: "${WORKSPACE}\\target\\site\\serenity${defTimestamp}", reportFiles: 'index.html', reportName: 'Evidencias de Prueba', reportTitles: ''])
                        echo 'Reporte realizado con exito'
                    }

                    catch (ex) {
                        echo 'Reporte realizado con Fallos'
                        error ('Failed')
                    }
                }
            }
        }
    }
}
