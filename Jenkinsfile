pipeline {
    agent any
    stages {
        stage('Make Shared Directory') {
            steps {
                sh 'mkdir -p $HOME/.tmp; chmod 777 $HOME/.tmp'
            }
        }
        stage('Run everything in docker') {
            agent {
                docker {
                    image 'maven:3.5-alpine'
                    args '--volume $HOME/.tmp:/tmp/.tmp'
                }
            }
            stages {
                stage('Build') {
                    steps {
                        sh 'mvn -B -DskipTests clean package'
                    }
                }
                stage('Test') {
                    steps {
                        sh 'mvn test'
                    }
                    post {
                        always {
                            junit 'target/surefire-reports/*.xml'
                        }
                    }
                }
                stage('Deliver') {
                    steps {
                        sh './jenkins/scripts/deliver.sh'
                    }
                }
            }
        }
        stage('Upload') {
            agent any
            steps {
                sh '/usr/local/bin/aws s3 cp $HOME/.tmp/*.jar s3://afrldigen-afrldle-installs/jenkins-test/'
            }
        }
    }
}
