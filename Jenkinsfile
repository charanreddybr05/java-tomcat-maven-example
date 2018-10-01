pipeline {
    environment {
        registry = "charanreddybr05/shopizer"
        registryCredential = 'docker-hub-credentials'
    }
    agent none
    //agent {
    //    docker {
    //        image 'maven:3-alpine'
    //        args '-v /root/.m2:/root/.m2'
    //    }
    //}
    stages {
        stage('Clone repository') {
            agent any
        /* Let's make sure we have the repository cloned to our workspace */
            steps {
                checkout scm
            }
        }
        stage('Build') {
            agent {
            docker {
                image 'maven:3-alpine'
                args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Building image') {
            agent any
            steps {
                script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Push image') {
            agent any
            steps {
                //script {
                //docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                //app.push("${env.BUILD_NUMBER}")
                //app.push("latest")
                //}
                //}
                script {
                    docker.withRegistry( '', registryCredential ) {
                    dockerImage.push()
                    }
      }
        }
        }
        stage('Deploy Kubernetes') {
            agent any
        steps {
        sh 'kubectl create -f tomcat-rc.yaml --VERSION=${BUILD_NUMBER}'
        sh 'kubectl create -f tomcat-svc.yaml'
        }
  }
}
}
