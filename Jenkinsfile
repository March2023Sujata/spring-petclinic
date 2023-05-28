pipeline {
    agent { label 'Node' }
   triggers {
       pollSCM('*/30 * * * *')
    }
    stages {
        stage('vcs') {
            steps {
                git url: 'https://github.com/March2023Sujata/spring-petclinic.git',
                    branch: 'develop'
            }
        }
        stage('build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('aks') {
            steps {
                dir('terraform'){
                    sh 'terraform init'
                    sh 'terraform apply -auto-approve'
                    sh 'mv kubeconfig ~/.kube/config'
                }
            }
        }
        stage('post build') {
	        steps {
		      archiveArtifacts artifacts: '**/target/spring-petclinic-3.0.0-SNAPSHOT.jar',
				 onlyIfSuccessful: true
		      junit testResults: '**/surefire-reports/TEST-*.xml'
		    }
	    }
        stage('docker') {
            steps {
                    sh 'docker image build -t sujatajoshi/dev-spc:$BUILD_NUMBER .'
                    sh 'docker image push sujatajoshi/dev-spc:$BUILD_NUMBER'
            }
        }
    }    
}
