# Guide to Running Newman Tests in Kubernetes with Azure DevOps

This document serves as a comprehensive guide for running Newman Postman tests in Kubernetes clusters through an Azure DevOps pipeline. The setup leverages Docker, Newman, and Kubernetes to execute API tests.

## Requirements
- Docker installed on the system.
- Access to Azure Container Registry (ACR) and Azure
- Kubernetes Service (AKS).
- Properly configured Azure Service Connection in Azure DevOps.
- Installed Newman globally to run Postman tests.

## Usage
- Modify the parameters in the YAML files to match your environment configurations.
- Ensure Dockerfile and templates are correctly placed in your project directory.
- Trigger the pipeline execution manually or as part of your CI/CD process.


## Code Explanation

```DockerFile
# Use a Node base image
FROM node:latest

# Create a directory to hold the collection and data files
WORKDIR /app

# Install Newman globally
RUN npm install -g newman

# Set the workdir as a volume to allow mounting of files at runtime
VOLUME ["/app"]

# By default, run Newman with no parameters
CMD ["newman"]
```

This Dockerfile sets up a Node.js environment with Newman installed globally. It creates a volume to allow runtime file mounting for collections and data files

```YAML
parameters:
- name: environment
  displayName: ambiente destino
  default: dev
  values: ['dev', 'uat']

trigger: none

stages:  
  - stage: RunNewmanTests
    pool:
        vmImage: 'ubuntu-latest'
    jobs:
      - job: RunTestsInKubernetes
        steps:
          - template: templates/acr-template.yml
            parameters:
              environment: ${{ parameters.environment }}
          - template: templates/aks-template.yml
            parameters:
              environment: ${{ parameters.environment }}
```

This YAML configuration sets up a pipeline with two stages: one for running tests in Azure Container Registry (ACR) and the other for running tests in Azure Kubernetes Service (AKS).
The environment parameter allows selecting the target environment (dev or uat).
The trigger is set to none, meaning this pipeline will only run manually.

```ACR template
parameters:
  - name: environment
    displayName: 'ambiente destino'
    default: dev
    values: ['dev', 'uat', 'prd']

steps:
  - task: Docker@2
    displayName: 'login ACR'
    inputs:
      command: login
      containerRegistry: InsertConnection

  - script: |
      cd devops-utils/
      docker build -t InsertACR.azurecr.io/newman:1.1 newman/templates
      docker push InsertACR.azurecr.io/newman:1.1
    displayName: 'docker build and push'

  - task: Docker@2
    displayName: 'logout ACR'
    inputs:
      command: logout
      containerRegistry: InsertConnection
```
This template logs in to the Azure Container Registry, builds a Docker image for Newman, and pushes it to the registry.

```AKS Template
parameters:
  - name: environment
    displayName: ambiente destino
    default: dev
    values: ['dev', 'uat', 'prd']

steps:
  - ${{ if eq(parameters.environment, 'dev') }}:
      - task: Kubernetes@1
        displayName: 'kubectl login aks ${{ parameters.environment }}'
        inputs:
          connectionType: 'Kubernetes Service Connection'
          kubernetesServiceEndpoint: InsertConnection
          command: 'login'

  - ${{ if eq(parameters.environment, 'uat') }}:
      - task: Kubernetes@1
        displayName: 'kubectl login aks ${{ parameters.environment }}'
        inputs:
          connectionType: 'Azure Resource Manager'
          azureSubscriptionEndpoint: InsertConnection
          azureResourceGroup: InsertResourceGroup
          kubernetesCluster: InsertCluster
          useClusterAdmin: true
          command: 'login'

  - ${{ if eq(parameters.environment, 'prd') }}:
      - task: Kubernetes@1
        displayName: 'kubectl login aks ${{ parameters.environment }}'
        inputs:
          connectionType: 'Kubernetes Service Connection'
          kubernetesServiceEndpoint: InsertConnection
          command: 'login'

  - script: |
        kubectl run newman-aks -n test \
          --image=InsertACR.azurecr.io/newman:1.1 \
          --restart=Never \
          --command -- /bin/sh -c "sleep 3600"
    displayName: 'Create Newman Test Pod'

  # Other steps to copy files, run tests, retrieve reports, and clean up the environment follow...

```

This template logs in to the Azure Kubernetes Service based on the environment selected and runs Newman tests within a Kubernetes pod.