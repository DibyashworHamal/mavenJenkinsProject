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
        }

        stage('CreateImage') {
            steps {
                echo 'Creating Docker Image'
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

