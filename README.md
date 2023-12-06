## Implemented an Automated CICD Pipeline for YouTube Clone Application (React JS)

In this project, we embark on the deployment of a YouTube-Clone-App with a comprehensive CI/CD pipeline leveraging key tools used in DevOps. 

- ***Git:*** serves as the backbone for version control, tracking and storing the source code. 
- ***Jenkins:*** CI/CD orchestrator, diligently monitors the Git repository, triggering an automated sequence via webhooks upon any code changes.
- ***SonarQube:*** The application undergoes a seamless process within the pipeline: it is built, dependencies are installed, and production-ready bundles are generated. Code quality is scrutinized through *SonarQube*, ensuring strict adherence to coding standards. 
- ***Docker:*** steps in to containerize the application. Docker images are pushed to a container registry *DockerHub* becoming ready for deployment
- ***Trivy:*** scans the resulting image, ensuring it's free from vulnerabilities.
- ***OWASP Checks:*** The project emphasizes security with the integration of OWASP checks, fortifying the application against potential threats.
- ***Argocd:*** Finally, ArgoCD is introduced to the pipeline, streamlining and automating the deployment of applications in Kubernetes clusters. 

