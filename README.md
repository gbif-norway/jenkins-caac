# Jenkins CaaS Pipelines

This repository contains Jenkins pipeline configurations for Container-as-a-Service (CaaS) using Kubernetes and Kaniko.

## Repository Structure

```
jenkins-caac/
├── pipelines/                    # Pipeline definitions
│   ├── build-on-merge/          # Build pipeline triggered on merge
│   │   ├── Jenkinsfile         # Pipeline definition
│   │   └── README.md           # Pipeline-specific documentation
│   └── [other-pipelines]/      # Other pipeline definitions
├── shared/                      # Shared resources
│   ├── templates/              # Reusable pipeline templates
│   └── scripts/                # Shared scripts
└── README.md                   # This file
```

## Available Pipelines

### Build on Merge Pipeline
Located in `pipelines/build-on-merge/`
- Triggers on GitHub webhook events (push to main branch)
- Uses Kaniko for building containers in Kubernetes
- Pushes built images to a container registry

## Prerequisites

1. Jenkins with Kubernetes plugin installed
2. Kubernetes cluster with proper permissions
3. Jenkins credentials configured for Docker registry access
4. GitHub webhook secret configured in Jenkins

## Setup

1. Configure Jenkins Credentials:

   a. Go to Jenkins Dashboard > Manage Jenkins > Credentials > System > Global credentials
   
   b. Add Docker Registry credentials:
   - Kind: Username with password
   - ID: docker-registry-credentials
   - Username: your-registry-username
   - Password: your-registry-password
   - Description: Docker Registry Credentials

   c. Add GitHub Webhook Secret:
   - Kind: Secret text
   - ID: github-webhook-secret
   - Secret: your-webhook-secret
   - Description: GitHub Webhook Secret

2. Configure the GitHub webhook in your repository:
   - URL: `https://your-jenkins-url/github-webhook/`
   - Content type: `application/json`
   - Secret: Use the same secret as configured in Jenkins

3. Apply the pipeline configuration:
```bash
kubectl apply -f pipelines/build-on-merge/job.yaml
```

## Adding New Pipelines

To add a new pipeline:
1. Create a new directory under `pipelines/`
2. Add your pipeline-specific files (Jenkinsfile, job.yaml, README.md)
3. Update this README.md to document the new pipeline

## Pipeline Configuration

The main pipeline (`build-pipeline.yaml`) is configured to:
- Trigger on GitHub webhook events (push to main branch)
- Use Kaniko for building containers in Kubernetes
- Push built images to a container registry

## Pipeline Structure

- `Jenkinsfile`: Main pipeline definition
- `jobs/build-pipeline.yaml`: Jenkins job configuration with GitHub webhook trigger

## Pipeline Parameters

The pipeline accepts the following parameters:
- `DOCKER_REGISTRY`: Container registry URL (default: ghcr.io)
- `GITHUB_WEBHOOK_SECRET`: GitHub webhook secret

These can be configured in the Jenkins job configuration page or passed during job execution.