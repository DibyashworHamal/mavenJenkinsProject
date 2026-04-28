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
        stage('PushImage') {
            steps {
                echo 'Push image to Harbor Registry'
            }
        }
        stage('RunContainer') {
            steps {
                echo 'Running Inage Inside Container'
            }
        }
    }
}

