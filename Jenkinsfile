pipeline {
    agent any
    environment{
        def mavenhome=tool name : 'MyMaven'
    }
    parameters {
        string(name: 'PERSON', defaultValue: 'Mr Ram', description: 'Who are you?')
        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')
        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')
    }
    options {
   buildDiscarder(logRotator(numToKeepStr: '5'))
   timeout(time: 60, unit: 'SECONDS')
        timestamps()
    }
    stages {
        stage('Parameters') {
            steps {
                sh 'sleep 10'
                
                echo "Hello ${params.PERSON}"

                echo "Toggle: ${params.TOGGLE}"

                echo "Choice: ${params.CHOICE}"

            }
        }
        stage('Clone Repo') {
            steps {
                echo 'Going to Checkout from Git'
                git branch: 'master', url: 'https://github.com/ValaxyTech/SimpleCustomerApp.git'
                echo 'Completed Checkout from Git'
            }
        }
        stage('Sonarqube Analysys') {
              def scannerhome=tool name : 'SonarqubeScanner';
                withSonarQubeEnv('SonarqubeScanner') {
    //          sh "$mavenhome/bin/mvn sonar:sonar"
        }
        stage('Maven Build') {
            steps{
                sh '$mavenhome/bin/mvn clean package'
                echo "Maven build tool executed successfully"
            }
        }
        stage('store artifact in Nexus Repo') {
            steps{
                nexusPublisher nexusInstanceId: 'RamNexus', nexusRepositoryId: 'RamMaven', packages: []
            }
        }
        stage('Deploy') {
            steps{
        sshagent(['PipelineTomcatDeployment']) {
           sh "scp -o StrictHostKeyChecking=no webapp/target/webapp.war ec2-user@54.206.111.81:/opt/tomcat/webapps"
        }
            }
        }
    }
}
