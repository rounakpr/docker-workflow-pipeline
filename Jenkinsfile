node('admin') {

      stage('scm Checkout') {
            git credentialsId: 'Github', url: 'https://github.com/rounakpr/docker-workflow-pipeline.git'
      }

      stage('mvn Package') {
            // calling mvn from path variable
            //def mvnHome = tool name: 'MVN', type: 'maven'
            //def mvnCMD = "${mvnHome}/bin/mvn"
            //sh "${mvnCMD} clean package"
            sh "mvn clean package"
      }

      stage('Build Docker Image') {
            //sh "docker build -t rounakpr/my-app:2.0.0 ."
            sh "docker build -t rounakpr/my-app:1.0.${env.BUILD_ID} ."
      }

      stage('Push Docker Image') {
            withCredentials([string(credentialsId: 'dockerHubPwd', variable: 'dockerhubPwd')]){
                  sh "docker login -u rounakpr -p ${dockerHubPwd}"
            }
            sh "docker push rounakpr/my-app:1.0.${env.BUILD_ID}"
      }

      stage('Run Container on Server') {
            def dockerRun = "docker run -p 8080:8080 -d --name my-app rounakpr/my-app:1.0.${env.BUILD_ID}"
            sshagent(['dev-Server']) {
                  sh "ssh -o StrictHostKeyChecking=no ec2-user@10.75.116.13 ${dockerRun}"
            }
      }
}
