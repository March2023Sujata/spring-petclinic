pipeline {
    agent { label 'Node' }
    triggers {
       pollSCM('* * * * *')
    }
    stages {
        stage('vcs') {
            steps {
                git url: 'https://github.com/March2023Sujata/spring-petclinic.git',
                    branch: 'main'
            }
        }
        stage('build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('docker') {
            steps {
                sh 'docker image build -t sujatajoshi/spc:latest .'
                sh 'docker image push sujatajoshi/spc:latest'
            }
        }
    }    
}
