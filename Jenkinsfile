def credential = 'app-key'
def userdock = 'menther'
def server = 'menther@10.36.116.205'
def directory = 'literatur-frontend'
def url = 'https://github.com/frenkyst/literatur-frontend.git'
def branch = 'Production'
def image = 'literature-frontend'

pipeline{
  agent any
  stages{
     stage('Pull From Frontend Repo') {
         steps {
            sshagent([credential]) {
                sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                cd ${directory}
                # docker compose down
                docker system prune -f
                git remote add origin ${url} || git remote set-url origin ${url}
                git pull ${url} ${branch}
                exit
                EOF"""
              }
          }
      }
      stage('Building Docker Images'){
          steps{
              sshagent ([credential]) {
                  sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
                  cd ${directory}
                  # docker container stop ${userdock}-${image}
                  # docker container rm ${userdock}-${image}
                  docker compose down
                  docker rmi ${userdock}/${image}
                  docker rmi ${image}
                  docker compose up -d
                  exit
                  EOF"""
              }
          }
      }
      stage('Push to Docker Hub') {
            steps {
                sshagent([credential]) {
            sh """ssh -o StrictHostkeyChecking=no ${server} << EOF
            docker tag ${image}:latest ${userdock}/${image}:latest
            docker image push ${userdock}/${image}:latest
            exit
            EOF"""
          }
      }
      }
  }

}
