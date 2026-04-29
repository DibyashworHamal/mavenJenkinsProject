pipeline {
    agent any
    environment{
	REGISTRY='harbor.registry.local'
	}
    stages {
        stage('Compile') {
            steps {
                echo 'Compiling Code'
		sh 'mvn compile -f */pom.xml'
            }
        }
       stage('UnitTest') {
            steps {
                echo 'Start Unit Testing'
            }
        }
        stage('SecurityScan') {
            steps {
                echo 'Scan Vulnerability'
            }
        }
        stage('QualityGate') {
            steps {
                echo 'QGate'
            }
        }
	 stage('BuildApp') {
            steps {
                echo 'Building Java-demo-app'
		sh 'mvn clean package -DskipTests -f */pom.xml'
            }
	    post{
	       success{
		   archiveArtifacts artifacts: '*/*/*.war', followSymlinks: false, onlyIfSuccessful: true
		}
 	   }
        }

        stage('CreateImage') {
            steps {
                echo 'Creating Docker Image'
		sh 'docker image build -t java-demo-app:${BUILD_NUMBER} -f java-demo-app/Dockerfile .'
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
        stage('RunContainer') {
            steps {
                echo 'Running Image Inside Container'
            }
        }
    }
}

