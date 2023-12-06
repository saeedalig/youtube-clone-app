
//Single Repo for both CI and CD

def COLOR_MAP = [
    'FAILURE' : 'danger',
    'SUCCESS' : 'good'
]


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

        stage('Code Analysis'){
            steps{
                withSonarQubeEnv('SonarCloud') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner \
					-Dsonar.organization=devopsas \
					-Dsonar.java.binaries=.\
					-Dsonar.projectKey=youtube \
					-Dsonar.sources=. \
                    -Dsonar.host.url=https://sonarcloud.io
					'''
                }
            }
        }

        stage("Quality Gate Status"){
           steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar' 
                }
            } 
        }
		
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }

        stage('OWASP FS Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('TRIVY FS Scan') {
            steps {
                sh "trivy fs . > trivy-fs.txt"
            }
        }

        stage("Docker Build & Tag"){
            steps{
                script{

                    sh "docker build --build-arg REACT_APP_RAPID_API_KEY=252f61132fmshde283ead7250c26p109475jsna4c8a163b047 -t ${APP_NAME} ."
                    sh "docker tag ${APP_NAME} ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker tag ${APP_NAME} ${IMAGE_NAME}:latest"
                    
                }
            }
        }

        stage("TRIVY Image Scan"){
            steps{
                sh "trivy image asa96/youtube:latest > trivy-image.txt" 
            }
        }

        stage('Docker Push') {

            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-auth', passwordVariable: 'PASSWD', usernameVariable: 'USER')]) {
                    sh "docker login -u ${env.USER} -p ${env.PASSWD}"
                    sh "docker push ${DOCKERHUB_USERNAME}/${APP_NAME}:${IMAGE_TAG}"
                    sh "docker push ${DOCKERHUB_USERNAME}/${APP_NAME}:latest"
                }
            }
        }

		
        stage('Delete Docker Images'){
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker rmi ${IMAGE_NAME}:latest"
            }
        }

        stage('Update k8s deployment file') {
            sh "cat deployment.yml"
            sh "sed -i \"s|\\(image:.*${APP_NAME}\\).*|\\1:${IMAGE_TAG}|\" deployment.yml"
            sh "cat deployment.yml"

        }

        stage('Push the changed deployment file to GitHub') {
            steps {
                script {
                    sh """
                    git config --global user.name "zeal"
                    git config --global user.email "zeal@gmail.com"
                    git add deployment.yml
                    git commit -m 'Updated the deployment file'
                    """
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh 'git remote set-url origin https://$USER:$PASS@github.com/saeedalig/youtube-clone-app.git'
                        sh 'git push origin main'
                    }
                }
            }
        }

        post {
            always {
                echo 'Slack Notifications'
                slackSend (
                    channel: '#jenkins',   // Change this to your actual channel name
                    color: COLOR_MAP[currentBuild.currentResult],
                    message: "*${currentBuild.currentResult}:* \n Job: ${env.JOB_NAME} \n Build: #${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
                )
            }
        }
    }
}
