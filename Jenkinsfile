pipeline{
    agent any 
    stages{
        stage('Test SH') {
            steps {
                script {
                    echo 'Testing sh command outside withSonarQubeEnv'
                    sh 'which sh'
                }
            }
        }
        stage("Sonar Quality Check"){
            agent {
                docker {
                    image 'openjdk:21'
                }
            }
            steps{
                script{
                    echo "Starting SonarQube quality check..."

                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        echo 'Before running sh command'
                        sh 'which sh'
                        echo 'After running sh command'
                        sh 'chmod +x gradlew'
                        sh './gradlew clean build'
                        sh './gradlew sonarqube --debug --scan --stacktrace'
                        echo "SonarQube scan completed."
                    }

                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                        echo "Quality gate passed."
                    }
                }  
            }
        }
    }
}
