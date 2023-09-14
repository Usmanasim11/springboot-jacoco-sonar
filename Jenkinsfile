pipeline {
    agent any
//     tools {
//       maven 'maven'
//   }
    stages {

        stage('Build') {
            steps {
                sh 'mvn -version'
                // sh 'mvn clean install'
                sh 'mvn clean package'
               // sh 'mvn clean package -Dmaven.test.failure.ignore=true'
            }
        }
    }
}
