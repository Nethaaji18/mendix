pipeline {
    agent any
    environment {
        GIT_USERNAME = 'nethaaji.madhanmohan@rapiddatatech.com'
        GIT_PASSWORD = credentials('PAT_TOKEN')
        ENDPOINT = credentials('DATABASE_ENDPOINT')
    }
    stages {
        stage('delete_buildpack'){
            steps{
                script{
                    sh """
                    rm -rv docker-mendix-buildpack
                    
                    """
                }
            }
        }
        stage('application pull') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'mendix', passwordVariable: "${GIT_PASSWORD}", usernameVariable: "${GIT_USERNAME}")]) {
                        // Get some code from a GitHub repository
                        sh """
                        git config --global credential.username "\${GIT_USERNAME}"
                        git config --global credential.helper "!echo password=\${GIT_PASSWORD}; echo"
                        git clone https://git.api.mendix.com/dd390624-b804-4bff-8671-247f64bb0f99.git
                        echo "Pulled the code"
                        """
                    }
                }
            }
        }
        stage('buildpack pull') {
            steps {
                script {
                    // Copy a file from the workspace to /home/ubuntu/downloads
                    //sh "mv dd390624-b804-4bff-8671-247f64bb0f99 /home/ubuntu/downloads/docker-mendix-buildpack"
                    sh "git clone https://github.com/mendix/docker-mendix-buildpack.git"
                }
            }
        }
        stage('move'){
            steps{
                script{
                    sh "mv /var/lib/jenkins/workspace/mendix/dd390624-b804-4bff-8671-247f64bb0f99 /var/lib/jenkins/workspace/mendix/docker-mendix-buildpack"
                }
            }
        }
        stage('build'){
            steps{
                script{
                    sh """
                    cd /var/lib/jenkins/workspace/mendix/docker-mendix-buildpack
                    docker build --build-arg BUILD_PATH=dd390624-b804-4bff-8671-247f64bb0f99 -t nethaaji/demoapp:${BUILD_NUMBER} .
                    """
                }
            }
        }
        stage('push'){
            steps{
                script{
                    sh"""
                    docker login -u nethaaji -p 123456789
                    docker push nethaaji/demoapp:${BUILD_NUMBER} 
                    docker logout
                    """
                }
            }
        }
        stage('docker run'){
            steps{
                script{
                    sh"""
                    docker login -u nethaaji -p 123456789
                    docker stop my-mendix-container
                    docker rm my-mendix-container
                    git clone https://github.com/Nethaaji18/mendix.git
                    cd /var/lib/jenkins/workspace/mendix/mendix
                    docker compose -f mendix-compose.yml up -d
                    """
                }
            }
        }
    }
}
