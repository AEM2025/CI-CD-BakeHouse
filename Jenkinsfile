pipeline {
    agent { label 'Node-slave'}

    stages {
        stage('Build') {
            steps {

                sh '''
                echo 'Build'
                echo " -----------------------------"
                echo env.GIT_BRANCH
                echo ${BRANCH_NAME} > ../BRANCH.txt
                '''
                
                script {
               if (env.GIT_BRANCH == "release") {

                withCredentials([usernamePassword(credentialsId: 'DockerHub_Cred', usernameVariable: 'USERNAME_Docker', passwordVariable: 'PASSWORD_Docker')]) 
                    {
                 
                        sh '''
                        docker login -u ${USERNAME_Docker} -p ${PASSWORD_Docker}
                        docker build -t ahmedemad2025/bakehouseitismart:v${BUILD_NUMBER} .
                        docker push ahmedemad2025/bakehouseitismart:v${BUILD_NUMBER}
                        echo ${BUILD_NUMBER} > ../build.txt
                        echo ${BRANCH_NAME} > ../BRANCH.txt
                        '''
                    }
                }
            }
        }
}
        stage('Deploy') {
            steps {
                echo 'Deploy'
                script {
                if (env.GIT_BRANCH == "dev" || env.GIT_BRANCH == "test" || env.GIT_BRANCH == "preprod") {
                    withCredentials([file(credentialsId: 'my_KubeConfig', variable: 'KUBECONFIG_ITI')]) {
                        sh ''' 
                         export BUILD_NUMBER=$(cat ../build.txt)
                         mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                         cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                         rm -f Deployment/deploy.yaml.tmp
                         
                         export BRANCH_NAME=$(cat ../BRANCH.txt)
                         mv Deployment/Dev-namespace.yaml Deployment/Dev-namespace.yaml.tmp
                         cat Deployment/Dev-namespace.yaml.tmp | envsubst > Deployment/Dev-namespace.yaml
                         rm -f Deployment/Dev-namespace.yaml.tmp
                         kubectl apply -f Deployment --kubeconfig ${KUBECONFIG_ITI}
                         echo ${BUILD_NUMBER}
                        '''
                    }    
                }
            }
        }

}
 



    }
}
