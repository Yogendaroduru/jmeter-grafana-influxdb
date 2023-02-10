pipeline {
    agent any
	
    stages {
		stage('Clean up existing data') {
			steps {
				dir('kubernetes-init/data') {
					sh 'rm -rf *'
					//echo "current build number: ${currentBuild.number}"
				}
			}
		}
		stage('Pull Jmeter Image from registery') {
			steps {
			    sh 'docker pull saikrishnaoduru/jmeter:latest'
			}
		}
        stage('Get latest Build from repository') {
            steps {
                echo 'Pulling latest script from GIT'    
                sh 'git clone https://github.com/Yogendaroduru/jmeter-grafana-influxdb.git'
            }
        }
        stage('Build Kubernetese Config') {
            steps {    
                sh 'mkdir -p `pwd`/kubernetes-init/data/;cp `pwd`/data/First.jmx `pwd`/kubernetes-init/data/First.jmx;'
                sh 'mkdir -p `pwd`/kubernetes-init/data/logs/;mkdir -p `pwd`/kubernetes-init/data/results/;'
                echo 'kubernetes configuration for Jmeter Masters and Slaves created successfully'
                //echo "current build number: ${currentBuild.number}"
            }
        }
        stage('Deploying Jmeter Slave Pods') {
            steps {
				sh 'cd `pwd`/kubernetes-init/; kubectl apply -f config/'
              
                echo 'Started Jmeter Slave Pods'
            }
        }
		stage('Deploying JMeter Master and Start Test') {
            steps {
				echo 'Starting Execution'
                sh 'cd `pwd`/kubernetes-init/config/; kubectl apply -f master.yaml'
				sh 'kubectl logs master -f'
                
            }
        }
		stage('Archive data') {
			steps {
				dir('kubernetes-init/data') {
					archiveArtifacts artifacts: '**'
				}
			}
		}
    }
}
