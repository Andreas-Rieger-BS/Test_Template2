pipeline {
    agent any 
    stages {
        stage('Build') {
            steps {
                echo 'Hello world!' 
            }
        }
        stage('SonarQube analysis') {
            steps {
                script {
                    scannerHome = tool 'sonarqubeScannerInstallation'// must match the name of an actual scanner installation directory on your Jenkins build agent
                }
                withSonarQubeEnv('sonarqubeInstallation') {// If you have configured more than one global server connection, you can specify its name as configured in Jenkins
                  sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage('bomBuilder') {
            steps {
                sh """                
                    git config --global http.proxy ${GLOBAL_AGENT_HTTP_PROXY}
                    git config --global https.proxy ${GLOBAL_AGENT_HTTPS_PROXY}
                    git clone https://github.com/Andreas-Rieger-BS/Test_Template2.git
                    /cyclonedx-linux-x64 add files --no-input --output-format xml --exclude /.git/** --exclude cyclonedx-linux-x64 --output-file bom.xml --base-path ~/Test_Template2/
                    mv bom.xml ~/Test_Template2/
                    cd ~/Test_Template2
                    git remote set-url origin https://${GITHUB_AUTH}@github.com/${GITHUB_USERNAME}/Test_Template2.git
                    git push origin
                    cd /var/lib/jenkins/workspace
                """
            }
        }
        stage('dependencyTrackPublisher') {
            steps {
                 dependencyTrackPublisher artifact: 'bom.xml', projectName: 'Test_Template2', projectVersion: '1.0', synchronous: true
            }
        }
    }
}
