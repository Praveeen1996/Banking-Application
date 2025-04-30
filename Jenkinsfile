pipeline{
    agent any
    tools {
  jdk 'jdk17'
  nodejs 'nodejs'
}
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/Praveeen1996/New-Update-Application.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=newupdate \
                    -Dsonar.projectKey=newupdate'''
                    sha256:94ccf926cab370e5f0bab44c5db75389fcb8ce3b3ce1d9f1883a42a326b858b6
                }
            }
        }
        stage("quality gate"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){
                       sh "docker build -t newapplication ."
                       sh "docker tag youtube praveenhema/newapplication:latest "
                       sh "docker push praveenhema/newapplication:latest"
                    }
                }
            }
        }
        stage("deploy_docker"){
            steps{
                sh "docker run -d --name newapplication -p 3000:3000 praveenhema/newapplication:latest"
            }
        }
    }
}
