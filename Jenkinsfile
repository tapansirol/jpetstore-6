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
 	appscan application: 'ec9c13f5-8525-494f-ad9a-3025e4527abc', credentials: 'ASOC_Demo', failBuild: true, failureConditions: [failure_condition(failureType: 'high', threshold: 2)], name: 'JPS_test', scanner: static_analyzer('C:\\Users\\kalra_m\\eclipse-workspace-latest\\jpetstore-6'), type: 'Static Analyzer', wait: true   
 }

//  stage ('Tomcat Deploy') {
//  	bat 'copy /Y target\\jpetstore.war C:\\Common\\apache-tomcat-8.5.32_Jenkins\\webapps'
//  }

stage('publish artificats to ucd'){
   step([$class: 'UCDeployPublisher',
        siteName: 'ucd-server',
        component: [
            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
            componentName: 'jenkins-jpet-component',
            createComponent: [
                $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
                componentTemplate: '',
                componentApplication: 'Demo-app'
            ],
            delivery: [
                $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
                pushVersion: 'Ver.${BUILD_NUMBER}',
                baseDir: 'workspace\\Demo-JPetStore\\target',
                fileIncludePatterns: '*.war',
                fileExcludePatterns: '',
               // pushProperties: 'jenkins.server=Jenkins-app\njenkins.reviewed=false',
                pushDescription: 'Pushed from Jenkins'
            ]
        ]
    ])
	step([$class: 'UCDeployPublisher',
        	siteName: 'ucd-server',
        	deploy: [
            	$class: 'com.urbancode.jenkins.plugins.ucdeploy.DeployHelper$DeployBlock',
            	deployApp: 'Demo-app',
            	deployEnv: 'JPetStore_Dev',
            	deployProc: 'Deploy Jenkins',
            	createProcess: [
                	$class: 'com.urbancode.jenkins.plugins.ucdeploy.ProcessHelper$CreateProcessBlock',
                	processComponent: 'Deploy'
            	],
            	deployVersions: 'jenkins-jpet-component:Ver.${BUILD_NUMBER}',
		//deployVersions: 'SNAPSHOT=Base Configuration',
            	deployOnlyChanged: false
        ]
    ])
} 
}
