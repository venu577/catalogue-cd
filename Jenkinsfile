pipeline {
    agent  {
        label 'AGENT-1'
    }

     environment { 
        appVersion = ''
        REGION = "us-east-1"
        ACC_ID = "454046153308"
        PROJECT = "roboshop"
        COMPONENT = "catalogue"
    }

     options {
        timeout(time: 30, unit: 'MINUTES') 
        disableConcurrentBuilds()
    }

    parameters {
        string(name: 'appVersion', description: 'image version of the application')
        choice(name: 'deploy_to', choices: ['dev', 'aq', 'prod'], description: 'pick the environment') 
    }

    //Build
    stages {
       
        stage('Docker Build') {
            steps {
                script {
                    withAWS(credentials: 'aws-creds', region: 'us-east-1') {
                        sh """
                            aws eks update-kubeconfig --region $REGION --name "$PROJECT-${params.deploy_to}"
                            kubectl get nodes
                            kubectl apply -f 01-namespace.yaml
                            sed -i "s/IMAGE_VERSION/${params.appVersion}/g" values-${params.deploy_to}.yaml
                            #helm upgrade --install $COMPONENT -f values-${params.deploy_to}.yaml -n $PROJECT .
                        """
                    }
                }
            }
        }

        stage('Unit Testing') {
            steps {
                script {
                   sh """
                        echo "unit tests"
                   """
                }
            }
        } 
       
    }

     post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'Hello Success'
        }
        failure { 
            echo 'Hello Failure'
        }
    }
}