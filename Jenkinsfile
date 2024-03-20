pipeline {
    agent any
    stages{
        stage('Build Maven'){
            steps{
                git url:'https://github.com/karthikhr97/cicdakshat/', branch: "master"
               sh 'mvn clean install'
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t karthikhr97/endtoendproject6march:v5 .'
                }
            }
        }
          stage('Docker login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker_cred', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push karthikhr97/endtoendproject6march:v5'
                }
            }
        }
           stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube') {
                    sh './mvnw clean org.sonarsource.scanner.maven:sonar-maven-plugin:3.9.0.2155:sonar'
                }
            }
        }
        stage('Kubernetes Login and Deploy') {
            when { expression { env.GIT_BRANCH == 'origin/master' } }
             steps {
                sshagent(['sshprivatekey']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.12.95 ls"
                }
                script {
                    kubernetesDeploy(configs: 'deploymentservice.yaml', kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
  }
