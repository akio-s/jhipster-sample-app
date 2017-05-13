pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                // checkout scm
                stash includes:'**', name:'ws'
            }
        }
        stage('Build Backend') {
            steps {
                withMaven {
                    sh 'mvn clean package'
                }
            }
        }
        stage('Test Frontend') {
            agent {
                docker 'shfx/node-phantomjs'
            }
            steps {
                unstash 'ws'
                sh 'yarn install'
                sh 'yarn global add gulp-cli'
                sh 'gulp test'
                /*
                  This fails:
                    [18:29:17] Starting 'test'...
                    [32m13 05 2017 18:29:19.163:INFO [karma]: [39mKarma v1.2.0 server started at http://localhost:9876/
                    [32m13 05 2017 18:29:19.164:INFO [launcher]: [39mLaunching browser PhantomJS with unlimited concurrency
                    [32m13 05 2017 18:29:19.169:INFO [launcher]: [39mStarting browser PhantomJS
                    [33m13 05 2017 18:30:19.172:WARN [launcher]: [39mPhantomJS have not captured in 60000 ms, killing.
                    [33m13 05 2017 18:30:21.173:WARN [launcher]: [39mPhantomJS was not killed in 2000 ms, sending SIGKILL.
                    [33m13 05 2017 18:30:23.175:WARN [launcher]: [39mPhantomJS was not killed by SIGKILL in 2000 ms, continuing.                  
                */
            }
        }
        stage('Checkstyle') {
            steps {
                withMaven {
                    sh 'mvn pmd:pmd'
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

