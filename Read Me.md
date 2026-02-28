# DevOps Automated Deployment (macOS)

This project demonstrates a complete **DevOps automation workflow** using cloud infrastructure, configuration management, containerization, and CI/CD.

The setup provisions an EC2 server automatically, installs Docker, deploys a containerized web application, and enables continuous deployment using GitHub Actions.


---

## Project Structure


.
├── terraform/
│ └── main.tf
├── ansible/
│ ├── inventory
│ └── install_docker.yml
├── app/
│ ├── Dockerfile
│ └── index.html
├── .github/
│ └── workflows/
│ └── deploy.yml
└── README.md


---

## Project Workflow Overview

1. Terraform creates AWS infrastructure (EC2 + Security Group)
2. Docker is installed automatically on the server
3. Application is containerized using Docker
4. GitHub Actions builds and pushes Docker image
5. EC2 pulls the latest image and runs the container
6. Application is accessible via EC2 public IP on port **80**

---

## SSH Key Setup (AWS Generated – macOS)

- SSH key pair was created **from AWS Console**
- Private key (`Assignment_Key.pem`) was downloaded once
- Key was moved to macOS SSH directory:

```bash
mv ~/Downloads/Assignment_Key.pem ~/.ssh/
chmod 400 ~/.ssh/Assignment_Key.pem

AWS stores the public key automatically on the EC2 instance

SSH Command
ssh -i ~/.ssh/Assignment_Key.pem ubuntu@<PUBLIC_IP>
🏗️ Infrastructure Provisioning (Terraform)
Initialize and Apply Terraform
cd terraform
terraform init
terraform apply

Terraform automatically:

Creates EC2 instance

Creates security group (ports 22 & 80)

Installs Docker using user_data

Outputs the public IP dynamically

No IP addresses are hard-coded.

Server Configuration (Ansible)
Inventory File
[servers]
<PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/Assignment_Key.pem
Run Playbook
ansible-playbook -i inventory install_docker.yml

Ansible ensures Docker is installed and running.

Application Containerization (Docker)
Dockerfile
FROM nginx:latest
COPY index.html /usr/share/nginx/html/index.html

Docker packages the application into a portable container.

CI/CD Pipeline (GitHub Actions)
Pipeline Behavior

Triggered on push to main branch

Builds Docker image

Pushes image to Docker Hub

SSHs into EC2

Pulls latest image

Runs container on port 80

Required GitHub Secrets

Add these in Repository → Settings → Secrets and variables → Actions:

Secret Name	Description
DOCKER_USERNAME	Docker Hub username
DOCKER_PASSWORD	Docker Hub password / token
EC2_PUBLIC_IP	Terraform output public IP
EC2_SSH_KEY	Content of Assignment_Key.pem
Accessing the Application

Once deployed, open in browser:

http://<PUBLIC_IP>
Why This Project Matters

No manual server setup

Fully automated infrastructure

Reproducible deployments

Real-world DevOps workflow

Core Learning:
Terraform provisions infrastructure, Ansible configures the server, Docker runs the application, and GitHub Actions automates deployment using a CI/CD pipeline.