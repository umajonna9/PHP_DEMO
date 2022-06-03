pipeline{
    agent any
        environment{
            IMAGE_NAME='umajonna9/repo1:7.0-apache$BUILD_NUMBER'
            DEV_SERVER_IP='ec2-user@3.87.197.123'
            DEPLOY_SERVER_IP='ec2-user@54.226.193.19'
        }
        stages{
            stage('BUILD DOCKER IMAGE ON DEV SERVER'){
                steps{
                    script{
                        sshagent(['BUILD_SERVER_KEY']) {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
        echo "Building the php image"
        sh "scp -o StrictHostKeyChecking=no -r docker-files ${DEV_SERVER_IP}:/home/ec2-user"
        sh "ssh -o StrictHostKeyChecking=no ${DEV_SERVER_IP} 'bash /home/ec2-user/docker-files/docker-script.sh'"
        sh "ssh ${DEV_SERVER_IP} sudo docker build -t ${IMAGE_NAME} /home/ec2-user/docker-files/"
        sh "ssh ${DEV_SERVER_IP} sudo docker login -u $USERNAME -p $PASSWORD"
        sh "ssh ${DEV_SERVER_IP} sudo docker push ${IMAGE_NAME}"

}
}
                    }
                }
            }
    stage('RUN PHP_APP ON TEST SERVER'){
        steps{
        script{
        sshagent(['BUILD_SERVER_KEY']) {
        withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]){
       echo "RUN THE DOCKER COMPOSE FILES"
       sh "scp -o StrictHostKeyChecking=no -r docker-files ${DEPLOY_SERVER_IP}:/home/ec2-user"
       sh "ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER_IP} 'bash /home/ec2-user/docker-files/docker-script.sh'"
       sh "ssh ${DEPLOY_SERVER_IP} sudo docker login -u $USERNAME -p $PASSWORD"
       sh "ssh ${DEPLOY_SERVER_IP} bash /home/ec2-user/docker-files/docker-compose-script.sh ${IMAGE_NAME}"
       }
       }
                    }
                }
            }
    }
}