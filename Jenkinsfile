pipeline {
    agent any
    tools {
        gradle 'Gradle 7.5.1'
    }
    stages {
        stage('Build') {
            steps {
                script{
                sh """
                cd rest
                gradle build
                """
            }
            }
        }
        stage('test with Sonarqube') {
            steps {
                script{
                    withSonarQubeEnv('Sonar'){
                        sh"""
                        cd rest 
                        gradle sonarqube \
                        -Dsonar.project=sonarqube \
                        -Dsonar.host.url=http://192.168.5.174:9000 \
                        -Dsonar.login=eb4293b6998fc56d3c8285fe8161184460aec0a2 \
                        -D sonar.sourceEncoding=UTF-8 \
                        -D sonar.language=java 
                        """
                    }
                }
            }
        }
        stage('Build Docker Image') {
            agent{
            label 'Docker_Slave'
            } 
            steps {
                script{
                 sh"""
                      cd rest
                      docker login -u $DOCKER_USER -p $DOCKER_PASSWORD
                      sudo docker build -t trion97/test:1.0 .
                      docker push $DOCKER_USER/test:1.0
                    """
                }
                echo 'succesfull'
            }
        }
        stage('Deployment') {
            steps {
                script{
                sh """
                az login --service-principal --username 8eca206b-667a-4436-b8a8-cf554837c081 --password xlK8Q~Gyc4VSLqrReh314L1KDAz971ApMEE6Iaja --tenant 374fe296-bb62-48b2-b064-75f1b0fae7ce 
                az account set --subscription 33414221-3b85-41aa-b5d1-5d349e46fee3
                az aks get-credentials --resource-group GrupoC3 --name GrupoC3
                cd rest/k8s
                kubectl apply -f micro_imagen.yaml
                """
                }
                echo 'succesfull'
            }
        }
    }
}
