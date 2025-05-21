pipeline {
  agent none
  stages {
    stage('build') {
      steps {
        echo 'compile maven app'
        sh 'mvn compile'
      }
    }

    stage('test') {
      parallel {
        stage('test') {
          steps {
            echo 'test maven app'
            sh 'mvn clean test'
          }
        }

        stage('sleep-step') {
          steps {
            sleep 4
          }
        }

      }
    }

    stage('package') {
      parallel {
        stage('package') {
          steps {
            echo 'package maven app'
            sh 'mvn package -DskipTests'
            archiveArtifacts 'target/*.jar'
          }
        }

        stage('Docker BnP') {
          agent any
          steps {
            script {
              docker.withRegistry('https://index.docker.io/v1/', 'dockerloginstep') {
                def commitHash = env.GIT_COMMIT.take(7)
                def dockerImage = docker.build("vishaltom02/sysfoo:${commitHash}", "./")
                dockerImage.push()
                dockerImage.push("latest")
                dockerImage.push("dev")
              }
            }

          }
        }

      }
    }

  }
}