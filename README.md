## Implemented an Automated CICD Pipeline for YouTube Clone Application (React JS)

In this project, we embark on the deployment of a YouTube-Clone-App with a comprehensive CI/CD pipeline leveraging key tools used in DevOps. 

- ***Git:*** serves as the backbone for version control, tracking and storing the source code. 
- ***Jenkins:*** CI/CD orchestrator, diligently monitors the Git repository, triggering an automated sequence via webhooks upon any code changes.
- ***SonarQube:*** Code quality is scrutinized through *SonarQube*, ensuring strict adherence to coding standards. 
- ***Docker:*** steps in to containerize the application. Docker images are pushed to a container registry *DockerHub* becoming ready for deployment
- ***Trivy:*** scans the resulting image, ensuring it's free from vulnerabilities.
- ***OWASP Checks:*** The project emphasizes security with the integration of OWASP checks, fortifying the application against potential threats.
- ***Argocd:*** Finally, ArgoCD is introduced to the pipeline, streamlining and automating the deployment of applications in Kubernetes clusters.

## Branching Strategy
I've created two branches *main* and *staging*. When Engineers/Developers push changes to *staging branch* then the owner of the *main branch* reviews and merges the changes into main branch by raising a *Pull Request*.

- To trigger the jenkins job, I'have configured a webhook that sends a POST request to Jenkins server whenever a pull request is opened.
- In Jenkins, I'have installed and set up the **Generic Webhook Trigger** plugin, specifying the payload format that the plugin should expect when a pull request webhook is received.
- Have set *conditions* in Jenkins job to specify when it should be triggered, such as targeting a specific branch.
- When the owner of the main branch  opens a PR, the webhook sends a POST request to Jenkins. The Generic Webhook Trigger plugin then processes the payload and checks conditions. If the conditions are met, the Jenkins job is triggered and executes configured steps & other tasks.

This setup allows for automated building, testing and deploying ans other tasks to be taken whenever a pull request is opened. It helps ensure that changes introduced in the staging branch are tested before they are merged into the main branch, promoting a more reliable and stable codebase.

