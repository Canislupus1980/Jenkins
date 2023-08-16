pipeline {
    tools {
        jdk 'OpenJDK'
        maven 'Maven-3.9.4'
    }
    agent {
        label "master"
    }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
    }  
    environment {
        JOB_NAME = sh(script: "echo ${JOB_NAME} | cut -d'/' -f 1", returnStdout: true).trim()
    }
    parameters{
        choice(
            name: 'SERVICE', 
            choices: [
                '*'
            ], 
            description: 'Set Service'
        )
        choice(name: 'CONTOUR', choices: ['dev'], description: 'Set Contour')
    }    
    stages {
        stage ("Start") {
            stages {
                stage ('Notification') {
                    steps {
                        script{
                            wrap([$class: 'BuildUser']) {
                            env.JOB_USER = sh(script: 'echo "${BUILD_USER}"', returnStdout: true).trim()
                            }
                        }
                        // sh "telegramBot* 'YELLOW' 'Start' 'Build' '${JOB_NAME}' '${BRANCH}|${JOB_USER}|${RUN_DISPLAY_URL}' '${CONTOUR}'"
                    }
                }
                stage('SCM') {
                    steps {
                        git changelog: false, credentialsId: '*', poll: false, url: '*'
                    }
                }               
                stage('Maven Build') {
                    steps {
                        sh "chmod +x mvnw"
                        sh "./mvnw -B clean install -Dmaven.test.skip=true"
                    }
                }
                // stage('Docker Build & Push') {
                //     steps {
                //         script {
                //             withDockerRegistry(url: '*') {
                //                 sh "docker build -t *:${TAG_BUILD} ."
                //                 sh "docker push *:${TAG_BUILD}"
                //             }
                //         }
                //     }
                //     post { 
                //         always {                           
          		// 		    cleanWs()
                //         }                                                       
                //     }       
                // }                                                                                                      
            }                                                                               
        
            // post {
            //     success {                
            //         sh "telegramBot* 'GREEN' 'success' 'Build' '${JOB_NAME}' '${BRANCH}|${JOB_USER}|${RUN_DISPLAY_URL}' '${CONTOUR}'"            
            //     }                      
	        //     failure {
	        //         sh "telegramBot* 'RED' 'Failed' 'Build' '${JOB_NAME}' '${BRANCH}|${JOB_USER}|${RUN_DISPLAY_URL}' '${CONTOUR}'"
	        //     }
            // }            
        }
        // stage ("Deploy") {
        //     steps {
        //         //updateGitlabCommitStatus name: "Deploy-Dev", state: "running"
        //         build job: 'Deploy-*', parameters: [
        //             string(name: 'SERVICE', value: "*"), 
        //             string(name: 'CONTOUR', value: "${CONTOUR}"),
        //             string(name: 'BRANCH_BUILD', value: "${BRANCH}") 
        //         ]
        //     }                                  
        // }            
    }   
}
