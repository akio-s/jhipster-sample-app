pipeline {
    agent any
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                withMaven {
                    sh 'mvn clean package'
                }
            }
            post {
                always {
                    jacoco()
                }
            }
        }
        stage('Test Frontend') {
            agent {
                docker 'shfx/node-phantomjs'
            }
            steps {
                sh 'yarn install'
                sh 'yarn global add gulp-cli'
                sh 'gulp test'
            }
            post {
                always {
                    junit 'target/test-results/karma/*.xml'
                }
            }
        }
        // Maybe jacoco + lcov?
        stage('Static Analysis') {
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                withMaven {
                    sh 'mvn pmd:pmd'
                }
            }
            post {
                always {
                    pmd pattern:'target/pmd.xml'
                }
            }
        }
        stage('Deploy to QA') {
            when {
                branch 'qa'
            }
            agent {
                docker {
                    image 'maven:3-alpine'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh './deploy.sh qa'
                withMaven {
                    sh 'mvn gatling:execute -DbaseURL=http://app1.qa.acme.com/'
                }
            }
        }
        stage('Deploy to Production') {
            when {
                branch 'production'
            }
            agent any
            steps {
                input message: 'Deploy to production?', ok: 'Fire zee missiles!'
                echo 'Let's pretend a production deployment is happening'
                sh './deploy.sh prod'
            }
        }
    }
}

