End-to-End CI/CD Pipeline with Jenkins, Docker, and AWS ECS

This project demonstrates a production-style CI/CD pipeline that automates the build, test, code analysis, containerization, and deployment of a Java web application to Amazon ECS.
The pipeline ensures reliable, repeatable, and version-controlled deployments using Jenkins and AWS services.
________________________________________
📊 Architecture
GitHub → Jenkins → Maven → SonarQube → Docker → ECR → ECS → Users
________________________________________
# Prerequisites
- JDK 21 
- Maven 3.9 
- MySQL 8
________________________________________
🧰 Tech Stack
•	Jenkins (CI/CD automation)
•	Maven (Build & dependency management)
•	Docker (Containerization)
•	Amazon ECR (Container registry)
•	Amazon ECS (Container orchestration)
•	SonarQube (Code quality analysis)
•	GitHub (Source control)
________________________________________
⚙️ CI/CD Pipeline Workflow
1.	Checkout code from GitHub
2.	Build application using Maven
3.	Run unit tests
4.	Perform code quality analysis (Checkstyle + SonarQube)
5.	Build Docker image
6.	Push image to Amazon ECR
7.	Update ECS task definition with new image version
8.	Deploy application to ECS service
________________________________________
🐳 Docker Image Versioning
Each build is tagged using the Jenkins build number:
Example:
vprofileappimg:16
Benefits:
•	Traceable deployments
•	Easy rollback
•	Avoids risks of using latest
________________________________________
☁️ ECS Deployment Strategy
•	Task definitions are dynamically updated using Jenkins
•	New revisions are registered automatically
•	ECS performs rolling deployments with zero downtime
•	Each deployment uses a specific image version
________________________________________
📊 Code Quality
•	Checkstyle enforces coding standards
•	SonarQube provides static code analysis
•	Reports are generated during each pipeline run
________________________________________
🧠 Key Features
•	End-to-end CI/CD automation
•	Version-controlled deployments
•	AWS cloud-native architecture
•	Integrated code quality checks
•	Automated ECS deployment updates
________________________________________
🚀 Future Improvements
•	Blue/Green deployments in ECS
•	Infrastructure provisioning using Terraform
•	Kubernetes (EKS) deployment with Helm
•	GitHub Actions pipeline implementation

________________________________________
👩🏽‍💻 Author
Tina Collins

