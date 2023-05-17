pipeline{
    agent any;
    stages{
        stage ('check kubectl installed'){
            steps{
                sh '''kubectl version
                 if [ $? == 0 ]
                 then
                   echo "kubectl installed"
                 else
                   exit 1
                 fi
                '''
            }
        }
        stage("connectivity check to EKS"){
            steps{
               sh ''' kubectl get no
                 if [ $? == 0 ]
                 then
                   echo "connected to EKS"
                 else
                   exit 1
                 fi
                    '''
            }
        }
        stage("deploy mariadb"){
            steps{
                sh ''' helm upgrade --install devops-mariadb mariadb --values mariadb/createat-mariadb.yaml
                       sleep 30s
                       helm status devops-mariadb mariadb
                '''
            }
        }
        stage("java docker image build"){
            steps{
                sh ''' cd springboot/java-springboot
                       bash Build.sh   
                   #docker build -t malleshdevops/createat-devops-task:java-spring-v1 .
                   #docker push malleshdevops/createat-devops-task:java-spring-v1
                   '''
            }
        }
        stage("deploy java spring app"){
            steps{
                sh ''' 
                   helm upgrade --install devops-spring springboot --values springboot/create-task-java.yaml
                   sleep 30s
                   helm status devops-mariadb devops-spring
                '''
            }
        }
        /*stage("terraform apply"){
            steps{
                sh ''' kubectl apply -f react-deploy.yaml
                       sleep 10s
                       kubectl apply -f react-svc.yaml
                '''
            }
        }*/
    }
}
