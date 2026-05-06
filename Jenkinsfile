pipeline {
    agent any
    environment{
	REGISTRY='harbor.registry.local'
	}
    stages {
        stage('Compile') {
            steps {
                echo 'Compiling Code'
		sh 'mvn compile -f java-demo-app/pom.xml'
            }
        }
       stage('UnitTest') {
        agent {
            label 'ubuntu-vm1'
        }
            steps {
                echo 'Start Unit Testing'
            }
        }
        stage('SecurityScan') {
            agent {
            label 'ubuntu-vm1'
        }
            steps {
                echo 'Scan Vulnerability'
            }
        }
        stage('QualityGate') {
            agent {
            label 'ubuntu-vm1'
        }
            steps {
                echo 'QGate'
            }
        }
	 stage('BuildApp') {
            steps {
              echo 'Building Java-demo-app'
		      sh 'mvn clean package -DskipTests -f java-demo-app/pom.xml'
            }
	     post{
	       success{
		    archiveArtifacts artifacts: 'java-demo-app/*/*.war', followSymlinks: false, onlyIfSuccessful: true
		  }
 	     }
        }

        stage('CreateImage') {
            steps {
                echo 'Creating Docker Image'
                sh '''
                cd java-demo-app  
		        docker image build -t java-demo-app:${BUILD_NUMBER} .
                '''
            }
        }
        stage('RenameImage') {
            steps {
                echo 'Tagging an Image'
		        sh 'docker image tag java-demo-app:${BUILD_NUMBER} ${REGISTRY}/jenkinsprojects1/javademoapp:${BUILD_NUMBER}'
            }
        }
	 stage('LoginToHarbor') {
            steps {
                echo 'Logging into Harbor Registry'
		withCredentials([usernamePassword(credentialsId: 'harbor-password', 
                                          usernameVariable: 'USER', 
                                          passwordVariable: 'PASS')]) {
		sh "echo '${PASS}' | docker login ${REGISTRY} -u '${USER}' --password-stdin"
		}
            }
        }
	 stage('PushImage') {
            steps {
                echo 'Push image to Harbor Registry'
		sh 'docker image push ${REGISTRY}/jenkinsprojects1/javademoapp:${BUILD_NUMBER}'
            }
        }
        /*stage('RunContainer') {
            steps {
                echo 'Running Image Inside Container'
		        sh '''
		        docker container stop tomcatContainer || true
		        docker container rm tomcatContainer || true
		        docker container run -itd --name tomcatContainer -p 8086:8080 ${REGISTRY}/jenkinsprojects1/javademoapp:${BUILD_NUMBER}
		        '''
            }
        } */

        stage('Deploy to production environment') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    input message: 'Approve PRODUCTION Deployment?'
                }
                echo 'Running app on production environment'
		        sh '''
		        docker container stop tomcatContainerprod || true
		        docker container rm tomcatContainerprod || true
		        docker container run -itd --name tomcatContainerprod -p 8087:8080 ${REGISTRY}/jenkinsprojects1/javademoapp:${BUILD_NUMBER}
		        '''
            }
        }
    }
    post {
        always {
            echo 'Cleaning up workspace and Docker images'
            sh '''
            docker image rm java-demo-app:${BUILD_NUMBER} || true
            docker image rm ${REGISTRY}/jenkinsprojects1/javademoapp:${BUILD_NUMBER} || true
            '''
            cleanWs()
        }
        success {
            mail bcc: '', body: """Hello Team,
            BUILD #${BUILD_NUMBER} of Java Demo App was successful.
            You can find the build details at: ${env.BUILD_URL}
            and verify the details
            Regards,
            DevOps Team""", cc: '', from: '', replyTo: '', subject: "Build #${BUILD_NUMBER} Successful - Java Demo App",
            to: 'hamaldivyashwor2057@gmail.com'
        }
        failure {
            mail bcc: '', body: """Hello Team,
            BUILD #${BUILD_NUMBER} of Java Demo App has failed.
            Please check the build details at: ${env.BUILD_URL}
            and investigate the issue.
            Regards,
            DevOps Team""", cc: '', from: '', replyTo: '', subject: "Build #${BUILD_NUMBER} Failed - Java Demo App",
            to: 'hamaldivyashwor2057@gmail.com'
        }
    }
}

