#!groovy

import groovy.json.JsonOutput
import java.util.Optional
import hudson.tasks.test.AbstractTestResultAction
import hudson.model.Actionable
import hudson.tasks.junit.CaseResult

pipeline {

    agent any

    environment {
    }

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
                        mkdir -p ~/.ssh && cp ${keyfile} ~/.ssh/id_rsa
                        git checkout origin master
                        git fetch --tags --force
                        git tag --sort=committerdate | grep -E '^v[0-9]' | tail -1
                    """
                     sh """ 
                    version=\$(git tag --sort=committerdate | grep -E '^v[0-9]' | tail -1)
                    #Version to get the latest tag 
                    A="\$(echo \$version|cut -d '.' -f1)"
                    B="\$(echo \$version|cut -d '.' -f2)"
                    C="\$(echo \$version|cut -d '.' -f3)"
                    
                    if [ \$C -gt 8 ]
                    then 
                        if [ \$B -gt 8 ]
                        then
                            A=\$((A+1))
                            B=0 C=0 
                        else
                            B=\$((B+1))
                            C=0
                        fi
                    else
                        C=\$((C+1))
                    fi

                    echo "A[\$A.\$B.\$C]">outFile

                    """
                     nextVersion = readFile 'outFile'
                    echo "we will tag '${nextVersion}'"
                    result = nextVersion.substring(nextVersion.indexOf("[")+1,nextVersion.indexOf("]"))
                    echo "we will tag '${result}'"

                     sh """
                        mkdir -p ~/.ssh && cp ${keyfile} ~/.ssh/id_rsa
                        git config user.email "jenkins@rgare.com"
                        git config user.name "jenkins"
                        git tag -a "${result}" -m "Release of version ${result}"
                        git push origin "${result}"
                    """
                }
             }
        }
    }

}