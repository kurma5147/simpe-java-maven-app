pipeline {
  agent any
  tools {
    maven 'maven'
  }
  stages {
   stage ('Initialize') {
            steps {
                sh '''
                    M2_HOME=/opt/maven
                    M2=/opt/maven/bin
                    PATH=$PATH:$HOME/bin/:$JAVA_HOME:$M2:$M2_HOME
                    export PATH
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    whoami
                '''
            }
        }
    stage('Build app') {
      steps {
        sh 'mvn clean install package'
      }
    }
    stage('Push Artifact to S3') {
      steps {
        sh 'aws s3 cp webapp/target/webapp.war s3://mavenartifacts'
      }
    }
    
//     stage('Deploy to tomcat') {
//       steps {
//         sh 'sudo scp -i $tomcat_key -o "StrictHostKeyChecking=no" webapp/target/webapp.war ubuntu@18.191.57.72:/opt/tomcat/webapps'
//       }
//     }
    stage('building docker image from docker file by tagging') {
      steps {
        sh 'docker build -t   kurma5147/artifactimage:$BUILD_NUMBER .'
      }   
    }
    stage('logging into docker hub') {
      steps {
        sh 'docker login --username="kurma5147" --password="9908475147"'
      }   
    }
    stage('pushing docker image to the docker hub with build number') {
      steps {
        sh 'docker push kurma5147/artifactimage:$BUILD_NUMBER'
      }   
    }
    stage('deploying the docker image into EC2 instance and run the container') {
      steps {
        sh 'ansible-playbook deploy.yml --extra-vars="buildNumber=$BUILD_NUMBER"'
      }   
    }  
}
  post{
    always{
  
 stage('Emial Notifiacation'){
      mail bcc: '', body: '''Hi team,
      Pipeline is sucessfull...

Thanks and Regards,
Kurmarao.''', cc: '', from: '', replyTo: '', subject: 'jenkinsjob', to: 'kurma5147@gmail.com'
      }
}
  }
}
