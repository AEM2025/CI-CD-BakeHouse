pipeline {
    agent { label 'Node-slave'}

    stages {
        stage('Build') {
            steps {
                echo 'Build'
                echo " -----------------------------"
                echo env.GIT_BRANCH 

                script {
               if (env.GIT_BRANCH == "Release") {

                withCredentials([usernamePassword(credentialsId: 'DockerHub_Cred', usernameVariable: 'USERNAME_Docker', passwordVariable: 'PASSWORD_Docker')]) 
                    {
                 
                        sh '''
                        docker login -u ${USERNAME_Docker} -p ${PASSWORD_Docker}
                        docker build -t ahmedemad2025/bakehouseitismart:v${BUILD_NUMBER} .
                        docker push ahmedemad2025/bakehouseitismart:v${BUILD_NUMBER}
                        echo ${BUILD_NUMBER} > ../build.txt
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
                if (env.GIT_BRANCH == "Dev" || env.GIT_BRANCH == "Test" || env.GIT_BRANCH == "Preprod") {
                    withCredentials([file(credentialsId: 'my_KubeConfig', variable: 'KUBECONFIG_ITI')]) {
                        sh ''' 
                         export BUILD_NUMBER=$(cat ../build.txt)
                         mv Deployment/deploy.yaml Deployment/deploy.yaml.tmp
                         cat Deployment/deploy.yaml.tmp | envsubst > Deployment/deploy.yaml
                         rm -f Deployment/deploy.yaml.tmp
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
