node{
  stage ('Cloning the repository'){
	git 'https://github.com/DevSecOpsTeam/jpetstore-6'
  }
  stage ('Build') {
	withMaven(jdk: 'JDK_local', maven: 'MVN_Local') {
      bat 'mvn clean package'
    } 
  }
//Sonarqube static code analysis	
  stage('SonarQube Analysis'){
		def mvnHome = tool name : 'MVN_Local', type:'maven'
		withSonarQubeEnv('sonar-server'){
			bat "mvn sonar:sonar"
		}
}

stage ("Application Security on Cloud"){
	appscan application: '13a06581-eb2c-4b1f-8002-6722126ae44e', credentials: 'ASOC_Staging', failBuild: true, failureConditions: [failure_condition(failureType: 'high', threshold: 4)], name: 'JPS_test', scanner: static_analyzer('C:\\Program Files (x86)\\Jenkins\\workspace\\Demo-JPetStore'), type: 'Static Analyzer', wait: true   
}

//stage ("running appscan on cloud"){
// 	appscan application: '13a06581-eb2c-4b1f-8002-6722126ae44e', credentials: 'ASOC_Staging', failBuild: true, failureConditions: [failure_condition(failureType: 'high', threshold: 4)], name: 'JPS_test', scanner: static_analyzer('C:\\Users\\kalra_m\\eclipse-workspace-latest\\jpetstore-6'), type: 'Static Analyzer', wait: true   
// }

//  stage ('Tomcat Deploy') {
//  	bat 'copy /Y target\\jpetstore.war C:\\Common\\apache-tomcat-8.5.32_Jenkins\\webapps'
//  }

stage('Publish artificats to UrbanCode Deploy'){
   step([$class: 'UCDeployPublisher',
        siteName: 'ucd-server',
        component: [
            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
            componentName: 'jenkins-jpet-component',
            createComponent: [
                $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
                componentTemplate: '',
                componentApplication: 'JPetStore'
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
            	deployProc: 'Deploy-JPetStore',
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
