node {
    

    def mvnHome = tool 'maven 3.9.16'

    stage('Environment Variables') {

        echo "Job Name: ${env.JOB_NAME}"
        echo "Build Number: DEV-${env.BUILD_NUMBER}"
        echo "Build ID: ${env.BUILD_ID}"
        echo "Workspace: ${env.WORKSPACE}"
        echo "Jenkins URL: ${env.JENKINS_URL}"
        echo "Node Name: ${env.NODE_NAME}"
        echo "Build URL: ${env.BUILD_URL}"
        echo "Git Branch: ${env.BRANCH_NAME}"

    }

    stage('Checkout') {
        git branch: 'main',
            url: 'https://github.com/rahulkumar0055/java-maven-app.git'
    }

    stage('Build') {
        sh "${mvnHome}/bin/mvn clean package"
    }

    stage('SonarQube Analysis') {
        withSonarQubeEnv('SonarQube') {
            sh """
                ${mvnHome}/bin/mvn sonar:sonar \
                -Dsonar.projectKey=java-maven-app \
                -Dsonar.projectName=java-maven-app
            """
        }
    }

    stage('Upload to Nexus') {
        sh "${mvnHome}/bin/mvn deploy"
    }

    stage('Deploy to Tomcat') {
    sshagent(['tomcat-ssh']) {
        sh '''
        scp -o StrictHostKeyChecking=no target/*.war ec2-user@172.31.44.71:/tmp/

        ssh -o StrictHostKeyChecking=no ec2-user@172.31.44.71 "
            sudo mv /tmp/*.war /opt/tomcat/webapps/
            sudo /opt/tomcat/bin/shutdown.sh
            sleep 5
            sudo /opt/tomcat/bin/startup.sh
        "
        '''
    }
}
}
