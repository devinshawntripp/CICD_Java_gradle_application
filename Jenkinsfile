pipeline{
    agent any 
    environment{
        PATH = "/usr/bin"
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("Sonar Quality Check"){
            agent {
                docker {
                    image 'openjdk:17'
                    args '--user 1000:1000'  // Add this line if you face permission issues
                }
            }
            steps{
                script{
                    echo "Starting SonarQube quality check..."

                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonar --debug --scan --stacktrace'
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
