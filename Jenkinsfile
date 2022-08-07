
pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }

    stages {

        stage('Get code from SCM'){
            steps{
                git branch: 'master', url: 'https://github.com/tolani-michael/Weather-App.git'
            }
        }

        stage('Build & Push docker image'){
            steps{
                sh 'docker build -t madusonovi/helm .'
                sh 'echo "${PASSWORD}" docker login -u ${USERNAME} --password-stdin'
                sh 'docker push madusonovi/helm'
            }
            
        }


        stage('Deploy to Helm') {
                 steps {
                    withAWS(credentials: 'jenkins-aws', region: 'us-east-1') {
                        sh 'aws eks --region  us-east-1 update-kubeconfig --name project'
                        sh 'cat $KUBECONFIG'
                        sh 'helm install weather-app ./weatherapp --values weatherapp/values.yaml'
                    }
                    
                        
                }

            }
           
        }
    }

    post {
        always {
            emailext  (body: "${currentBuild.currentResult}: Job <b>${env.JOB_NAME}</b> completed!! <br><br> More info at: ${env.BUILD_URL}", subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}", to: 'madusonovidius@gmail.com', mimeType: 'text/html');
            deleteDir()
        }
    }
