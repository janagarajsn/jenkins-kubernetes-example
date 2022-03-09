pipeline{
    agent any
    stages {
        stage('Build Maven') {
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/janagarajsn/jenkins-kubernetes-example.git']]])
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                  sh 'docker build -t janagarajs/nodejsapp-1.0:latest .'
                }
            }
        }
        stage('Deploy Docker Image') {
            steps {
                script {
                 withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u janagarajs -p ${dockerhubpwd}'
                 }  
                 sh 'docker push janagarajs/nodejsapp-1.0:latest'
                }
            }
        }
    
        stage('Deploy App on k8s') {
            steps {
                sshagent(['k8s']) { //create ssh agent through pipeline syntax
                    // Public IP of the K8s  Master
                    sh "scp -o StrictHostKeyChecking=no nodejsapp.yaml ubuntu@35.180.204.238:/home/ubuntu"
                    script {
                        try{
                            sh "ssh ubuntu@35.180.204.238 kubectl apply -f ."
                        }catch(error){
                            sh "ssh ubuntu@35.180.204.238 kubectl create -f ."
                        }
                    }
                }
          
            }
        }
    }
}
