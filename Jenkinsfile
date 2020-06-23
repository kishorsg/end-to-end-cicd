node {
        stage('SCM Checkout') {
        git 'https://github.com/kishorsg/end-to-end-cicd'
        }
        stage('Compile-Package') {
        // Get maven home path
        def mvnHome =  tool name: 'maven', type: 'maven'
        sh "${mvnHome}/bin/mvn clean package"
        }

   /*stage('SonarQube Analysis') {
       def mvnHome =  tool name: 'maven', type: 'maven'
        withSonarQubeEnv('sonar') {
        sh "${mvnHome}/bin/mvn sonar:sonar"
        }
   }
   stage ('TestNG result'){

    sh "[$class : 'Publisher', reportFilenamePattern : '**/ /*testng-result.xml']"
  }*/

    stage ('Build Docker Image') {
        sh 'docker build -t kishorsg/my-app:2.0.0 .'
    }

        stage('Push Docker image') {
        withCredentials([string(credentialsId: 'docker-pwd', variable: 'dockerHubPwd')]) {
            sh "docker login -u kishorsg -p ${dockerHubPwd}"
        }
        sh 'docker push kishorsg/my-app:2.0.0'
        }
    stage ('Terraform Init') {
        print 'Init Provider'
        sh 'terraform init'
    }

    stage ('Terraform Validate') {
        print 'Validating The TF Files'
        withCredentials([string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                      string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
            sh 'terraform validate'
                      }
    }

    stage ('Terraform Plan') {
        print 'Planning The TF Files'
        withCredentials([string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                      string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
            sh 'terraform plan'
                      }
    }

    stage ('Terraform Apply') {
        print 'Execute the plan'
        withCredentials([string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                      string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
            sh 'terraform apply -auto-approve'
                      }
    }
    /* stage ('Terraform Destroy') {
        print 'Destroy the resources'
        withCredentials([string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY_ID'),
                      string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_ACCESS_KEY')]) {
       sh 'terraform destroy -auto-approve'

                      }
    }*/
}

