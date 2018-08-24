node{
  stage ('cloning the repository'){
	git 'https://github.com/DevSecOpsTeam/jpetstore-6'
  }
  stage ('Build') {
	withMaven(jdk: 'JDK_local', maven: 'MVN_Local') {
      bat 'mvn clean package'
    } 
  }
  stage ("running appscan on cloud"){
  	appscan application: '13a06581-eb2c-4b1f-8002-6722126ae44e', credentials: 'ASOC_Staging', failBuild: true, failureConditions: [failure_condition(failureType: 'high', threshold: 1)], name: 'JPS_test', scanner: static_analyzer('C:\\Users\\kalra_m\\eclipse-workspace-latest\\jpetstore-6'), type: 'Static Analyzer', wait: true   
  }
  stage ('Tomcat Deploy') {
  	bat 'copy /Y target\\jpetstore.war C:\\Common\\apache-tomcat-8.5.32_Jenkins\\webapps'
  }
}