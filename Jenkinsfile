pipeline {
    agent any
    tools {
      maven 'maven'
  }
    stages {

        stage('Build') {
            steps {
               // sh 'mvn -version'
                sh 'mvn clean install'
                sh 'mvn clean package'
               // sh 'mvn clean package -Dmaven.test.failure.ignore=true'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
                //sh 'mvn test -Dmaven.test.failure.ignore=true'
            }
        }
        
        stage('Publishing Junit Tests report ') {
            steps {
                junit 'target/surefire-reports/*.xml'
            }   
        }
        stage('Publishing Code Coverage') {
            steps {
                jacoco()
            }   
        }      
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                        mvn clean verify sonar:sonar -Dsonar.projectKey=Spring-Boot -Dsonar.host.url=http://40.88.142.73:9000 -Dsonar.login=sqp_6cbab81b6b5aa2afe0fa97689c409c2f16f4f241
                    '''
                 }
             }
         }
           
        stage('Image push to local Docker registry') {
            steps {
                sh 'docker version'
                sh 'docker build -t dockerregistry.com/springbootjacoco:0.0.1 -f Dockerfile .'
                withDockerRegistry(credentialsId: 'dockerlocalregistry-cred', url: 'https://dockerregistry.com/v2/') 
                 {
                 sh 'docker push dockerregistry.com/springbootjacoco:0.0.1'
                 }
             }
         }
        stage('Deploy to K8 Cluster') {
            steps {
                sshagent(['k8s_master_ssh_key']) {
                    sh 'scp -r -o StrictHostKeyChecking=no springbootappk8s.yaml root@40.76.219.249:/'
                    script{
                    try{
                        sh 'ssh root@40.76.219.249 kubectl apply -f /springbootappk8s.yaml --kubeconfig=/root/.kube/config'
                       }
                    catch(error)
                       {}
                    }
    
                }
            }
        }
    }
}
