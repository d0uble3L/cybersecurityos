---
title: "Building a Secure DevSecOps Pipeline: Deploying to Amazon ECR with GitHub Actions and Trivy"
date: 2024-12-03
tags: ["DevSecOps", "AWS", "GitHub Actions", "Container Security", "Trivy"]
description: "Learn how to set up a secure CI/CD pipeline to deploy container images to Amazon ECR using GitHub Actions and Trivy."
images:
  - /posts/devsecops/images/devsecops-trivy.webp
featured_image: "/posts/devsecops/images/devsecops-trivy.webp"
---

In today’s rapidly evolving tech landscape, incorporating security into every step of the development lifecycle is essential. [DevSecOps](https://owasp.org/www-project-devsecops/) ensures that security is baked into the process, not bolted on afterward.

This blog post will walk you through setting up a secure [CI/CD pipeline](https://aws.amazon.com/devops/continuous-delivery/) to deploy a container image to [Amazon Elastic Container Registry (ECR)](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html) using [GitHub Actions](https://docs.github.com/en/actions), with vulnerability scanning using [Trivy](https://aquasecurity.github.io/trivy/).

By the end of this guide, you’ll have a secure, automated workflow that builds, scans, and pushes your container images to ECR.

![Trivy](/posts/devsecops/images/trivy-logo.png) <!-- Callout for image -->

---

## Why DevSecOps?

Traditional development pipelines often treat security as an afterthought. This approach leads to costly fixes and vulnerabilities slipping into production. With DevSecOps:

- **Security is integrated**: Vulnerabilities are caught early in the CI/CD pipeline.
- **Automation ensures consistency**: Tools like Trivy automatically enforce security standards.
- **Developers and security teams collaborate**: Security becomes a shared responsibility.

![GitHub Actitons to ECR](/posts/devsecops/images/github-actions-ecr.png) <!-- Callout for image -->

---

## Setting Up the Pipeline

We’ll create a GitHub Actions workflow with the following steps:

1. Checkout the repository.
2. Configure AWS credentials for ECR access.
3. Build the Docker image.
4. Scan the Docker image with Trivy.
5. Push the image to Amazon ECR if it passes the security scan.

### Prerequisites

- An AWS account with permissions to access ECR.
- A GitHub repository.
- Docker installed locally for testing (optional).

---

## Step 1: Configuring AWS and ECR

Before setting up the GitHub Actions workflow:

1. Create an ECR repository in your AWS Management Console.

Here is a awscli command that you can use to create an ecr repo...

first you need to set your environment variables

```bash
export AWS_ACCESS_KEY_ID=<YOUR_ACCESS_KEY_ID>

export AWS_SECRET_ACCESS_KEY=<YOUR_SECRET_ACCESS_KEY>
```

To create a repository in the ECR from the AWS CLI On a machine that has the AWS CLI configured, enter the following to create the repository:

For example:

```bash
 aws ecr create-repository --region eu-west-1 --repository-name node-repo
```

If everything goes well, you should see an output like this:

```bash
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:eu-west-1:XXXXXXXXXX:repository/test",
        "registryId": "790783553687",
        "repositoryName": "test",
        "repositoryUri": "XXXXXXXXXX.dkr.ecr.eu-west-1.amazonaws.com/test",
        "createdAt": "2022-09-28T14:01:20+01:00",
        "imageTagMutability": "MUTABLE",
        "imageScanningConfiguration": {
            "scanOnPush": false
        },
        "encryptionConfiguration": {
            "encryptionType": "AES256"
        }
    }
}
```

2. Store your AWS credentials (`AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`) as **secrets** in your GitHub repository:
   - Go to **Settings > Secrets and Variables > Actions > New repository secret**.
   - Add the credentials with names `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`.

![GitHub Variables](/posts/devsecops/images/github-variables.png) <!-- Callout for image -->

---

## Step 2: Writing the GitHub Actions Workflow

Create a workflow file in your GitHub repository: `.github/workflows/deploy-to-ecr.yml`.

Here’s the complete workflow:

```yaml
name: "Build and Deploy to ECR with Trivy Scanning"

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  BuildAndPushImageToECR:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-west-1 # Adjust to your region

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2

      - name: Build Docker Image
        id: build-image
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          ECR_REPOSITORY: "my-app-repo"
          IMAGE_TAG: "latest"
        run: |
          docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG .
          echo "::set-output name=image::$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG"

      - name: Scan Docker Image with Trivy
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          ECR_REPOSITORY: "my-app-repo"
          IMAGE_TAG: "latest"
        run: |
          docker pull $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG || true
          docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
          aquasec/trivy:latest image --severity HIGH,CRITICAL \
          $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG

      - name: Push Image to Amazon ECR
        env:
          ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
          ECR_REPOSITORY: "my-app-repo"
          IMAGE_TAG: "latest"
        run: |
          docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
```

## Step 3: How It Works

### Step-by-Step Breakdown

1. **Checkout Code**: The `actions/checkout` step fetches the latest code from your repository.
2. **Configure AWS Credentials**: The `aws-actions/configure-aws-credentials` step sets up AWS access using the stored secrets.
3. **Login to Amazon ECR**: The `amazon-ecr-login` action authenticates Docker with your ECR registry.
4. **Build Docker Image**: This step builds your Docker image and tags it for ECR.
5. **Scan with Trivy**: The image is scanned for vulnerabilities. The pipeline will fail if high or critical issues are found.
6. **Push to ECR**: If the scan passes, the image is pushed to ECR.

Here is what the Trivy Output looks like:
![Trivy Output](/posts/devsecops/images/trivy-output.png) <!-- Callout for image -->

---

## Step 4: Demo

### Running the Workflow

1. Commit the `.github/workflows/deploy-to-ecr.yml` file to your repository.
2. Push changes to the `main` branch or open a pull request.
3. Monitor the workflow progress in the **Actions** tab of your repository.

If vulnerabilities are detected, the pipeline will fail at the Trivy scanning step, providing detailed output of the issues.

![GitHub Actions](/posts/devsecops/images/github-actions.png) <!-- Callout for image -->

---

## Wrapping Up

This secure DevSecOps pipeline demonstrates how to integrate security checks with GitHub Actions using Trivy, ensuring vulnerabilities are caught early. By automating the build, scan, and deploy process, your team can deliver secure containerized applications with confidence.

Start integrating security into your pipeline today, and embrace the principles of DevSecOps for a safer development lifecycle.

## Further Reading

- [GitHub Actions Documentation](https://docs.github.com/en/actions)  
  Learn the basics and advanced features of GitHub Actions.

- [Amazon ECR Documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)  
  Understand how to use Amazon Elastic Container Registry (ECR) for managing Docker container images.

- [Trivy Documentation](https://aquasecurity.github.io/trivy/)  
  Official documentation for Trivy, an open-source vulnerability scanner.

- [Introduction to DevSecOps](https://owasp.org/www-project-devsecops/)  
  Explore the principles and practices of integrating security into DevOps workflows.

- [Docker Documentation](https://docs.docker.com/)  
  Comprehensive guide to Docker, including containerization concepts and commands.

- [Best Practices for Building a CI/CD Pipeline](https://aws.amazon.com/devops/continuous-delivery/)  
  Learn how to create robust CI/CD pipelines with security and scalability in mind.

- [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/)  
  Another tool for vulnerability detection in project dependencies.

---

Thanks for reading,

Michael

If you enjoy the content, then consider [buying me a coffee.](https://trilltayo.gumroad.com/coffee)

---

**P.S.** Stay updated on the latest cybersecurity trends and best practices by subscribing to our newsletter or leaving your thoughts in the comments below! [Visit CyberSHIELD](https://cybershieldacademy.net)
