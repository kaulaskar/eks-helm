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
                       helm status devops-mariadb
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
                   helm status devops-spring
                '''
            }
        }
       stage("updating java lb url into reactjs app"){
            steps{
                sh '''
                  url=`kubectl get svc |grep java-springboot-svc |cut -d " " -f10`
                  cd react-frontend/src/services
                  sed s/backendurl/$url/g EmployeeService.js >test.js
                  count=`grep 'backendurl' test.js|wc -l`
                  if [ "$count" == "0" ]
                  then
                     mv test.js EmployeeService.js
                  else
                    echo "java backend url not update"
                    exit 1
                  fi
                '''
            }
        }
        stage("reacjs docker build "){
            steps{
                sh ''' cd react-frontend/frontend
                       docker build -t malleshdevops/createat-devops-task:react-v11 .
                       docker push malleshdevops/createat-devops-task:react-v11
                '''
            }
        }
        stage("deploy reacjs application with helm"){
            steps{
              sh ''' 
                   cd react-frontend
                   helm upgrade --install devops-reactjs frontend --values frontend/frontend-values-prod.yaml
                   sleep 30s
                   helm status devops-reactjs
                '''
            }
        }
    }
}
