pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'github', 
                url: 'https://github.com/ShubhamSoni-DevOps/cicd-end-to-end.git',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t shubhamsonirg/cicd-e2e:${BUILD_NUMBER} .
                    docker image tag shubhamsonirg/cicd-e2e:${BUILD_NUMBER} shubhamsonirg/cicd-e2e:latest
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
                script{
                    sh '''
                    echo 'Push to Repo'
                    docker push shubhamsonirg/cicd-e2e:${BUILD_NUMBER}
                    docker push shubhamsonirg/cicd-e2e:latest
                    '''
                }
            }
        }
        stage('Removing the Unused Images'){
            steps{
                script{
                    sh '''
                    echo "Removing the images"
                    docker rmi shubhamsonirg/cicd-e2e:${BUILD_NUMBER}
                    docker rmi shubhamsonirg/cicd-e2e:latest
                    '''
                }
            }
        }
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'github', 
                url: 'https://github.com/ShubhamSoni-DevOps/cicd-demo-manifests-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i "s/6/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/ShubhamSoni-DevOps/cicd-demo-manifests-repo.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
