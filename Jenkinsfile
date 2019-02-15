node('master') {
    
              notify('Project Build Started')
              
              def AppUrl
              def TerrPath

              try{
              
                             def props_path="props_dir"
                             
                             dir(props_path) {
                             
                                           stage ('Prop Checkout ') {
                                           
                                                          git 'https://github.com/harikrishna515/UtilRepo.git'
                                                          
                                                          def props = readProperties file: 'PropertiesFile.properties'
                                                          
                                                          AppUrl=props.APP_GIT_URL
                           
                                           }
                             }
                             
                             stage('App Checkout ') {
                                          echo "${AppUrl}"
                                           git "${AppUrl}"
                             }
    
                             stage('Code Analysis' ) {
                                           sh 'mvn sonar:sonar'
                             }
                             
                             stage('Build Automation') {    
                                           sh 'mvn clean package'
                             }
                             
                             stage('Build Management'){
                                           def server = Artifactory.newServer url:'http://localhost:8081/artifactory', username: 'admin', password: 'password'
					      def uploadSpec = """{
						 "files": [
						     {
								"pattern": "*.war",
								"target": "lib-staging"
						     }
						  ]
					      }"""
                             }
                             
                             

                             stage('Deployment'){
                               sh 'sudo cp target/*.war /root/Tomcat/apache-tomcat-8.5.37/webapps'
                               sh 'sudo ls -ltr /root/Tomcat/apache-tomcat-8.5.37/webapps'
                             }
                             
              
                             notify('Project Build Completed ')
              
              }
              catch(err) {
                             notify("Error ${err}")
                             currentBuild.result='FAILURE'
              }

}
def notify(status){
    emailext(
        to: "Hari.Garbham@mindtree.com",
        subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
        body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' :</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
        
        )
}
