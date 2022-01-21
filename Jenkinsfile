#!groovy

pipeline {

    agent any

    triggers {
        githubPush()
    }

    stages {

        stage('Checkout') {
            steps {
                deleteDir()
                checkout scm
            }
        }

        stage('Deploy Config') {
             steps {
                 script {
                     sh """
                         git config user.email "jenkins@rgare.com"
                        git config user.name "jenkins"
                        git tag -a "v1.3" -m "Release of version v1.3"
                        git push origin v1.3
                    """
                }
             }
        }
    }

}