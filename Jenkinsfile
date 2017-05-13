pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                // checkout scm
                stash includes:'**', name:'ws'
            }
        }
        stage('Build Backend') {
            agent {
                docker 'maven:3-alpine'
            }
            steps {
                withMaven {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Test Frontend') {
            agent {
                docker 'node:alpine'
            }
            steps {
                unstash 'ws'
                sh 'yarn install'
                sh 'yarn global add gulp-cli'
                sh 'gulp test'
            }
        }
        stage('Checkstyle') {
            agent {
                docker 'maven:3-alpine'
            }
            steps {
                unstash 'ws'
                withMaven {
                    sh 'mvn checkstyle:check'
                }
            }
        }
    /*
        stage('Backend') {
            agent {
                docker 'maven:3-alpine'
            }
            steps {
                parallel(
                    'Performance' : {
                        unstash 'ws'
                        unstash 'war'
                        sh './mvnw -B gatling:execute'
                    })
            }
        }
        stage('Build Container') {
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2 -v /var/run/docker.sock:/var/run/docker.sock'
                }
            }
            steps {
                unstash 'ws'
                unstash 'war'
                sh './mvnw -B docker:build'
            }
        }
        stage('Deploy to Staging') {
            agent any
            steps {
                echo "Let's pretend a deployment is happening"
            }
        }
        stage('Deploy to production') {
            agent any
            steps {
                input message: 'Deploy to production?', ok: 'Fire zee missiles!'
                echo "Let's pretend a production deployment is happening"
            }
        }
*/
    }
}

