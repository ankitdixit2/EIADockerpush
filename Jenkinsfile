import groovy.json.JsonSlurper

def notifySlack(String buildStatus = 'STARTED') {
    //  Build  status of null means successs....
    buildStatus = buildStatus ?: 'SUCCESS'
    
    def msg = "${buildStatus}: `${env.JOB_NAME}` #${env.BUILD_NUMBER}:-Microservice image has been created and pushed into Docker registry by ${env.USER}"
//   Import Testing  
    slackSend(message: msg)
    userNotification(buildStatus)
}
def userNotification(String buildStatus)
{ 
    if(buildStatus.equals('SUCCESS'))
    {
    def token ='xoxb-318958501572-402684909218-YW8RZZeh6ZXEkjSM8rxEIDzT'
    def userName='test_eia'
    def userList
    def userId
    def channelId
    def get 
    def body
     get = new URL('https://slack.com/api/users.list?token=' + token).openConnection()
    if(get.getResponseCode().equals(200)) {
     body=get.getInputStream().getText()
        userList = new JsonSlurper().parseText(body)
        println("UserList success");
                   
                      for ( i = 0; i < userList.members.size(); i++) {
                            if (userList.members[i].name == userName) {
                                userId = userList.members[i].id
                                println("Got id")
                                break
                            }
                        }
    
        get = new URL(' https://slack.com/api/im.open?token=' + token + '&user=' + userId).openConnection()
        if(get.getResponseCode().equals(200)) {
        body=get.getInputStream().getText()
        channelId = new JsonSlurper().parseText(body).channel.id
        println(channelId)
        println(1)
        def string='Microservice image is created and pushed into Docker registry, please type #vmCreate to start further.'
        string =URLEncoder.encode(string, "UTF-8")
        get = new URL('https://slack.com/api/chat.postMessage?token=' + token + '&channel=' + channelId +'&text='+string+'&as_user=false&username=devOpsBot').openConnection()
        
        body=get.getInputStream().getText()
        println(body)
        println(2)       
        }
    }
    }
}
node {
    checkout scm
    try {
    stage('build') {
        /* Test docker  compose  */
      //      sh "docker-compose build"
      //      sh "docker tag mysqlnodejsmicroservice_db ankurdixit82/mysqlnodejsmicroservice_db:latest"
     //       sh "docker tag mysqlnodejsmicroservice_users-service ankurdixit82/mysqlnodejsmicroservice_users-service:latest"
         //    sh "docker login -u ****** -p *****"     
             sh "docker build -t users_service ./users-service"
             sh "docker build -t eiadatabase ./eiadatabase"
             sh "docker tag users_service 52.76.36.139:5000/docker_users-service:latest"
             sh "docker tag eiadatabase 52.76.36.139:5000/docker_db:latest"
             sh "docker push 52.76.36.139:5000/docker_users-service:latest"
             sh "docker push 52.76.36.139:5000/docker_db:latest" 
             sh "docker image remove users_service"
             sh "docker image remove eiadatabase"
             sh "docker image remove 52.76.36.139:5000/docker_users-service:latest"
             sh "docker image remove 52.76.36.139:5000/docker_db:latest"
        
    }
        currentBuild.result = "SUCCESS"
        notifySlack(currentBuild.result)
       
  } catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    //throw ef
    notifySlack(currentBuild.result)
  }    
}
