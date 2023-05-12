pipeline {
    agent any
    
    stages {

        stage('Build') {
            steps {
                sh 'mvn -version'
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
                        mvn clean verify sonar:sonar -Dsonar.projectKey=sonar-maven -Dsonar.host.url=http://20.102.67.142:9000 -Dsonar.login=sqp_ed3f8bd8268695c62a75b456d1b339c4c4225b64
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
