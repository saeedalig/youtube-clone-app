
//Single Repo for both CI and CD

pipeline {
    agent any

    tools{
        jdk 'jdk17'
        nodejs 'node16'
    }
	
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKERHUB_USERNAME = "asa96"
        APP_NAME = "youtube"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/" + "${APP_NAME}"
        REGISTRY_CREDS = "dockerhub-auth"
    }
				
    stages {
	
        stage('Cleanup Workspace'){
            steps {
                script {
                    cleanWs()
                }
            }
        }
		
        stage('Checkout SCM'){
            steps {
                git credentialsId: 'github-auth', 
                url: 'https://github.com/saeedalig/youtube-clone-app.git',
                branch: 'main'
            }
        }

        // stage('Code Analysis'){
        //     steps{
        //         withSonarQubeEnv('SonarCloud') {
        //             sh ''' $SCANNER_HOME/bin/sonar-scanner \
		// 			-Dsonar.organization=devopsas \
		// 			-Dsonar.java.binaries=.\
		// 			-Dsonar.projectKey=youtube \
		// 			-Dsonar.sources=. \
        //             -Dsonar.host.url=https://sonarcloud.io
		// 			'''
        //         }
        //     }
        // }

        // stage("Quality Gate Status"){
        //    steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'sonar' 
        //         }
        //     } 
        // }
		

        stage("Docker Build & Tag"){
            steps{
                script{

                    sh "docker build --build-arg REACT_APP_RAPID_API_KEY=252f61132fmshde283ead7250c26p109475jsna4c8a163b047 -t ${APP_NAME} ."
                    sh "docker tag youtube ${APP_NAME}:${IMAGE_TAG} "
                    sh "docker tag youtube ${APP_NAME}:latest "
                    
                }
            }
        }

        stage('Docker Push') {

            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-auth', passwordVariable: 'PASSWD', usernameVariable: 'USER')]) {
                    sh "docker login -u ${env.USER} -p ${env.PASSWD}"
                    sh "docker push ${APP_NAME}:${IMAGE_TAG}"
                    sh "docker push ${APP_NAME}:latest"
                }
            }
        }

		
        // stage('Push Docker Image'){
        //     steps {
        //         script{
        //             docker.withRegistry('', REGISTRY_CREDS ){
        //                 docker_image.push("${BUILD_NUMBER}")
        //                 docker_image.push('latest')
        //             }
        //         }
        //     }
        // } 
		
        // stage('Delete Docker Images'){
        //     steps {
        //         sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
        //         sh "docker rmi ${IMAGE_NAME}:latest"
        //     }
        // }
		
		// stage('Update k8s deployment file'){
        //     steps {
        //         sh "cat deployment.yaml"
        //         sh "sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml"                
        //         sh "cat deployment.yaml"
        //     }
        // }
        
        // stage('Push the changed deployment file to GitHub') {
        //     steps {
        //         script {
        //             sh """
        //             git config --global user.name "saeed"
        //             git config --global user.email "saeed@gmail.com"
        //             git add deployment.yaml
        //             git commit -m 'Updated the deployment file'
        //             """
        //             withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
        //                 sh 'git remote set-url origin https://$USER:$PASS@github.com/saeedalig/k8s-manifest.git'
        //                 sh 'git push origin main'
        //             }
        //         }
        //     }
        // }
    }
}