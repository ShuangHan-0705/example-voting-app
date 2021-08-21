def remote = [:]
remote.name = 'web server'
remote.host = '155.138.159.234'
remote.user = 'root'
remote.password = '3Ru#[2q6TnH%Cr57'
remote.allowAnyHosts = true
pipeline {
  agent any
  
  environment {
    DISABLE_AUTH = 'true'
    DB_ENGINE    = 'sqlite'
  }
  
  stages {
    stage('Static Check') {
      steps {
        sh 'echo Static Check'
      }
    } 
    stage('Unit Testing') {
      steps {
        sh 'echo Unit Testing'
      }
    }
    stage('Cypress Integration Testing') {
      steps {
        sh 'echo Start Cypress Integration Testing'
        sh './getCypressCode.sh'
        sh './executeCypressTest.sh'
     }
    }
    stage('Build Result Image') {
      steps {
        sh 'printenv'
        sh 'docker build -t nzleoliang/result ./result'
        echo 'Build result completed'
      }
    } 
    stage('Build Vote Image') {
      steps {
        sh 'docker build -t nzleoliang/vote ./vote'
      }
    }
    stage('Build Worker Image') {
      steps {
        sh 'docker build -t nzleoliang/worker ./worker'
      }
    }
    stage('E2E Testing') {
      steps {
        parallel(
          Chrome: {
            echo "Running E2E Testing on Chrome"
          },
          Firefox: {
            echo "Running E2E Testing on Firefox"
          },
          Safari: {
            echo "Running E2E Testing on Safari"
          }
        )
      }    
    } 
    stage('Push Result Image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'') {
          sh 'docker push nzleoliang/result'
        }
      }
    }
    stage('Push Vote Image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'') {
          sh 'docker push nzleoliang/vote'
        }
      }
    }
    stage('Push Worker Image') {
      when {
        expression {
          return env.GIT_BRANCH == "origin/master"
        }
      }
      steps {
        withDockerRegistry(credentialsId: 'dockerhubid', url:'') {
          sh 'docker push nzleoliang/worker'
        }
      }
    }
    stage('Deployment') {
      steps {
        // sshCommand remote: remote, command: 
        sh 'pwd && ls -l '
        sh 'docker-compose pull && ' +
        'docker-compose down && ' +
        'docker-compose up -d && ' + 
        'docker-compose ps'
      }
    }
  }
}
