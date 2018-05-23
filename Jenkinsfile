pipeline {

    agent any

    enviroment {
        ENV_VAR = "My value"
    }

    stages {

        stage('build'){
            sh 'ant -f build.xml -v'
        }

    }

}