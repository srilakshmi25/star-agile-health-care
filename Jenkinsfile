pipeline{
    agent any
    stages{
        stage('checkout the code from github'){
            steps{
                 git url: 'https://github.com/srilakshmi25/star-agile-health-care'
                 echo 'github url checkout'
            }
        }
        stage('codecompile with sri'){
            steps{
                echo 'starting compiling'
                sh 'mvn compile'
            }
        }
        stage('codetesting with akshat'){
            steps{
                sh 'mvn test'
            }
        }
        stage('qa with akshat'){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('package with akshat'){
            steps{
                sh 'mvn clean package'
            }
        }
        
        stage('run dockerfile'){
          steps{
               sh 'docker build -t vedanshsri/healthcareimage .'
           }
         }
        stage('push dockerfile'){
          steps{
				withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerpwd', usernameVariable: 'dockeruser')]) {
				sh "docker login -u ${env.dockeruser} -p ${env.dockerpwd}"
				sh 'docker push vedanshsri/healthcareimage:latest'
					}
               
           }
         }
        stage("Execute Ansible") {
			
            steps {
                 //sh "sudo chmod 777 apache.yml"
                 //sh "sudo chmod 777 hosts"
                ansiblePlaybook credentialsId: 'ansible', disableHostKeyChecking: true, installation: 'ansible', inventory: 'hosts', playbook: 'ansible-playbook.yml', vaultTmpPath: ''                 
            }    
        }  
		 stage('Deploy to k8s'){
            when{ expression {env.GIT_BRANCH == 'master'}}
            steps{
                script{
                     kubernetesDeploy (configs: 'deploymentservice.yaml' ,kubeconfigId: 'k8sconfigpwd')
                   
                }
            }
			}
    }
       }
    
