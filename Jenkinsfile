node('master') {
    
              notify('Project Build Started')
              
              def AppUrl
              def TerrPath
		def ArtifactoryPath
		def UserName
		def Password

              try{
              
                             def props_path="props_dir"
                             
                             dir(props_path) {
                             
                                           stage ('Prop Checkout ') {
                                           
                                                          git 'https://github.com/harikrishna515/UtilRepo.git'
                                                          
                                                          def props = readProperties file: 'PropertiesFile.properties'
                                                          
                                                          AppUrl=props.GIT_URL
						   	ArtifactoryPath =props.Artifactory_ID
						   	UserName=props.username
						   	Password=props.Password
						   TerrPath=props.TerraformPAth
                           
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
                                           def server = Artifactory.newServer url:ArtifactoryPath, username: UserName, password: Password
					      def uploadSpec = """{
						 "files": [
						     {
								"pattern": "*.war",
								"target": "lib-staging"
						     }
						  ]
					      }"""
                             }
                             dir(TerrPath) {
                                           stage('Terraform'){
                                                          sh "sudo /usr/local/bin/terraform apply -auto-approve -var-file=../modulone.tfvars"
                                                          
                                           }
                             }

                             

                             stage('Deployment'){
                               sh 'sudo cp target/*.war /home/devopsuser4/apache-tomcat-8.5.37/webapps'
				sh 'sudo ls -ltr /home/devopsuser4/apache-tomcat-8.5.37/webapps'
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
