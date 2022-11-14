pipeline {
     agent any
     stages {
         stage('Build') {
              steps {
                  sh 'echo Building...'
              }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Lint Docker File') {
              steps {
                  sh 'make lint'
              }
         }
         stage('Build Docker Image') {
              steps {
                  sh 'docker build -t capstone-project-cloud-devops .'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry([url: "", credentialsId: "docker-hub"]) {
                      sh "docker tag capstone-project-cloud-devops himanshuluthra/capstone-project-cloud-devops"
                      sh 'docker push himanshuluthra/capstone-project-cloud-devops'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'aws', region: 'us-east-1') {
                      sh "aws eks --region us-east-1 update-kubeconfig --name capstonecluster --verbose"
                      echo '.....Cleared Step 1.........'
                      sh "kubectl config use-context arn:aws:eks:us-east-1:911745291225:cluster/capstonecluster"
                      echo '.....Cleared Step 2.........'
                      sh "kubectl set image deployments/capstone-project-cloud-devops capstone-project-cloud-devops=himanshuluthra/capstone-project-cloud-devops:latest"
                      echo '.....Cleared Step 3.........'
                      sh "kubectl apply -f deployment/deployment.yml"
                      echo '.....Cleared Step 4.........'
                      sh "kubectl get nodes"
                      echo '.....Cleared Step 5.........'
                      sh "kubectl get deployment"
                      echo '.....Cleared Step 6.........'
                      sh "kubectl get pod -o wide"
                      echo '.....Cleared Step 7.........'
                      sh "kubectl get service/capstone-project-cloud-devops"
                      echo '.....Cleared Step 8.........'
                  }
              }
        }
        stage("Cleaning up") {
              steps{
                    echo 'Cleaning up...'
                    sh "docker system prune"
              }
        }
     }
}
