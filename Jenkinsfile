pipeline{
    agent any
    tools {
  jdk 'jdk19'
  nodejs 'nodejs version 19'
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
                git branch: 'main', url: 'https://github.com/Praveeen1996/newupgrade.git'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar-server') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=newupgrade \
                    -Dsonar.projectKey=newupgrade'''
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
                       sh "docker build -t newupgrade ."
                       sh "docker tag youtube praveenhema/newupgrade:latest "
                       sh "docker push praveenhema/newupgrade:latest"
                    }
                }
            }
        }
        stage("deploy_docker"){
            steps{
                sh "docker run -d --name newupgrade -p 3000:3000 praveenhema/newupgrade:latest"
            }
        }
    }
}
stage('Deploy Container') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying Docker container...'
                sh '''
                    docker rm -f ${praveenhema} || true
                    docker run -d --banking-appliction ${praveengema} -p 3000:3000 ${REGISTRY}/${praveenhema}:${banking-application}
                '''
            }
        }
}
