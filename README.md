## Implemented an Automated CICD Pipeline for YouTube Clone Application (React JS)

In this project, we embark on the deployment of a YouTube-Clone-App with a comprehensive CI/CD pipeline leveraging key tools used in DevOps. 

- ***Git:*** serves as the backbone for version control, tracking and storing the source code. 
- ***Jenkins:*** CI/CD orchestrator, diligently monitors the Git repository, triggering an automated sequence via webhooks upon any code changes.
- ***SonarQube:*** Code quality is scrutinized through *SonarQube*, ensuring strict adherence to coding standards. 
- ***Docker:*** steps in to containerize the application. Docker images are pushed to a container registry *DockerHub* becoming ready for deployment
- ***Trivy:*** scans the resulting image, ensuring it's free from vulnerabilities.
- ***OWASP Checks:*** The project emphasizes security with the integration of OWASP checks, fortifying the application against potential threats.
- ***Argocd:*** Finally, ArgoCD is introduced to the pipeline, streamlining and automating the deployment of applications in Kubernetes clusters.

![Alt text](pics/img-1.png)


## Branching Strategy
I've created two branches *main* and *staging*. When Engineers/Developers push changes to *staging branch* then the owner of the *main branch* reviews and merges the changes into main branch by raising a *Pull Request*.

- To trigger the jenkins job, I'have configured a webhook that sends a POST request to Jenkins server whenever a pull request is opened.
- In Jenkins, I'have installed and set up the **Generic Webhook Trigger** plugin, specifying the payload format that the plugin should expect when a pull request webhook is received.
- Have set *conditions* in Jenkins job to specify when it should be triggered, such as targeting a specific branch.
- When the owner of the main branch  opens a PR, the webhook sends a POST request to Jenkins. The Generic Webhook Trigger plugin then processes the payload and checks conditions. If the conditions are met, the Jenkins job is triggered and executes configured steps & other tasks.

This setup allows for automated building, testing and deploying ans other tasks to be taken whenever a pull request is opened. It helps ensure that changes introduced in the staging branch are tested before they are merged into the main branch, promoting a more reliable and stable codebase.

## Server SetUp
**Jenkins**
```
# Install Java
sudo apt install openjdk-17-jdk -y

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y

sudo systemctl enable jenkins
sudo systemctl restart jenkins

# Password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

**Docker**
```
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER  
newgrp docker
sudo chmod 777 /var/run/docker.sock
```
**Trivy**
```
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update -y
sudo apt-get install trivy -y
```
**Sonarqube**
```
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
username and password: `admin`
```
**Argocd**
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Service
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'
kubectl port-forward svc/argocd-server -n argocd 8085:443

# passwd
argocd admin initial-password -n argocd
```

