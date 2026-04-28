pipeline {
    agent any

    stages {
        stage('Compile') {
            steps {
                echo 'Compiling Code'
		sh 'mvn compile -f java-demo-app/pom.xml'
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
		sh 'mvn package -DskipTests -f java-demo-app/pom.xml'
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
		sh 'docker image build -t java-demo-app:v1.0.0 -f java-demo-app/Dockerfile .'
            }
        }
        stage('RenameImage') {
            steps {
                echo 'Tagging an Image'
		sh 'docker image tag java-demo-app:v1.0.0 harbor.registry.local/jenkinsprojects1/javademoapp:v1.0.0'
            }
        }
	 stage('LoginToHarbor') {
            steps {
                echo 'Logging into Harbor Registry'
		sh ' "Harbor12345" | docker login $REGISTRY -u "admin" --password-stdin '
            }
        }
	 stage('PushImage') {
            steps {
                echo 'Push image to Harbor Registry'
		sh 'docker image push harbor.registry.local/jenkinsprojects1/javademoapp:v1.0.0'
            }
        }
        stage('RunContainer') {
            steps {
                echo 'Running Image Inside Container'
            }
        }
    }
}

