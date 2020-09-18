pipeline{
    agent none  
    stages{
        stage("Worker Build"){
                agent{
                  docker{
             image 'maven:3.6.1-jdk-8-slim'
             args '-v $HOME/.m2:/root/.m2'
              }
         }    
    
            when{
                changeset "**/worker/**"
                }
            steps{
                echo 'Building worker..'
                sh 'mvn -f worker/pom.xml compile'
            }
        }
        stage("Worker Test"){
            when{
                changeset "**/worker/**"
                }
                    agent{
        docker{
             image 'maven:3.6.1-jdk-8-slim'
             args '-v $HOME/.m2:/root/.m2'
              }
         }    
    
            steps{
                echo 'Testing worker..'
                sh 'mvn -f worker/pom.xml test'
            }
        }
        stage("Worker Packaging"){
            when{
                changeset "**/worker/**"
                branch 'master'
                }
                    agent{
        docker{
             image 'maven:3.6.1-jdk-8-slim'
             args '-v $HOME/.m2:/root/.m2'
              }
         }    
    
            steps{
                echo 'Packaging Worker'
                sh 'mvn -f worker/pom.xml package -DskipTests'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            } 
        }
        stage('docker-package-worker'){
           agent any
           when{
               changeset "**/worker/**"
               branch 'master' 
               }
           steps{
               echo 'Packaging worker app with docker'
               script{
                     docker.withRegistry('https://index.docker.io/v1/','dockerlogin'){
                     def workerImage = docker.build("ramrajkandasamy/worker:v${env.BUILD_ID}","./worker")
                     workerImage.push()
                     workerImage.push("latest")
                     }
                     }
               }
        
           }   
      stage("Build Vote") {
      agent {
        docker {
          image 'python:2.7.16-slim'
          args '-u root'
        }
      }
      when {
        changeset "**/vote/**"
      }
      steps {
        echo 'Building Vote..'
        dir('vote') {
          sh 'pip install -r requirements.txt'
        }
      }
    }
    stage("Test Vote") {
      agent {
        docker {
          image 'python:2.7.16-slim'
          args '-u root'
        }
      }
      when {
        changeset "**/vote/**"
      }
      steps {
        echo 'Testing vote..'
        dir('vote') {
          sh 'pip install -r requirements.txt'
          sh 'nosetests -v'
        }
      }
    }
    stage('docker-package-vote') {
      agent any
      when {
        changeset "**/vote/**"
        branch 'master'
      }
      steps {
        echo 'Packaging vote app with docker'
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
            def workerImage = docker.build("ramrajkandasamy/vote:v${env.BUILD_ID}", "./vote")
            workerImage.push()
            workerImage.push("master")
          }
        }
      }
    }
     stage("Build Result") {
      agent {
        docker {
          image 'node:8.16.0-alpine'
        }
      }
      when {
        changeset "**/result/**"
      }
      steps {
        echo 'Building result..'
        dir('result') {
          sh 'npm install'
        }
      }
    }
    stage("Test Result") {
      agent {
        docker {
          image 'node:8.16.0-alpine'
        }
      }
      when {
        changeset "**/result/**"
      }
      steps {
        echo 'Testing result..'
        dir('result') {
          sh 'npm test'
        }
      }
    }

    stage('docker-package-result') {
      agent any
      when {
        changeset "**/result/**"
        branch 'master'
      }
      steps {
        echo 'Packaging result app with docker'
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin') {
            def workerImage = docker.build("ramrajkandasamy/result:v${env.BUILD_ID}", "./result")
            workerImage.push()
            workerImage.push("master")
          }
        }
      }
    }   
    }
}
