pipeline {
    agent any
    stages {
        stage('Build API') {
                    steps {
                        sh 'cd API/spring-petclinic-rest-master && nohup mvn spring-boot:run &'
                    }
                }

        stage('Build Website') {
            steps {
                sh 'cd Website/static-content && curl https://jcenter.bintray.com/com/athaydes/rawhttp/rawhttp-cli/1.0/rawhttp-cli-1.0-all.jar -o rawhttp.jar && nohup java -jar ./rawhttp.jar serve . -p 4200 &'
                sh 'sleep 20'

            }
        }
         
        stage('Postman tests') {
            steps {
                sh 'newman run PostmanFiles/Spring_PetClinic.postman_collection.json -e PostmanFiles/PetClinic_Environment.postman_environment.json -- reporters junit'
            }

        }

        stage('Robot Framework tests') {
            steps {
                sh 'robot --variable BROWSER:headlesschrome -d Website/Robotframework/Tests/Results Website/Robotframework/Tests'
            }
            post {
                always {
                    script {
                        step(
                            [
                                $class                  :   'RobotPublisher',
                                outputPath              :   'Website/Robotframework/Tests/Results',
                                outputFileName          :   '**/output.xml',
                                reportFileName          :   '**/report.html',
                                logFileName             :   '**/log.html',
                                disableArchiveOutput    :   false,
                                passThreshold           :   50,
                                unstableThreshold       :   40,
                                otherFiles              :   "**/*.png,**/*.jpg",
                            ]
                        )
                    }
                }
            }
        }

       
    }
}


