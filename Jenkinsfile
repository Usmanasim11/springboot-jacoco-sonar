pipeline {
    agent any
    
    stages {
//         stage('Checkout') {
//             steps {
//                 checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/mhassanmanzoorsl/spring-boot-jacoco.git']])
//             }
//         }

        stage('Build') {
            steps {
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
        stage('Publishing Code Covergae') {
            steps {
                jacoco()
            }   
        }
//         stage('SonarQube Analysis') {
//             steps {
//                 script {
//                     def mvn = tool 'Default Maven'
//                     withSonarQubeEnv {
//                         sh "mvn clean verify sonar:sonar -Dsonar.projectKey=sonar-maven -Dsonar.host.url=http://20.102.67.142:9000 -Dsonar.login=sqp_ed3f8bd8268695c62a75b456d1b339c4c4225b64"
//             }
//         }
//     }
// }       
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh '''
                        mvn clean verify sonar:sonar -Dsonar.projectKey=sonar-maven -Dsonar.host.url=http://20.102.67.142:9000 -Dsonar.login=sqp_ed3f8bd8268695c62a75b456d1b339c4c4225b64
                    '''
                 }
             }
         }
        
        stage('Docker') {
            steps {
                sh 'docker version'
                sh 'docker build -t ahmedsystems/springbootjacoco:0.0.1 -f Dockerfile .'
                withDockerRegistry(credentialsId: 'ahmedsystems-dockerhub', url: 'https://index.docker.io/v1/') {
                sh 'docker push ahmedsystems/springbootjacoco:0.0.1'
                }
             }
         }
        
//         stage('Image push to local Docker registry') {
//             steps {
//                 sh 'docker version'
//                 sh 'docker build -t docker-registry:5000/springbootjacoco:0.0.1 -f Dockerfile .'
//                 withDockerRegistry(credentialsId: 'docker_reg_cred_k8', url:'https://docker-registry:5000/v2/') 
//                  {
//                  sh 'docker push docker-registry:5000/springbootjacoco:0.0.1'
//                  }
//              }
//          }
        // stage('Deploy to K8 Cluster') {
        //     steps {
        //         sshagent(['k8s_master_ssh_key']) {
        //             sh 'scp -r -o StrictHostKeyChecking=no springbootappk8s.yaml root@192.168.1.60:/'
        //             script{
        //             try{
        //                 sh 'ssh root@192.168.1.60 kubectl apply -f /springbootappk8s.yaml --kubeconfig=/root/.kube/config'
        //                }
        //             catch(error)
        //                {}
        //             }
    
        //         }
        //     }
        // }
    }
}
