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
                sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
            }
        }

        stage ("Running on CentOS") {
            agent {
                label 'CentOS'
            }

            steps {
                sh "wget http://192.168.56.3/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
            }
        }

        stage ("Running on Debian") {
            agent {
                docker "openjdk:latest"
            }

            steps {
                sh "wget http://192.168.56.3/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
                sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
            }
        }

        stage ("Promote to Green") {
            agent {
                label 'apache'
            }

            when {
                branch 'master'
            }

            steps {
                sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
            }
        }

        stage ("Promote development branch to master") {
            agent {
                label "apache"
            }

            when {
                branch 'development'
            }

            steps {
                echo "Stashing Any Local Changes"
                sh "git stash"
                echo "Checking out development branch"
                sh "git checkout development"
                echo "Checking out master branch"
                sh "git checkout master"
                echo "Merging Development into master branch"
                sh "git merge development"
                echo "Pushing to origin master"
                sh "git push origin master"
            }
        }

    }


}