pipeline {
    agent { label 'Node' }
    triggers {
       pollSCM('0 23 * * 1-5')
    }
    stages {
        stage('vcs') {
            steps {
                git url: 'https://github.com/March2023Sujata/spring-petclinic.git',
                    branch: 'qa'
            }
        }
        stage('build') {
            steps {
                sh 'mvn clean install'
            }
        }
            stage('aks') {
            steps {
                 sh 'terraform init'
                 sh 'terraform fmt'
                 sh 'terraform validate'
                 sh 'terraform apply -auto-approve'
                 sh 'mv kubeconfig ~/.kube/config'
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
                sh 'docker image build -t sujatajoshi/qa-spc:$BUILD_NUMBER .'
                sh 'docker image push sujatajoshi/qa-spc:$BUILD_NUMBER'
            }
        }
        stage('deploy') {
            steps {
                sh 'kubectl apply -f .'
                sh 'sleep 10s'
                sh 'kubectl get svc'
            }
        }
    }    
}
