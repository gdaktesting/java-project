pipeline {

    agent {
        label 'master'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '2'))
    }

    stages {

        stage('Unit Test') {
            steps {
                sh 'ant -f test.xml -v'
                junit 'reports/result.xml'
            }
        }

        stage('Build') {
            steps {
                sh 'ant -f build.xml -v'
            }
        }

        stage('Deploy') {

            steps {
                echo 'Build number: ${env.BUILD_NUMBER}'
                sh 'cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/'
            }

        }

    }

    post {
        always {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
    }

}