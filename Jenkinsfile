pipeline {

    agent none

    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '2'))
    }

    stages {

        stage('Unit Test') {
            agent {
                label 'master'
            }
            steps {
                sh 'ant -f test.xml -v'
                junit 'reports/result.xml'
            }
        }

        stage('Build') {
            agent {
                label 'master'
            }

            steps {
                sh 'ant -f build.xml -v'
            }

            post {
                success {
                    archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
                }
            }
        }

        stage('Deploy') {
            agent {
                label 'master'
            }
            steps {
                echo "Build number: ${env.BUILD_NUMBER}"
                sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/"
            }
        }

        stage ("Running on CentOS") {
            agent {
                label 'CentOS'
            }

            steps {
                sh "wget http://192.168.56.3/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar  3 4"
            }
        }

    }


}